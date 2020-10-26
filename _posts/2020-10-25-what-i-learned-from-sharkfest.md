---
title: What I learned From Sharkfest '20
layout: post
post-image: "/assets/images/sf20v_logo.png"
description: Sharkfest is a wealth of information for all network and security practitioners.
  Here are a couple packet analysis tips that I'll be using and you can too!
tags:
- Sharkfest
- Wireshark
- PCAP
---

I learned a ton of great things that will help me as a better network engineer, and analyst. This will be a recap of a couple important take aways that everyone can use any time they are looking at PCAPs. If you find any of this interesting go subscribe to the [Sharkfest YouTube channel](https://www.youtube.com/c/SharkFestWiresharkDeveloperandUserConference/) and start planning to attend the next Sharkfest near you!

---

## Where You Capture From Matters!
The key take away here is: 
1. Know what questions you're trying to answer and which capture points are the best to answer those questions.
2. Capture at both sides of the conversation, source & destination, this will give the most complete view.
<p align="center">
<img src="/assets/images/capture_points.png">
</p>

**Source/Destination local host capture:** (Score: 1/3)
- :heavy_check_mark:Software change only. (Administrative privileges required)
- :x:Data/timings/performance can be mis-leading.
- :x:Hardware offloading (at the NIC) can present misleading PCAPs with regards to: packet tx/rx timings, performance and packet sizes.

**TAP near the source/destination:** (Score: 3/4)
- :heavy_check_mark:Best option for measuring tx/rx timings.
- :heavy_check_mark:True on-the-wire sizing.
- :heavy_check_mark:Great for measuing performance.
- :x:Requires physical network changes.

**SPAN/Mirror:** (Score: 1/5)
- :heavy_check_mark:Can capture many clients/servers at once.
- :x:Not ideal for measuring performance.
- :x:Each vendor can pull the SPAN/Mirror from differnt packet/frame hardware/software paths resulting in differnt headers being stripped or added before or after being SPAN/Mirrored.
- :x:Packets can be lost/not SPAN'd resulting in broken sessions in the PCAP.
- :x:Packets can be queued for tx changing the original rx timing.

---

## IP ID Number
From [RFC 791](https://tools.ietf.org/html/rfc791#page-11):
```
   Identification:  16 bits

   An identifying value assigned by the sender to aid in assembling the
   fragments of a datagram.
```

The IP ID is set by the sender for when a packet is fragmented in the path it indicates to the receiver that all the fragments should be reassembled and are part of the same packet. Both the client and server set their own IP ID values.

The very convienient thing about IP ID is, they are often sequential making it super easy to spot: out of order packets, missing packets, and hardware offloading in some cases. If you were to follow an IP conversation and filter by only the source or destination, you would commonly see sequential IP ID's.
<p align="center">
<img src="/assets/images/ip_id_sequence.png">
</p>
This is a mandatory field in IPv4 for all IP packets but is only seen in IPv6 packets when fragmentation is used.
<p align="center">
<img src="/assets/images/ip_id_packet_header.png">
</p>
**Warning:** It is possible to have the same IP ID for different sessions within the same PCAP.
<p align="center">
<img src="/assets/images/same_ip_id.png">
</p>
Like everyhting in IT and networking, there's always exceptions, as was pointed out in Sharkfest Discord by [Jasper](https://twitter.com/PacketJay):
> Some Firewalls can change the IP ID, e.g. pfSense has a "IP Random id generation" setting which modifies the IP ID.
<p align="center">
<img src="/assets/images/pfsense_ipid.png">
</p>
IP ID can also reveal if a TCP Retransmission is a true retransmission or if it is a packet that was captured twice. If a packet was captured twice, or if duplicate packets were created by a networking device in the path, the IP ID would be the same on both captured packets. If the captured packet is a true TCP Retransmission a new IP ID would be used (in most cases).

---

## TCP Stream Graphs
This is a feature in Wireshark I have never used in confidence. I knew it existed but didn't understand: what I was looking at and what these graphs could indicate.

Through some careful hand holding I now have a basic understanding of them and I now incorporate them into my analysis, when needed.

These can all be found under: **Statistics --> TCP Stream Graphs**

**Stevens Graph:** Tracks sequence numbers seen over time. This is uni-derectional. This can indicate so quickly when out-of-order packets have been seen. 
- :heavy_check_mark:Ideally you would want a constant incline.
- :x:When you see valley's or falls it indicates a packet was received with a sequence number lower than the packets before it.
- :x:When you see a plateau it indicates time has passed without seeing an increase of sequence number.
**NOTE:**
: These indicators by themselves aren't always an issue but can start to build a story of where an issue might be.
 <p align="center">
<img src="/assets/images/stevens.png">
</p>
---

## The Power of Profiles
I have never used different profiles for different scenarios. I always stuck with the "Default" profile. Through Sharkfest I learned the importance of different profiles and how they contribute to the efficiency of analysis.

Depending on what type of questions you need to answer you might need different data available to you (different from the default profile). This is why profiles are so important.

Some of the things you can do with profiles are: 
- Coloring Rules: Define when Wireshark should highlight a packet and what the foreground/background colors should be.
- Columns: You can customize colomns putting the data you need next to each other.
- Filter Buttons: Buttons which store common display filters so the can be easily applied.

While the below TCP Profile might not mean anything to you. That's okay because it's not suposed to mean anything to *you*, it's supposed to mean something to *me* and help *me* solve a problem. Profiles are very personal and should help the individual increase their own analysis efficiency. 
 <p align="center">
<img src="/assets/images/tcp_profile.png">
</p>

---

## Expert Information
Shamefully,  I never knew what that "stop light" indicator was for in Wireshark's bottom left corner. From Sharkfest, I learned that is an aggregate of the "Expert Information" Wireshark sometimes adds to packets to indicate something that should be looked at. It's important to know that these aren't all "problems", this is just information. Some of the quick things this is good for is quickly locating packets where: TCP Window is Full, Out-of-Order segments TCP Retransmissions and more.

The color of the stop light will be of the highest severity seen in the capture.
 <p align="center">
<img src="/assets/images/expert_info.png">
</p>

---
# ...But Wait, There's More!
I learned sooo much and there's so much more detail and depth to go into on each of these topics.

There are many more analysis eficiencies to go over.

If you want to learn more or if I got something wrong, let me know using Disqus below (you can post as guest), or let me know on Twitter :wink:

Again, if you find any of this interesting go subscribe to the [Sharkfest YouTube channel](https://www.youtube.com/c/SharkFestWiresharkDeveloperandUserConference/) and start planning on attending the next Sharkfest near you!