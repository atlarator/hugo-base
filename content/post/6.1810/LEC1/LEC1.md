---
title: "xv6 LECTURE1"
date: 2025-01-01T08:28:39+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 1

Purpose of an OS:

Isolate hard/software, virtualize hardware  
Interact with OS via syscalls  
First arg in syscall is FD  
UNIX I/O is 8-bit bytes  

When CPU receives a syscall:  
save, jump, execute, call, restore, reduce, jump back  

Shell is a user program instead of shell  
fork() lets us create a new process  
exec() replaces current process with an executable file  

## Book Chapter 1

Operating system let different softwares use hardware together  
Process: instructions, data, stack  
fork() returns 0 in the child process while pid in the parent process  
wait() returns the pid and copies the exit status  
exec() loads a file and executes it  

file descripter refers to a file.  
fork() and exec()let the shell has the chance to redirect child's I/O  
File descripter is a good abstraction  

Pipe is a small kernel buffer conncting files.  
Pipe: auto-clean, pass long data, allow parallel execution  

Underlying file is called inode  
cd is built-in, if not, the shell will form a infinity process tree of dir-changing  

ALL XV6 PROGRAMS RUN AS ROOT!!!!!  

## Amusement: UNIX

In order to avoid large amount of team population and project cost, we need to build a good programming environment. UNIX is for building such an environment.  
PROBLEM: Large number of softwares, large scale of jobs  
Software is always demanded for different operations, to avoid rewrite code per annum, the code should be divided into modules.
Layers: kernel, shell, util  
A show of pipes  
Easy and packed pattern-matching algorithms  
Refer file as a simple sequence of bytes, file is contained in directories  
Concept of I/O redirection  
C: If you need to avoid hardware, simply use it; but also ways to interact with hardware directly  
An example of a software, inputting boolean equation and outputting circuit traces  
Computer is built for easy to use  

## Lecture 1

What would a kernel do?  
Everyone thought OS provides what they already know  
Many tensions occurred in OS design  
Func call vs Syscall? func hasn't previlege to access real CPU and mem but syscalls have  
High-level language is designed to be portable  
There is a rule that we read from fd 0 and write to fd 1  
Kernel holds a process table and guided by fd  
Instruction ecall passes the system from user space to the kernel space  
Parent and child process shares the same fd  
exec() loads the program in the file and throw away current process  
exec() remains the fd that already exists  
wait() waits the child process to return, if the process hasn't child process, it'll return -1  

## Just before Lab util...

installing the riscv64-unknown-elf-gcc:  
```shell
sudo apt install gcc=riscv64-unknown-elf
```