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

coordination  
```C
acquire(p->lock);
p->state = RUNNABLE;
swtch();
release(p->lock);
```
check whether the thread is runnable  
calling swtch() requires p->lock but forbid any other locks  

two threads acquire same locks  
freeze as the process2 will spin forever  

coordination: sleep() & wakeup()  
busywait loop  
sleep channel: sleep() and wakeup() could check whether the thread is sleeping  
why sleep needs the second argument?  

lost-wakeup problems  
broken_sleep: simply set the value  
wakeup: for each p in procs: setback the process to runnable  
```C
int done;
uartwrite(buf)
    for each c in buf:
        while not done:
            sleep(&tx_chan);
        done = 0;

intr()
    done = 1;
    wakeup(&tx_chan);
```

add lock & unlock; why not work?  
on some other core, UART interrupt is working  
nothing will wake up the broken_sleep, as wakeup() has already happened  
tx_done: a way for interrupt routine to connect the uartintr()  
each typing will cause an interrupt, wakes up the sleeping process for a while then continue sleeping  

wakeup(): run through process table, lock every process, if SLEEPING & same channel, wakeup and release lock  
after we released the condition lock, the wakeup() will not see this process until it holds process lock  
reader acquire the lock to prevent writer from sleeping  
last thing sleep() does is acquiring condition lock  

exit() will close files if necessary  
wait() will find the process whose parent process is current process and in state ZOMBIE  
parent will call freeproc() to release resources  
concurrent exit of parent and child?  

why doesn't kill() kills all the processes that could be handled?  
not allowed in real world, but in xv6, there's no permissions, you can do this  
in Linux, the process to be kill must have the same id as its user id, or killing is not allowed  
we would never expose broken variants in the file system  
