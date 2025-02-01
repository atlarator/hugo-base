---
title: "xv6 LECTURE20"
date: 2025-02-01T11:07:10+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 20

```shell
tcpdump -t -xx -n
```
ethernet addr  
today, ethernet LAN is a switch with cables to each host  
use the dest addr to decide where to send  
arp: translate IP addr to ethernet addr  
nest high layer packet in low layer packet  
packet mbuf allocator  

queues of buffers: avoid forcing discard, keep output NIC busy, allow independent control flow  
illustrate tradeoffs in kernel network structure  
could a disk cause a livelock?  
don't spend time on subsequent packets until done with this one  
modern Linux: NAPI  
livelock is made worse by doing even more processing before discard  
timeout + resend  

## Paper  

(TODO)