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

receive livelock: system spends all its time deal with interrupts  
poll is expensive especially when I/O is rare  
interrupt-driven design loccking is better  
network implementation must deal with high event rates  
non-flow control protocols  

receive livelock: the system is not deadlocked, but has no progress on current event  
resonable latency and jitter  
scheduling system must fairly allocate CPU resources among packet reception  
queue provides some insulation against packet losses due to transient overlords  
batch interrupts  
receive livelock: a state when the system is no useful process is made, as some necessary resources is consumed with processing receiver interrupts  
latency is increased by the time it receives the entire burst  
transmissin starvation: packets may awaiting transmission, but the transmitting interface is idle  

a cycle that could repeat ad infinitum  
use a round-robin schedule to provide fairness polling  
purely interrupt driven wil lead to livelock, purely polling system will lead to unnecessary latency  
fast-path designs  
use route-under-test to check the performance  
interrupt handler does almost no work at all  
unavoidable queue as no guarantee to let the output interface run as fast as input interface  
the screend program is typically run as the only application on a system  
simply measure the resources used by CPU for receiving packets  
