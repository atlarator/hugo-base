---
title: "xv6 LECTURE16"
date: 2025-01-29T17:25:38+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 16

OS kernel use VM in creative ways, even benefit from VM  
trap, prot, unprot, dirty, map  
mmap(): map memory into user space  
mprotect(): change the permission of mapping  
munmap(), sigaction()  
kernel asks VM waht to do, generate signal, run user handler, kernel returns to user program, continue or restart the previous action  
bake a large sqrt table by a single page  
real-time GC Algorithm  

real-time: cheap and large to allocate  
cost: pointer reside, difficult to run collector and the program at the same time  
adding instructions could solve user-level VM problems  

## Paper

add user-level handler to handle fault with more friendly messages  
not decrease the accessibility of a page, but decrease for a large batch with small cost for each page  
in the same pagetable to avoid expensive context switching  

fetch and store are common in memory operations  

real-time garbage-collecting algorithm  
divides the memory into 2 spaces: from-space & to-space  
trace out searchable objects  
forward: point to the space previous to the to-object  
mutator see only to-space pointers, new area and scanned area only contains to-space pointers  
synchronize mutatoe and collector  
multiple mapping pages: let the page scanned but still accessed by mutator  

SVM: large conherent VM accessed by multi-core  
shared memory only exists virtually  

page fault could be used to make checkpointing real-time  
acess protection page fault to instead saving data to disk all at once  
protect dirtied-pages only  

young records: may die sooner than old records and likely points to old records  
VM could detect assignments to old records  

persistent store: a dynamic allocation heap persists from one-invocation to the next  
commit or abort  
as fast as fetches and stores in main memory  
modified: not altered until commit  
compiled programs could act fetch quickly  

extended address  
handle page fault, append another page, transform it into short pointer  
page compression algoerithm  

detect stack overflow: mark the page above the stack invalid  
heap overflow: detected by a control and condition-overflow branch  
at the end of the heap, cause a page fault, call the garbage collector  
slow fault will cause problems  

clear what is needed and what is not  
TLB consistency, easily patch and fix TLB faults  
batch the shootdown to stop the shootdown to the multi-core  

optimal page size <- dynamic ram  
algorithms must be asynchronous friendly  

## Lecture 16

(TODO)