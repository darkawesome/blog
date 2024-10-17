---
title: "Server-Update"
categories:
- Linux

draft: false
---

Tailscale is cool. Vpns are nice. I need more ram and faster CPU speeds.
<!--more-->

# Tailscale and old tech 
Last week my server was configured in such a way, that when I needed to do something on it remotely I would use LogMeIn Hamachi. Which exists on PVE1, and from there I would ssh into my other machine or into other VMs or containers. Now since I am running the two machines in a cluster ( I will be adding a third soon to utilize HA) when PVE2 was having connection issues it conscerned me greatly. There is somewhere between a beer belly and a few ex wives between me and my homelab. So for me to go back home was off the table. Though there were times when the cluster would make a connection for a good hour or so. Now addmittedly, I didn't fix this issue because "it fixed itself". Now I realized after day 3 it did not. So after checking logs and running around with the resources... I realized a few things went overlooked when configuring the server.

1. I serverly under estimated the amount of resources it would need
2. The intel Pentium G3250 is gonna need to be like a hot dog and ketchup
3. A "permanent" fix is one that worked too well

The real culprit here was that the server was using the 2 cores. The machine it self only has the 2 cores to begin with. Honestly im surprised this issue didn't occur more often because i've never seen it till now. Though I believe it was because I put a new vpn on it. I decided to throw tailscale on it, one to test it out and to have some redundancy. I believe this was just asking a bit more of it and was the reason why I was seeing some crazy numbers coming from the cpu utilization on the host machine. I have been monitoring it more closely now and even looking at solutions like uptime kuma, Grafana, or checkmk. More research is needed here but I am in Grad school now so this may get more attention at a later date.

# Vpns and exit nodes
A beautiful feature of Tailscale is that it allows for simple configuration and has introduced integration with mullvad vpn. Currently through a free plan on tailscale you can have 3 user accounts and 100 devices on a vpn. Now I don't know if you got the same idea as me reading that but... yeah.. my account will stay free forever. Mullvad did get $5 a month out of me though. From looking at the offerings of exit nodes all over the world. I am very happy with what I have been able to get now from a privacy focused company. They also allow you to overwrite the dns that the device connected to it uses. And with allowing the local network acces the cluster stays in quorum. The documentation <a href="https://tailscale.com/kb/1258/mullvad-exit-nodes?tab=linux">here </a>is quite good.

# Looking Forward 
I am thinking of moving toward a more server grade motherboard and CPU. A beast like the Supermicro MBD-X11SRL-F-O would be perfect. Well to be honest it is overkill for what the single container on the machine does now. Though it does leave room for more containers and VMs to come at a later time. Noteable features would be IPMI, up to 1TB of ECC LRDIMM, 7 fan headers, 8 SATA3 ports, and 2 RJ45 ports. Other specs are other specs those are the most important to me at this time. Really excited to try out IPMI as it would lessen the need for 2 vpn tunnels but who knows. The CPU I was looking at was the intel Core i7-7820X. Nothing real special here just a 8 core 16 thread guy that'll sit until I get an Intel Xeon or an AMD EPYC. That'll probably not happen anytime soon soon here but who knows.