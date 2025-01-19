---
title: "xv6 LECTURE10"
date: 2025-01-19T07:11:18+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 10

kernel must deal with parallel syscalls  
race between two cores calling kfree() leads to a page losing  
if multiple cores calls the lock, only one will be returned, other will wait until the lock release  
auto locking? needs explicit comtrol over different regions of code  
deadlocking problem  
locks are often not private business of modules  
lock & parallelism may require a whole re-write for the project!  
use big lock first, big lock is always enough  
check and re-lock the lock atomically, pushing down the question into hardware  

spinlock: if locked=1, again the loop, if locked=0, set it to 1, return 0  
if you use locks, you don't need to understand the memory ordering rules, you need them if you want to write exotic "lock-free" code  

in RISC-V:
```assembly
a5 = 1
s1 = &lk->locked
amoswap.w.aq a5, a5, (s1)
```

## Book Chapter 6

concurrency: multiple instruction streams are interleaved  
wait() frees child's memory  
race: the location is accessed concurrently  
critical section: code covered in acquire() and release()  
invariants: properties of data structures maintained across operations  

traditional approach:
```C
for(;;)
{
    if(lock == 0)
        lock = 1;
}
```
two cores may reach line 43 at the same time!  
amoswap r, a  
swaps the value in address a and register r  
kalloc: a single free list protected by single big lock  
deadlock: a program requires A to B, the other program requires B to A  
deadlock requires global lock aquisition  

file system contains a huge lock-chain  
re-entrant lock: recursive lock  
acquire the lock again: allow the action  
if a spinlock is used by an interrupt handler, a CPU must never hold that lock with interrupts enabled  

nesting level of locks: acquire() calls push_off(); release() calls pop_off();  
__sync_synchronize() tells the compiler not to re-order the code, in fact, it's a memory barrier  

sleeplock: not occupying the core for the lock could last for a long time  

lock can be expensive if CPU requires the same lock at the same time  

## Book Chapter 9

acquire() and yield() are released in the scheduler thread  
inode acts as a shard lock  
implementation of spinlock: no lock at all  
scheduler, different process call fork() at the same time  

## Lecture 10

(TODO)