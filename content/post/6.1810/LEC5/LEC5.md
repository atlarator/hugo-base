---
title: "xv6 LECTURE5"
date: 2025-01-09T21:52:22+08:00
draft: false
tags: ["XV6 OS"]
---

## GDB

step: one line of code at a time, jumps into the function  
next: jump over functions  
stepi, nexti: do same thing for asm code  
continue: run until next breakpoint  
finish: runs until the function ends  

break: set a breakpoint  
watch: stop whenever value changes  
x: prints the raw memory  
print: prints the expression  
info registers: print value of registers  
info frame: prints current stack frame  

set: change value of a variable  

## Handout 5

overwrite return address will cause an infinite loop  

## Calling Conventions

int in RISC-V is always 32-bit wide, while long is 32-bit wide in RV32 and 64-bit wide in RV64  
in RV64, int is sign-extended  
struct: pass first 8 pointer-words into register  
twice size as a pointer word: use an even-odd pair; more than twice: pass by reference  

## Lecture 5

(TODO)