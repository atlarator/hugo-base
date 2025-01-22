---
title: "xv6 LECTURE12"
date: 2025-01-22T14:43:23+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 12

why hold p->lock across threads?  
forbid holding lock when yielding the CPU!  
threads often wait for conditions  

use coordination primitives  
uartwrite(): writing thread should give up CPU  
sleep() cannot simply wait the event, or will cause lose wake-up problem  

what's wrong if uartwrite() releases the lock before broken_sleep()?  
uartwrite() went to sleep EVEN THOUGH UART TX WAS DONE, nothing will awake uartwrite()  
use lock to prevent wakeup() from running during the entire window  

sleep() will require a lock to protect its condition, both sleep() and wakeup() will hold the condition lock  
caller acquired condition lock before calling wakeup()  
acquire p->lock before releasing condition lock  

wakeup() holds both locks, sleep() just need to hold either  

wakeup() can't proceed until after swtch() completes so wakeup() is guaranteed to see p->state==SLEEPING and p->chan==chan  
all uses of sleep are wrapped in a loop, so they re-check  
sleep() doesn't need to know the condition  

a thread cannot free all of its own resources  
voluntarily quit with exit()  
kill() just sets p->killed flag  
kill() will change SLEEPING into RUNNABLE  
user space: will die next time it makes a system call or takes a timer interrupt  
kernel space: target will never execute another user instruction but may spend a while yet in the kernel  


## Book Chapter 7

(DONE)

## Lecture 12

(TODO)