---
title: "xv6 LECTURE8"
date: 2025-01-15T10:29:46+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 8

pgtbl is hard to debug, why?  
how to speed up syscall? Linux has vDSO  
vDSO enables virtual syscalls  
511: trampoline  
510: trapframe  
509: USYSCALL  

protection bits? URV, WRV, XRV  
scan all pages could be expensive!!  

## Slides 8
which syscall could be sped up?  
mo side-effect, return constant value, value can change after entering the kernel  
options: getpid() & uptime()  

provide a bitmask for pages accessed  
detect page accesses without access bits? use page fault!  
TOCTOU attack: argument is modified after the kernel reads it  

chatgpt: reduce the time lap between check and use  

## virtual syscalls

put syscalls access kernel space -> access process addr space, reduce the cost of context switching  
vDSO? a library, or a memory region  
vDSO is a fully formed ELF image  

## List Balancing

file-backed pages  
change of kernel-tracking infrastructure  
