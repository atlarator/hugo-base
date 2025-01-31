---
title: "LEC19"
date: 2025-01-31T12:49:19+08:00
draft: true
---

## Slides

what if write an OS in HLL?  
less faults, but with less performance and less chance to interact with bottom of a computer  
tricky to measure trade-offs, must compare with production of C kernel  
kernel threads -> goroutines  
syscall: SYSENTER, SYSEXIT  
solve the heap exhaustion rule  
Go is easy to analyze heap bounds  
HLL truly simplified code  
concurrency in Go is easy  
compare apps through biscuit & Linux  
Go's GC has no many large pauses but many small delays  
many programs could tolerate the pause rate of GC  
Go is slower but competitive  

## Paper

Go: type-safe with GC  
Singularity didn't fix the security issues in HLL  
Rust kernels designed without using Rust as an implementation language  
no consensus in GC  

risk of deadlock when dealing with heap exhaustion  
apply too-small-to-fail rule, instead of exhaustion, the user application see delay  
certain kinds of bugs are less likely happen in HLL  

biscuit is a monolithic UNIX kernel  
shim layer: provide functions of underlying kernel  
provide user with POSIX interfaces  
use Go's channels to ensure synchronization related  
in performance-critical code, using read-lock-free lookups  
biscuit does not support scheduling priority  

biscuit's approach to kernel exhaustion: purge caches and soft state -> wait until reserve -> kill by kernel-watcher thread  
maximum amount of simultaneously live data  
maxlive to calculate code used for a single syscall  
handle poll & write loops in deep reservations  
apply to long-running kernel threads  
killer thread is woken when syscall's reservation fails  
Go's auto GC never reduce fragmentation nor increases performance  

unsafe Go used in memory related code  

count and explain the ways HLL benefits the kernel  
code execution bugs in biscuit is mostly avoided in HLL related features  
try ways to make code routine between C and Go same  
use pingpong and pagefaults to masure performance of biscuit  
Linux uses RCU to delay such frees of safe deleting  

