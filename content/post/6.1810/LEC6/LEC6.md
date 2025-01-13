---
title: "xv6 LECTURE6"
date: 2025-01-12T16:14:43+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 6

focusing on user->kernel transition  
NEVER execute user code in supervisor mode  
C on RISC-V puts function arguments in a0, a1, a2, &c  

check pagetables: C-a c, info mem  
trampoline: the start of kernel's trap handling code  
at the top: avoid punch a hole in user addr  

observe PC is an indirect way to check whether we're in supervisor mode  

ecall:  
change mode, save PC in SEPC, jump to STVEC, disable further interrupts  
ecall does as little as possible  
even supervisor mode is constrained to use pgtbl  

save the user registers:  
using trapframe or the sscratch register  

why not a crash? we just switched page tables while executing  
the trampoline is mapped at the same addr in pgtbls  

back to usertrap(), return $ and space  
usertrapret()  
prepare for next transition  
use sret instruction  

complex from isolation  
what if design for faster?  

## Book Chapter 4

ecall & exception will cause a trap  
stvec: trap handler  
sepc: PC saver  
scause: cause of occurring a trap  
sscratch: avoid overwriting  
usertrap: determine the reason of trap  
usertrapret -> userret -> switch satp to user space -> load trapframe addr to a0 -> executes sret  

argint, argaddr, argfd retrieves n'th argument of integer, address or fd  
kernelvec: save regs, jump to kerneltrap, yield

## Lecture 6

don't let user code to interfere the transition between user and kernel code  
supervisor: R/W control regs, satp, stvec, sepc, sscratch, use PTE/PTE_U  

write() -> ecall -(user->kernel)-> uservec -> usertrap() -> syscall() <-> sys_write() -> usertrapret() -> userret()

the user code could only access memory with PTE_U flag  
(checking by C-a c, info mem)  
csrrw? swaps a0 and special temp register  
lack of crash, the status of pc -> we're in supervisor mode  
OS cares lot about performance, so ecall never forces you to do stack operations  
struct trapframe stays in proc.h, using 32 slots to save registers  

when running shell, sscratch holds the pointer to trapframe  
save? beware overwrite  
the instructions not executed? we're still in trampoline, where user VA = kernel VA  

what if switch to another process?  
we could only switch pgtbl on trampoline  

sret? re-enables interrupts, setting the PC same as sepc, switching user mode  
what happens in UIE? 