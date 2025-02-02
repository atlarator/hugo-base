---
title: "xv6 LECTURE22"
date: 2025-02-01T22:25:25+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 22

can we have a lock that allow parappel reads?  
one writer, but no other reader or writer; lots of reader, but no writer  
atomic CAS's execute one at a time -- not in parallel  
r_lock's writes are expensive  
reader connot simply don't lock, but use methods like RCU  
in RCU: writer doesn't modify the file directly, but prepare a new copy, the reader won't see a partial midified string, even if it doesn't hold a lock  

before: no change is visible; after: all changes are visible  
use memory barriers to enhance order of transactions  
potential disaster: use after free  
let writer wait for GC  
implement the graceful delay  

RCU implements nearly zero in reads  
reference counting, redesign for no sharing  

## Paper

RCU's success: high performance in concurrent reading and updating  
support for concurrent reading and updating, low computation and storage overhead, deterministic completion time  
VFS needs concurrent reading and updating  
RCU allows thread to wait for the completion of pre-existing RCU critical sections, but coordinate activities using other mechanisms  
synchroize_rcu: ensure every cpu acts a context switch  
writer just need to wait for reader, but reader must communicate with writer that the critical section is complete  

RCU's simplest usage is to wait for a transaction complete  
using RCU in NMI system  
execute in RCU, apply the reference counting  
using an RCU critical section is good if the object is relatively short  
RCU could use to remove need for type-safe memory  
RCU has higher deadlock immunity than read/write lock  

RCU is used increasingly in Linux kernel source code  