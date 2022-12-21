---
title: "Nextcloud"

date: 2022-09-27T14:11:48-04:00
url: /Nextcloud/
image: /img/nextcloud.jpg
categories:
  - Linux
  - Networking
tags:
  - Ubuntu
draft: False
---
If you have knowledge, let others light their candles in it. -Margaret Fuller 
<!--more-->

## The Why 
A while ago I decided to make a nextcloud server just to host important files and begin to own my data. Also google my google drive was starting to fill up with videos. The initial set up was a VM with about 150GB of storage where I had school papers, coding projects and other miscellaneous documents. However, the way that I would connect to it would be through ssh. This made it really clunky to connect to and was more commands than I would care for when it comes to just moving a single document to it. So a quick fix was to use a bash script and speed the process up. However, I again changed how this worked when my roomate lost his harddrive that he works off of to make music for people.

## A new vision 
Honestly I dont feel as though I did enough to try and fix his harddrive initially. His 250GB of data was lost and he was hurting over it for a bit. And since I had the old server laying around I had to think of a way to have him connect to it easily. Since we were on the same network he could just use the IP address and connect anytime he was home. However, when he wasn't home and needed the information off the server again we encountered a problem. For his needs, getting the music files off the server was easy with the interface but using ssh was not something he wanted to do. And so again we had to change the way to connect so it was easier for the end user. The quick fix was to setup a logmein hamachi server so he could connect to the server. The OPSEC problem with this is that he would have access to our whole network including all of the other VMs I had hosted. 

## Storage for All
So I had to find a way to secure my servers, make it easy for him to connect and make it easy for anyone in the user to use as well in the future. I decided to use a network tunnel using cloudflare. Really decided on this because it met my needs, finally gave a use to an old domain I bought, and made it so I could use my own server anytime I wanted from anywhere. For me and my roomate this worked out well so the next step was to look at who in my group of friends would be interested in this as well. And with a bit of testing we were live. 


## Conclusion
After a little bit of testing with friends all I need to do on my end is get a new switch to get a 1G switch. The one in my current config doesn't allow for 1000Base-T and really does't help the Boys out when it comes to uploads and download speeds. Also a NAS would be nice to have for increasing storage. All in all this isn't its final form. We have a lot more to do before I think its at a place where I can host all of my friends storage. 

## Cloudflare Documents 

[![Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/static/documentation/connections/connect-apps/handshake.jpg)](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)  
_Note: Cloudflare Tunnel Write-up - Hold Ctrl + Left Click to open in new window_


#### Social

- YouTube - <https://www.youtube.com/channel/UCdvsMWPh4kQIyWELSkaGkPg>






