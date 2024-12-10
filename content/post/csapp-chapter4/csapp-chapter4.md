---
title: "Csapp Chapter4"
date: 2024-12-10T16:44:47+08:00
draft: false
---

## 4.1

### prelude

PC holds the address of the instruction that currently executed.

registers, base pointer, stack pointer

condition code: ZF, SF, OF

memory(monolithic byte array)

Types of encoding the Y-86 instructions:

I-type, D-type and B-type

### instructions

movq is split: I, R, M -> R, M

AL instructions: addq, subq, andq, xorq (as there is no zero register in Y-86 processor, you must add an xorq instruction)

jumps: jmp, <=, <, ==, !=, >=, >

cond move: <=, <, ==, !=, >=, >

halt: stops the program

### encode

code + function

store in a small RAM

no ambiguity when encoding the instructions

Y86-64 is CISC-like for its various length of instructions and RISC-like for its single load/store encoding

### exceptions

AOK, HLT, ADR, INS

shut whenever not AOK

### run a code 

addq needs another register but subq can also set the stat code

pushq and popq's details may largely reduce the code portability, problem may occur when subq compares rsp and the pushed value, or pop the stack pointer, undefined behavior always annoy people anytime anywhere.

## 4.2

### components

combinational logic, memory elements, clock signals(sequential logic is split into two parts)

similarity: Verilog -> C, VHDL -> Ada

HCL == block:

``bool eq = (a && b) || (!a && !b)``

HCL MUX block:

``bool out = (s && a) || ()``

word level: using cases

```
word Out = [
	s: A;
	1: B;
	];
```

### sequential logic

reg & RAM

state changes only when clock rises

simple regfile:

valA, valB, srcA, srcB, dstW, valW

in the Y86 processor: a memory with 2 ports, one for R inst, the other for R/W data

## 4.3

### stages of instructions

#### Fetch

PC = memaddr

valP = PC + len(inst)

#### Decode

read operands from reg file

#### Execute

ALU performs instruction or incr/decr the stack pointer, for other instruction, similar

#### Memory

W/R data in mem

#### Write back

write back to register

#### PC update

PC stores addr of next instruction

cost of adding hardware > cost of copying code in software

process similar instructions in same manners

### Hardware

Fetch: PC computes valP, the incremented PC.

Decode: read valA and valB

Execute: ALU calculates integer or increments the stack pointer, decides whether jump or not.

Memory: read or write a word of mem

Write Back: write back from ALU or mem

PC update

only start next cycle when clock rises

processor never read back

eg: no instruction will both set and read the cond code

eg: stack pointer

fetch: fetching 6 bytes and generate instruction fields.

decode & write back: generate register identifiers, serve necessary data.

execute: ALU performs as the calculator or just the adder.

memory: write or read memory values.

PC increment: depend on instruction code or branch or not.

### Survey

the clock must run slow enough to suit the single-cycle.

the parts of the processor only active for a single part of time in the clock cycle

## 4.4

### pipelining

divide into a series of discrete stages

increase the throughput of a system, but slightly increase the latency

nonpipelined: a logic performs the computation with a register hold the result of the computation

latency: the total time to perform a single instruction

slowing the clock won't change the behavior of a pipeline.

different clock delays of different instructions influences the pipeline,

due to the delay, pipeline doesn't always have larger throughput with more stages on each instruction

logical dependencies limits the performance of pipeline

