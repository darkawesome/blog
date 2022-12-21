---
title: "Samba File Server"
date: 2021-09-02T07:34:20-04:00
image: "img/samba.png"
categories:
  - Linux
  - Windows
  - Networking
tags:
  - Ubuntu
draft : False
---

This was a Samba File server that I setup on my latptop running linux to send files from my pc to my laptop.
<!--more-->

# Write-Up

Right off the bat after install I went into the etc/samba/smb.conf file and went to look at the
default configs. I didn’t really understand some of what was in there and didn’t have any use for the
printer section. So I just made my own config file from scratch and used the samba wiki for support when
needed. I also asked Steve about setting a workgorup in the config but did not. As per his advice, it was
not needed if it were to be a standalone server. The next road block was getting my windows pc to see my
laptops samba share. When I went the option of typing in the ip address in file explorer I got an error. I
then thought of starting over and using 2 virtual machines and using NAT so they would be able to talk to
each other but before that I asked Steve another question about the hosts file. I thought the two machines
might need to know about each other prior to allowing connection and he said it would depend on the
config. And that there may be a setting somewhere about only allowing the hosts in host.allow. I thought
that by making the user that connects, a user with limited privileges, I could circumvent that. However,
that didn’t end up working and I continued to try the host.file way. At this point I added the windows PC
into the host.allow file and tried to ssh into it but just received a “no such host is known” error. So in a bit
of a cheating way but maybe better for my circumstances I just put logmeinhamachi on both machines
and connected them using that vpn service. So even when my laptop and PC are in different networks I
am still able to upload files to the server and as long as both are online I can retrieve those files. For some
reason doing a systemctl status smbd.service returns a message highlighted in red however its not an error
and just says

>>[2021/04/2208:21:11.851914,0]../../lib/util/become_daemon.c:135(daemon_ready)
Apr 22 08:21:11 Banana-King smbd[1051]: daemon_ready: daemon 'smbd' finished
starting up and ready to serve connections


## Video Walkthrough

[![Samba Write-Up](http://sysads.co.uk/wp-content/uploads/2016/05/samba-smb-700x328.jpg)](https://youtu.be/4iO9VSXM4Ns)  
_Note: YouTube Video - Hold Ctrl + Left Click to open in new window_

#### Social

- YouTube - <https://www.youtube.com/channel/UCdvsMWPh4kQIyWELSkaGkPg>


## Conclusion

In this project I connected my computers to each other using logmeinhamachi and ran a samba file server on 1 machine to serve files.
