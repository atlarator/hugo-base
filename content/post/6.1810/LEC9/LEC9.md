---
title: "xv6 LECTURE9"
date: 2025-01-16T23:24:27+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 9

CPU & devices: complicated & parallel  
most code in modern OSes are device drivers  
UART, RS232 port  
UART & FIFO: not using a busy loop, but using interrupts  
UART interrupts if: rx FIFO goes from empty to not-empty, or tx FIFO goes from full to not-full  

device interrupts: device -> PLIC -> trap -> usertrap()/kerneltrap() -> devintr()  
interrupt just means the state of device is changed  
the bottom-half of interrupt doesn't run in the context of top-half  
registers: sie(supervisor interrupt enabled), PLIC claim: get next IRQ, sstatus  

kernelvec is like trampoline for kernel  
executing syscall in kernel, save in some proc's stack  

multiple devices? deliver to different CPU or stay pending  
disable interrupts? clear SIE, using intr_off(), remember the pending interrupts, deliver when kernel re-enables interrupts  
many places has parallelism, the operation msy not atomic!  
using producer-consumer buffer  

trampoline cannot tolerate a second interrupt to trampoline!  
polling strategy: Top-half loops until device says it is ready  
use DMA is more efficient  

## Book Chapter 5

dispatch happens in devintr()  
top-half: kernel thread, bottom-kernel: executes at interrupt time  
UART appears as a series of memory-mapped registers  
use consoleinit() to init UART hardware  
transmit complete  
init() will let the shell read the console  

read() -> consoleread() -> sleep()  
devintr() -(UART)-> uartintr() -> consoleintr()  
each time the UART finishes sending a byte, it generates an interrupt.  
in write(), uartintr() -> uartstart()  
allowing processes to execute concurrently with device I/O  

in RISC-V, timer interrupt happens in M mode  
start.c executes before main, to program CLINT hardware, set up a scratch area, set mtvec to timeervec  
The kernel could be made somewhat simpler if device and timer interrupts only
occurred while executing user code.  

## Lecture 9

save, process, resume  
asynchronous, concurrency, program devices  
How the '$' appears, and what happens after ls ?  
PLIC: 53 interrupt connect from devices  
core hold interrupt  

Driver manages device  
interrupt handler: top, bottom, a queue in it with read/write interface  
the queue is used to decouple top & bottom  
programming device: memory-mapped I/O; ld/st  
the kernel and the device should make a protocol  

$: device put it into UART, UART gen interrupts when the char has been sent  
ls: keyboard connect to receive line, generate interrupt  

RISC-V support:  
SIE: 1 bit for external interrupt  
SSTATUS: bit enable/disable  
SIP: interrupt pending  
SCAUSE: the cause of interrupt  
STVEC: holds address for the switching  

plicinit(): take interrupt from the devices  
more initialization in main.c, main() finally calls scheduler()  

init.c: creates a device as console  
shell itself writes into fd 2  

a pointer to producer and a pointer to consumer  

Interrupt:  
If SIE bitset: clear SIE bits  
sepc<-pc, save current mode, entering supervisor mode, pc<-stvec  

interrupts & concurrency:  
devices & CPU run in parallel, interrupt stops current program  
top & bottom deliver may run in parallel  
producer & consumer: read after write, using a queue  

once, interrupt was fast, now, interrupt is slow.  
solution: polling    
dynamic switch  
