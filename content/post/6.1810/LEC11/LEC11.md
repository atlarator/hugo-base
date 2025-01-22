---
title: "xv6 LECTURE11"
date: 2025-01-21T16:58:37+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 11

concurrency: threads inside the kernel, processes  
each thread, taken alone, executes in an ordinary way  
need locks when interact  

in xv6, only one user-level thread  
event-driven and state machine could also use to multitasking  
executing: using resources; not executing: save and release  
each process has its own kernel thread  

p->state: running, runnable, sleeping  
user->kernel->scheduler->kernel->user  
scheduler thread: one per CPU  
idle scheduler: no running thread  
swtch() returns to scheduler()  
swtch(): saves current registers in xx(a0), restores registers in xx(a1)  

yield() acquires lock, scheduler() releases it  
the lock is released by a different thread  

p->lock makes steps atomic  
scheduling policy?  

yield() can be called by kerneltrap()  


## Book Chapter 7

force switching: sleep & wakeup; long period without sleeping  
context: struct context -> struct proc  
swtch: save callee registers, return to instruction pointed by ra register  
continue for loop, find a thread, run it  
hold the lock, or different CPU may run same thread  
procedures that intentionally transfer control to each other via thread switch are sometimes referred to as coroutines  

scheduler: find a process, run, until it is terminated  
maintain invariants: p->lock acquires in a thread and releases in another thread  

in multi-core, we can't hold global variables for process, create struct cpu for each core  
tp: stores the core's haltid  
xv6 disable caller interrupt but wait until the struct cpu is returned  
return value of myproc() is safe  

conceal actions from one thread to another: sleep & wakeup  
sequence coordination mechanism  
use a high-level spinlock? most of time it will produce right result but it's expensive  
naive approach of sleep and wakeup will cause lose wake-up problem  
solution: the caller must pass condition lock to sleep  

sleep: mark the current process SLEEPING and release the resources of core  
same channel: see spurious wakeups  

pipewrite & piperead are complex applications of sleep & wakeup  
nwrite == nread + PIPESIZE  

wait: wait_lock; exit: reparent  
kill: set p->killed, wakeup  
p->parent is protected by wait_lock instesd of p->lock  
round robin: run each process in turn  
linux's sleep adds a wait queue  

## Lecture 11

full use of multi-core  
thread: one single exec  
care the PC  
interleaving of multiple threads  
interleave threads  

scheduling: interleave one thread, execute another thread  
compute-bound?  
pre-empitue scheduling: even the code doesn't release the resources of CPU, the kernel will interrupt it by timer  
on the contrary, volunteer scheduling  
p1 -> tf1 -> kernel stack-> swtch()-> ctx1 -> ctx0 -> swtch() -> scheduler() -> tf2 -> p2  
where contexts stores? process structure, p->context  
process vs threads? a process in user; or in kernel; or stored in trapframe and context  

yield(): change the thread into runnable  
no point to save PC as we know we're in swtch  
ra will be the point to return  
why only half of regs are stored by swtch()? it is called by C function  
we want the start process also atomic  
what we really care is ra  

RISC-V uses general registers instead of floating-point registers  
we believe the kernel code will infinitely looping without interrupt  
why trampoline and swtch must be written in assembly? C is hard to reach ra & sp  

almost each thread in Linux is a complete process  
allocproc() sets the new context for the process  
the first call in forkret is about initializing file system and crash recovery  
