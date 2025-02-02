---
title: "xv6 LECTURE21"
date: 2025-02-01T22:25:18+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 21

What if malicious user code execute in kernel?  
the CPU may speculate the execution of asm code  
mispredict -> flush the result, revert content, restart execution  
CPU retires an instruction only after they won't be changed after all previous instructions are retired  

L1 miss: TLB look up, L2 look up with phy addr  
in real life, microarchitecture is not entirely invisible  
sense whether the instruction is cached: flush + reload  
use clflush to reflush the cache, use rdtsc to read the cycle counter  
deduce the low bit of kernel data based on which cache lines hold  
the attack is always failed  

fix the vulnerability: don't map the kernel in user pagetable, require a pagetable switch when context switches  
only return data from speculative loads  

## Paper

Meltdown: side effects of out-of-order processing  
exploit side-channel information on modern processors  
in out-of-order, CPU calculates for further information  
influence the cache, affects the side-channel  
kernel ASLR randomizes the offsets where driver is loaded on the boot  

use flush + reload to detect whether the instruction is cached so that the microarchitecture is no more hidden  
spectre attacks: lack of privilege escalation in Meltdown  
a transient instruction as a side effect  
execution suppression  
build a covert channel to receive information from microarchitecture and deduce this secret  
covert channel is not limited to microarchitectures  

steps: content of loadable mem location is loaded in reg, transient instruction, flush + reload  
bias is often lost in out-of-order execution  

KAISER: a strong isolation which won't map any kernel memory into user space  
