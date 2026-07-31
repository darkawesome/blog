---
title: "Ntp"

date: 2026-01-13T18:41:17-05:00
url: /ntp/
image: /images/2020-thumbs/ntp.jpg

draft: false
---

Been having some trouble with NTP pool servers not being reliable

<!--more-->

**What happened (the saga):**

**switching to `1.us.pool.ntp.org`:**

Looking at Jan 11 from 01:59 onwards, this was better than using **'0.us.pool.ntp.org'**

- Mostly 8/8 valid replies (excellent)
- Only 2 problem periods:
    - 01:59: one timeout (7/8 replies)
    - 04:59: one timeout (7/8 replies)
    - 09:59: **four timeouts** (4/8 replies) - this one hit 23.186.168.128 again

- **Old (0.us.pool.ntp.org):** Frequent multi-timeout events, sometimes 5-6 consecutive timeouts, one complete failure with WARNING

- **(1.us.pool.ntp.org):** Mostly clean, only 3 degraded syncs out of 11 attempts

The fact that 23.186.168.128 still showed up once in the new pool suggests that particular IP block genuinely has connectivity issues (probably a datacenter or network segment with problems). But with `1.us.pool.ntp.org` I was hitting it way less frequently. However later on that same day approx. 8 hours later I had a critical failure and lost connection to any NTP server and it knocked my servers off of tailscale.


## What is 23.186.168.128?

**Owner:** Amazing Creations and More LLC  
**ASN:** AS393746 (MAXHOST-IO)  
**Location:** Orting/Morton, Washington, USA  
**Business:** Small hosting company (maxhost.io) registered in February 2024

**Key details:**

- This is could be a smaller hosting provider run by an individual (Max Goodell)
- The company was registered in February 2024 and got their ASN in February 2024
- They're running a hosting business called MaxHost
- The entire /24 block (23.186.168.0-255) belongs to them
- They appear to be volunteering their servers for the NTP pool


**The bottom line:** This is essentially someone's small hosting business in Washington state that volunteered to be in the NTP pool. Nothing malicious, but they're clearly having reliability issues (probably bandwidth, routing, or server load problems). This explains the frequent timeouts you were seeing - it's not a robust, professionally-managed time server infrastructure.

---

## **171.66.97.126**

**Owner:** Stanford University  
**ASN:** AS32  
**Location:** Palo Alto, California  
**Network:** 171.64.0.0/14

**Analysis:** This is a university running NTP servers. Stanford should have robust infrastructure, so the timeouts are surprising. However, universities can have network congestion issues, especially during peak academic hours.

---

## **173.208.172.164**

**Owner:** Nocix (formerly DataShack)  
**ASN:** AS46562  
**Location:** Kansas City, Missouri  
**Type:** Budget dedicated server/colocation provider

**Analysis:** Nocix is a low-cost hosting provider founded in 2009, known for extremely affordable dedicated servers ($25/month starting). They're "budget friendly" which often means:

- Oversold bandwidth
- Less redundancy
- Minimal support
- Cost-cutting measures

The fact that 173.208.172.164 had **FOUR consecutive timeouts** is consistent with a budget host running an NTP server that's likely overloaded or under-provisioned.

---

*#*The above was some AI output on the problem servers**

 that I was encounerring when poking around 0.us.pool.ntp.org. I will try 2, 3 and all the rest when I am back on campus but for now a few problems here and there with 0.us is not so bad. I haven't gotten disconnected using it so it has been nice for that. I do however, have to look at the elephant in the room. There are way better time servers that I can look at like nist, google or any atomic clock. However, that wouldn't be fun. This little rabbit hole of keeping time has been fun to travel down. Learning about cesium vs hydrogen clocks or even the fact that their are Stratums for them. Making a tier list of servers that use GPS or use those that use GPS and so on up to Stratum 15.

To me its like really learning about the problems they had to deal with in the early days of computing with distributed systems. Like Tandem computers making computers that have to have the best time to keep up with ATMs, and allow computers to be networked. Though in my setup the glaring issue here is that there is no fault tolerance and the single raspberry pi that handles time can and has "creashed" the network. Though this is just the tailscale network and the actuall equipment on the LAN stays on.

[!] The remarkable Computers Built Not to Fail (https://www.youtube.com/watch?v=SSSB7ZTSXH4)  

I plan on continuing to monitot this and see what all happens as far as the overall reliability but when school starts im going to expirement with the other server pools and also what happens when I use another raspberry pi and have it use nist time.

Also there are some pretty nice upgrades coming to the rack soon. I plan on filling all 9u, getting a layer 3 switch and also getting a ups for redundancy. I would get a jbod or something similar for backups but I like living on the edge and sas harddrive prices are not fun right now. Eventually though I will have to look at that as its going to become a real issue with redundancy, reliability and fault tolerance. Thats why I want to use a zfs jbod or just invest in a 45homelab chassis.