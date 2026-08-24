---
title: "HDD Corruption"

date: 2026-08-24T14:57:37-04:00
url: /HDD-Corruption/
image: /images/2020-thumbs/HDD-Corruption.jpg

draft: false
---

A recent look at dmesg drove me to find ext4/io errors
<!--more-->

I decided to give a write up of a problem here rather than update the ticketing system with what is going on and how I fixed it. I think this will serve as a good basis of how my brain works and somewhere to start from if you were to have similar problems.


Went to bring up a compose stack today and got hit with the classic:

```
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```

which wouldn't have been a problem as I usually have them turned off by default. However it would not turn on now.

```
systemctl status docker
```

Failed. Restart loop, actually — "Start request repeated too quickly," which meant it had already crashed a few times before I even noticed. Digging into the actual error from `journalctl`:

```
failed to start daemon: readdirnames /var/lib/docker/runtimes: readdirent runtimes: bad message
```

**"bad message"** is not a Docker error I'd seen before. Turns out that's actually a raw `EBADMSG` bubbling up from the kernel through a syscall, not something Docker itself is complaining about. Which means something below Docker, at the filesystem level, is unhappy.

**What is 4065911?**

Went to `dmesg` on the container to see if there was more context, and there it was, over and over:

```
EXT4-fs error (device dm-4): ext4_empty_dir:3142: inode #4065911: comm dockerd: Directory block failed checksum
EXT4-fs error (device dm-4): htree_dirblock_to_tree:1083: inode #4065911: comm dockerd: Directory block failed checksum
```

Same inode, same error, going back — and this is the part that stung a little — to a timestamp translating back several *months*. "error count since last fsck: 30" by the time I actually looked at it. It had been quietly failing every time dockerd tried to read that directory, and nothing ever surfaced it to me until Docker itself finally choked hard enough to stay down.

Inode `4065911` turned out to be `/var/lib/docker/runtimes` — not exactly a critical directory (it's just where alt OCI runtime configs like nvidia-container-runtime would live), but its directory block had failed its checksum, which meant `readdir()` on it was returning garbage instead of entries. Docker just couldn't run at this point trying to enumerate it.

**The container-vs-host**

First instinct was to just fsck it from inside the Jellyfin LXC. Immediate wall: `dmsetup`, `pvs`, `lvs`, `smartctl` — none of it exists inside an LXC container, because device-mapper ioctls are blocked in the container's namespace by design. Containers see a filesystem, not a block device. That `dm-4` I was staring at in `dmesg` wasn't even *this* container's view of the world — it was the **host's** kernel ring buffer, since LXC shares the host kernel.

```
rootfs: TBNiceGuy:vm-105-disk-0,size=11103G
```

`TBNiceGuy` is a LVM volume group sitting on a single 10.9T disk (`sdb`)

```
vm-105-disk-0 TBNiceGuy -wi-ao---- 10.84t
```

**Running fsck**

Stopped the container (`pct stop 105`), which deactivated the LV and made its device node vanish from `/dev/mapper/` entirely. Had to bring it back manually:

```
lvchange -ay /dev/TBNiceGuy/vm-105-disk-0
fsck -f -y /dev/mapper/TBNiceGuy-vm--105--disk--0
```

11TB fsck on ext4, so I expected to wait a while. Pass 1 was mostly noise — a wall of "extent tree could be narrower/shorter, optimize?" which is just ext4 cleaning up its own metadata trees, nothing to do with the corruption. The actual fix showed up in Pass 2 and Pass 3:

```
Invalid inode number for '.' in directory inode 4065911. Fix? yes
Entry '..' in ??? (4065911) has an incorrect filetype (was 2, should be 1). Fix? yes
'..' in /var/lib/docker/runtimes (4065911) is <4064457> (4064457), should be /var/lib/docker (3935345). Fix? yes
```

There it was — same inode number I'd chased down from the kernel logs. The `runtimes` directory's `..` pointer had gotten scrambled at some point (bad shutdown, bit rot, who knows), which explains months of quiet checksum failures that nothing ever acted on until dockerd's `readdirnames` finally choked outright. Pass 4 cleaned up the resulting reference count mismatches on the parent directories, Pass 5 fixed a block bitmap discrepancy, and:

```
***** FILE SYSTEM WAS MODIFIED *****
```

No `lost+found` entries, nothing orphaned — just a clean structural repair of one bad directory link.

**Back up**

```
lvchange -an /dev/TBNiceGuy/vm-105-disk-0
pct start 105
```

and inside the container, `systemctl start docker` came up clean on the first try.

**Takeaways**

The thing that bugs me most here is that this had been happening since way back — 30+ logged error events before it actually took Docker down. There was no alerting on kernel-level ext4 errors, so it just sat there degrading silently until it hit something that mattered. The SMART system is Proxmox is a bit suspect to me now and I will have to do some investigation into how to better track errors like making api calls or maybe even a daily run of dmesg or something that'll send me a discord alert of the tasks. I am planning to migrate this and a few other drives into a larger storage pool so hopefully I will have some sort of solution by then. If the disk is starting to go (which I have a hunch may be the culprit here) I'll have to migrate sooner rather than later to save the files.

Nothing really fancy just a webhook that will watch dmesg for ext4/io errors and send a notification of all good or all bad.