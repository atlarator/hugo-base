---
title: "xv6 LECTURE3"
date: 2025-01-05T09:49:10+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 3
No OS: lack of isolation  
fork() -> abstract core  
exec()/sbrk() -> abstract RAM  
assumption: user is always willing to break the isolation, while kernel is always trustable  
user mode and supervisor mode  
pgtbl: maps virtual->physical  
ecall: change to supervisor mode, get to a known point of the kernel code  

Singularity provides a way of process isolation without hardware support  

kernel is a big program holds all of the syscalls  
On CVE there's 2997 bugs detected on Linux in 2024(kernel bugs are much more common than we thought)  

build the kernel: make  
gcc compiles every .c, .o, linking, kernel/kernel, kernel/kernel.asm, produces an .iso  
run the OS: make qemu  
simulates the CPU, run into the first instruction in the OS  

kernel/entry.S -> start.c -> main()  

## Book Chapter 2
Isolation can be done by protecting sensitive hardware from system calls  
monolithic kernel: the whole OS resides in the kernel  
micro kernel: minimalize code run in the supervisor mode  
in xv6, isolation is done by setting processes  
trampoline: contains the code from user space to kernel space  
trapframe: protects the status of the process  
sret instruction is used to return to user space from kernel space  

process: an addr space as a virtual RAM and an thread as a virtual CPU  

Boot a RISC-V Computer:  
power on -> run bootloader -> load kernel into memory address 0x80000000(I/O starts from here) and run into M mode -> set up a stack at _entry for runnning C code -> run into start of C -> program the clock chip to generate timer interrupts -> call useerinit to start the first syscall -> complete exec() -> return to user space and initialize the shell  

## Lecture 3
No strong isolation will cause memory overwrite, which will lead to a bug hard to debug  
OS should be defensive  
There is a flag to check whether the CPU is at user mode or kernel mode  
BIOS is always trustworthy  

fork() as a syscall hasn't its function decalaration  
when calling fork: ecall sys_fork (here sys_fork is a number) -> syscall -> find number in a0 -> create a new process  
How to get control from buggy useer application? set a timer  

think qemu as a real circuit board  
qemu is an infinite for loop: fetch, decode, execute  
use gdb: b _entry; c; si; layout src;  
use n to goto next line of C code  

## Book Chapter 4.3 & 4.4
(Before the labs...)  
args for exec() in a0 & a1, syscall number in a7  
SYS_exec -> sys_exec  
return value in p->trapframe->a0  

fetchstr -> copyinstr -> walkaddr -> walk -> pagetable  

## Just before Lab syscall...

installing riscv64-unknown-elf-gdb:  
download the tar file from TUNA mirror site, unpack it, enter the current directory
```shell
mkdir build
cd build
../configure --prefix=/usr/local --target=riscv64-unknown-elf
make -j 12
sudo make install
```

## using gdb
```shell
target remote localhost:26000
b syscall
c
layout src
backtrace
```

```shell
p /x *p
p /x $sstatus
```