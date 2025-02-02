---
title: "xv6 LECTURE23"
date: 2025-02-01T22:25:33+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 23

what if dynamically update the kernel?  
observability, performance, security  
detect DDoS attack  
eBPF uses bin-constraint approach  
C reads cBPF, emulates the "language", run in the same addr space as in the kernel  

whether the asm-like language will break the isolation?  
binaries, look through the whole program and disallow huge offsets  
speed: slow interpreter -> JIT  
ways of tracking pointers  
is eBPF even the right approach to extensibility?  

## Paper

original: designed for capture and filter packets  
design of VM is left behind the traditional processor  
mapping eBPF to native instructions  
in 2014, eBPF is directly accessed by user space  

attached to a designated code path  
debugging the kernel  

lock the kernel, verify the simulation, secure mode  
define the type of eBPF programs  
eBPF map: allow data pass through kernel, kernel-user, user space  
