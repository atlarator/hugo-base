---
title: "xv6 LECTURE17"
date: 2025-01-30T14:55:36+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 17

What should a kernel do?  
traditional: a "monolithic" kernel abstraction(UNIX, Linux, xv6)  
abstraction leads to security  
monolithic kernel may lead to a big, complex and buggy system, may lead to many thoughts in designing the whole kernel  
lots of code is needed to execute a pipe  

microkernel: make kernel simpler enough  
move most techniques out of the kernel, to the user space  
most bugs are at drivers, move them out to the user space so the kernel will be more modifiable  

L4: less syscalls, less code  
missing almost everything, but everything is in the user space  
pager: mmap, cow, alloc  
design to improve bad performance brought by microkernels  

send() & recv(): async & buffered  

how to build a microkernel? for embedded, easy; for a server, maybe run UNIX on top of the kernel is good  
on it, Linux is just a program!  
but Linux switches its threads through L4-s threads  
fork() -> turn into IPC -> alloc pages -> get page faults  
L4 server acts as the Linux's pager  
getpid() is turned into 2 syscalls in L4(call, sendrecv)  

whole OS benchmark: AIM  
microcontrollers are used to run specific software  

## Paper

researchers have almost stopped microkernel researching as its poor performance  
too-low and too-higfh ideas  
L4 built-on threads & addr spaces  
inter-process communication  
grant, map, unmap  
IO are treated as part of addr space  
compilers and libraries hides difference of each architecture  

Linux's processes and resources management system is architecture independent  
device drivers are always hardware dependent  
top halves & bottom halves of Linux interrupt handlers  

microkernel's task use for Linux's processes  
problems surrounding libc  
simulate TLB on Pentium processor  
the L4Linux uses physical copyin and copyout  
inter-thread using the same addr space  
support TLB for smaller spaces  

creating one server addr space per Linux process  
syncronization of server space & server thread lead to the mistake  
re-construct the TLB as Linux's minimum user space is too large  

using benchmarks compare L4Linux to MkLinux  
co-location decreases L4Linux's security, so is it worth?  
measure the cost of getpid() syscall  
indeed IPC is the main additional cost in L4Linux  
use lmbench to measure the cost of sync RPC  
use matrix multiplication to test VM performance  
use a protected transfer to reduce the cost of security, however, performance decreased  
