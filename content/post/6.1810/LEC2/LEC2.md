---
title: "xv6 LECTURE2"
date: 2025-01-03T22:12:02+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 2

memory layout:  
text: code, read-only data  
data: global C vars  
stack: local variables  
heap: sbrk, malloc, free  
.c -compile-> .o -link-> executable file  
strlen() uses array access while strcmp() uses pointer access  
kalloc() keeps a large linked list of free pages of memory  
LRU buffer cache is implemented as a dual-pointer linked list  
static: limited to the file where the variable is declared  

## Slides

This class focus on RAM and I/O  
address space: represent bus as a giant array of data  
memory allocation: decide where array to store things  
A stack is much more smaller than heap  

## K&R 
(TODO)