---
title: "Lxv6 LECTURE7"
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

(TODO)