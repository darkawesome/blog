---
title: "Snort"

date: 2021-09-07T23:06:47-04:00
url: /snort/
image: /images/2020-thumbs/snort.jpg
categories:
  - Linux
  - Windows
  - Networking
tags:
  - Ubuntu
---
This is a project I did for my computer security class where I made an intrusion detection system (IDS) and attempted to make an intrusion prevention system (IPS)
<!--more-->

# Write-Up

Initially, I began by setting up a reverse proxy server. Then when I went to start my
project for the Linux class I messed around with logmeinhamachi. And with that I said why not
combine the two and make something that instead of talks to clients and servers would monitor
the network and sort of do the same and more. So I began by setting up a snort server
(https://snort.org/ ) that was connected through the hamachi interface to access the network
and would monitor the incoming traffic there. The configs and the information on it was really
thorough and I was able to get through it with little to no fuss. I did get a few alerts on the server
that had to deal with SNMP.
>>[**] [1:1417:9] SNMP request udp [**] [Classification: Attempted Information Leak] [Priority: 2]
{UDP} 10.217.184.43:58888 -> 10.217.186.48:161

>>[**] [1:1411:10] SNMP public access udp [**] [Classification: Attempted Information Leak]
[Priority: 2] {UDP} 10.217.184.43:58888 -> 10.217.186.48:161

That's when I saw that snort was getting traffic from the schools WiFi instead of the hamachi
server. After that I looked in the config and a little bit around for another place to change the
interface again but I couldn’t find anything related besides setting the home and external
networks. So I then reinstalled snort to make sure that I configured the right IP address and the
right device name in the beginning. Then I kept getting the alert “ no preprocessors configured
for policy 0 “and I knew from the reading I did before that I would need to run 
https://stackoverflow.com/questions/29503344/snort-message-warning-no-preprocessors-configured-for-policy-0 

	`snort -v -c /etc/snort/snort.conf` 
  
to get rid of this error. However doing that doesn’t allow me to run through the hamachi interface. And after fumbling around I ran

https://www.linuxquestions.org/questions/linux-software-2/change-interface-listen-on-snort-877108/  

	`sudo snort -c /etc/snort/snort.conf -i ham0`
  
and it worked. At the end of it to get the whole thing working I ran:

	`sudo snort -d -l /var/log/snort/ -h 25.0.0.0/2620 -A console -c /etc/snort/snort.conf -i ham0`

Doing this shows me alerts in the console window, application packets are not included and the
logging directory is set. The -h I included because I wasn’t sure if the home network from the
snort.conf would actually work the way I wanted to. Thiscould probably be omitted from the long
string. All in all I think this would be better than simply having a remote proxy as the only
daemons on the machine running snort are ssh, samba and a mail daemon. However I thought
going a step further and being able to drop or reject packets from automatically from set rules. I
haven’t gotten to that point however as time was getting close and I wanted to turn in a working
project. So after messing with inline ips mode and the Daq I just went back to doing the old way
of using snort without the ips for now. However, I will get that figured out soon and try to make it
a ids and an ips.


## Video Walkthrough

[![Snort Write-Up](https://3.bp.blogspot.com/-8dJsBA5feZE/WQ7dzU5zaBI/AAAAAAAAJrA/OP6BQxD3d-IXWh8Ko0vh2zvDgliDylk2QCLcB/s1600/Snort-OpenSource-Network-Intrusion-Detection-Tool.jpg)](https://youtu.be/KyJFdf-kolE)  
_Note: YouTube Video - Hold Ctrl + Left Click to open in new window_


#### Social

- YouTube - <https://www.youtube.com/channel/UCdvsMWPh4kQIyWELSkaGkPg>

## Conclusion
