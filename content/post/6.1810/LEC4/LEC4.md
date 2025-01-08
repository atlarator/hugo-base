---
title: "xv6 LECTURE4"
date: 2025-01-07T18:57:30+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 4

what if a user program writes to a random part of memory?  
what way could we separate and isolate memory?  
page tables  
a level of indirection: CPU -(VM)> MMU -(PM)> MEM  
satp, MMU, kernel  

reduce the size of page tables, not building a direct-map  

page=4KB, maximum 52 bits, nowadays 27bits  
page table entry: 64 bits, 54 used, 10 of them are flags, low 12 bits of PA are from VA  
3-level page table to reduce its size  
a tree descended 9 bits at a time  

page-fault forces transfer to kernel  
PHYSTOP = 0x88000000  

the design and arrangement of user address space: easier for compiler to compile  
need contingous PM not VM  

kvmmake() makes kernel's page table  
kvmmap() adds PTEs to a page table  

## Book Chapter 3

```
bits 25  27    12
VA   EXT index offset
|
V
bits      44  10
pagetable PPN flags
|
V
bits 44    10
PA   index offset
```

TLB: avoid the cost of loading PTE  
write PA into satp register  
1 page table per process  
KERNBASE=0x80000000  
fork() uses parent's virtual memory address directly  
trampoline and kernel stack aren't directly-mapped  

pagetable_t: a pointer to root page table page  
walk() looks up for PTE's VA  
instruction sfence.vma flushes CPU's TLB  

allocator: keeps a free list of struct run  
kfree: set value to all 1  

refuse to execute, occur a page fault, kill the process and print the message  
make exploit attack harder by such a protection process  

sbrk() -> growproc() -> uvmalloc()/uvdemalloc() -> kalloc()/uvmunmap() 
exec() -> namei() -> quick check -> proc_pagetable() -> loadseg() -> walkaddr()  

xv6 is lack of malloc()-like allocator to allocate small pieces of space  

## Lecture 4

What do we want from isolation?  

The VA_MAX could be larger than PA_MAX  
where does page table allocates? kalloc()  
there are 4096 bytes of continuous PA in PM, but not continuous for pages  
the size of PM is determined by designers  
satp is points to the top directory  

don't use a translation scheme to rely on another translation scheme, so store PA in tables  
satp also stores PA  
why walk() ? initialization, sysinfo() needs walk()  
flexibility stays in dealing with page faults  
where will the data store? depends on a "multiplexer"  
mostly-identity mapping is used in kernel mapping  

stack overflow: page fault instead of covering other program's memory  
consolidate the mapping? good, but xv6 didn't do this action  

```shell
b main
c
b kvminit
c
layout src

```

```shell
b kvminithart
c
```

where to store satp? in each proc structure, there's a satp  
why 3-level not a single big table? you can remain many blocks empty  
etext is the last addr of kernel, KERNBASE=0x80000000, etext-KERNBASE=size of kernel  
