---
title: "xv6 LECTURE7"
date: 2025-01-14T12:07:31+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 7

VM-related projects: COW, mappings, performance  
ideas: isolation, indirection  
panic? update page tables when page fault happens  
VA causes page fault: stval reg  
violation causes page fault: scause reg  
instruction/mode can also cause page fault  

if the user program asks for more memory, sbrk() could be expensive  
zero-filled page? large part of memory filled with zero  
COW or write then copy  

don't let fork() copy every pages from parent  
but share addr space between parent and child, use RSW in PTEs  
page fault: make copy, map, read/write(hard in real life!)  

load the pages for the file on demand  
keep meta information in VMA  

memory-mapping: use load & store to access files  
page-in & page-out due to the LRU strategy, especially when the file is larger than the VM page  

KPTI is used to handle meltdown  

## Slides

Modern OSes allocate memory lazily, only allocate memory when user program accesses the memory  
COW: make all pages read-only, On page fault, copy page and mark R/W, use extra PTE bits  


## Book Chapter 4.6

xv6's response:  
kill the faulting process; kernel panic  
let child and parent use same block of PM? overwritten!!! 
load, store, instruction page faults  
COW fork is faster especially in fork just before exec  
lazy allocation  
demand paging; page to disk  


## The Paper of COW

<a>https://lwn.net/Articles/849638/</a>
Project Zero issue; CVE-2020-29374  
for performance, android doesn't put an exec() just behind fork()  

Xu: Breaking COW by GUP; Tovalds: do_wp_pages()  
the new approach breaks RDMA self-tests  

a userfaultfd() was reported  
something about the new rules  
the way of using RDMA for soft-dirty or userfaultfd() is broken now  

## Lecture 7

page faults can let page mapping from a static map to a dynamic one  
information needed: faulted va page, stval register  
type of page fault  

when page fault happens:  
allocate 1 page, set the page all 0, map the page, restart instruction  
what if the process uses up the PM? just kill the process  
lazy allocation: the memory not allocated will not be mapped  

free the memory that is not allocated will cause a fault, as the sbrk() let p->sz go upper  
negative number? shrinking addr space, but be careful!!!  

zero-fill on demand  
kalloc() a zero page, change the mapping  
copy, update, restart  
the cost of switching space and storing registers will be huge!!!  

COW fork: use share instead of copy  
set all pages read only, receive page fault, copy the page into a new page, map it, restart instruction, call userret()  
except trampoline which could never be freed, other physical memory blocks are belonged to two or more different processes  
but when to free the page?  

demand paging  
exec(): load text, data, segment, eagerly alloc page  
read, map, restart  
If out of memory: evict, use the just-free-page, restart  
evict the clean pages not dirty ones!!!  

mmap(va, len, plot, flags, fd, off)  
unmap(va, len), write back dirty block  
