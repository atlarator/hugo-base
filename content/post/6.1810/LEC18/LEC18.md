---
title: "xv6 LECTURE18"
date: 2025-01-31T07:48:44+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 18

virtual machine: simulation of a computer  
accuracy may be 100%  
software simulation: like qemu, but may slow  
run the instructions in real CPU but whole in user mode  
VM trap handler  
trap-and-emulate approach  
handle privileged instructions into virtual state  
VMM must ensure that guest only accesses its own memory and must remap guest physical addresses  
turn page faults into operation  
each CPU in non-root mode or in root mode  
extended page table contains user memory space  
2 layers of addr  
CPU forces exit to the guest, passes interrupts to the host  
Dune: use VMX to run a Linux process  
a loadable kernel module  
additional functionality like r/w  
bottom line: faster for some Linux syscalls  

## Paper

provide process rather machine abstraction  
move the kernel into lower layer may lead to higher security  
poor interaction between VM and the host OS  
Dune is a loadable kernel module  

VTX: VMX root & VMX non-root mode  
VMLAUNCH & VNRESUME to enter hardware  
PCID permits a single user program to switch between multi-pagetables efficiently  
include the libdune  
translate through ioctl on ``/dev/dune``  
assume CPU is free of defects  
hypercalls are common in VMM  
use a narrower hardware interface to limit the differences  
Dune could be considered a type2 hypervisor as it runs on top of an existing OS kernel  

host-virtual to host-physical memory translation  
query the kernel for mmap, manually update EPT  
mirror the configurationgset to the kernel  
expose raw access to TSC  
use VMCALL to apply syscalls  
share code of low-level of VT-x operations butnot sharing high-level code with KVM  
VPID: enable a unique TLB tag for each Dune process  
libDune is totally untrusted by kernel, including pagetable manager, ELF loader, simple page allocator, routines assist using programs in managing exceptions and syscalls  

use dune_signals instead of builtin system signals  
simpler implementations, higher performance and security  

run sandbox in separate memory space  
sandbox: null policy, prevent undesirable parties  
wedge: a sthread abstraction for safe fork, fast sthread recycling  
modified Boehm GC  

build synthetic benchmarks to test performance  
higher performance in ptrace and trap  
leverage modern virtualization hardware  
