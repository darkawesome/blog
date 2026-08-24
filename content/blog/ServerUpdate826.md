---
title: "ServerUpdate826"

date: 2026-08-17T09:32:02-04:00
url: /ServerUpdate826/
image: /images/2020-thumbs/ServerUpdate826.jpg

draft: false
---

Almost fully rack mounted
<!--more-->

In an effort to lower my footprint and consolidate the servers I have moved them almost fully into 1 rack. I have a dell poweredge at University of Maryland to be used for the classes. If I go back and grab that one it will have to sit on top of this current rack until I buy a larger one.

For now I need to move the drives from the media server into the Supermicro cse-826. The bandwidth and the internet speeds have moved from megs to a gig. Though the current setup is capped at around 1gb speeds and probably moves a bit slower than that due to other factors but its a huge jump in speed. The users haven't noticed (at least enough to say anything) but its been nice on the numbers end for me.

Top down I have a Mikrotik RB 3011 Ui AS-RM router, a HP 2530-48G Switch, a cheap amazon 2u server chasis, a supermicro cse-826, a APC UPS SMX750, and a raspberry pi. What I love here now is the redundancy on the netwrok, the battery back and the flexibility of RouterOS and a layer 3 switch. So far it has been pretty fun configuring everything and I have been really trying to share it with the other people I know in this space. I know a good bit of people from family friends to people I went to school with that are interested in doing this but I think the amount of control or learning seems daunting to them. Im not sure though.

From my view I have a good onboarding to get someone up to speed on all the machines and services I have running and how to for when things go wrong like nvidia drivers not playing nice on an update and relinkig devices to the server host. Although I have yet to have anyone go through the whole gauntlet of onboarding. It has only been looking at the knowledgebase and looking at a performance dashboard. Im still the one handling tickets.

## Conclusion

What was once a pile of devices is now ballooning into a nice datacenter/ playground for learning. Though I want to share this with others I have been finding that often I am the biggest user of all of the services and the users use a handful. The few problems that crop up now typically aren't too bad though I did have a harddrive melt the sata port shroud from bad airflow. It wasn't running anything important and has now been replaced but a lesson learned nonetheless. I may try and figure out a solution given it is a 1tb drive but I am not very pressed now to get it fixed.