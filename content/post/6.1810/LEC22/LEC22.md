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
