---
title: "assembly-related"
date: 2025-02-26T20:49:18+08:00
draft: false
tags: ["COA"]
---

# asm & syscalls

move value into register:
```assembly
mov rax, 60
```
dereference a pointer:
```assembly
mov rdi, [1337000]
```

let the asm program executable:
```assembly
.intel_syntax noprefix
.global _start
_start:
<asm_code>
```

exit():
```assembly
mov rdi, <exit_code>
mov rax, 60
syscall
```

write():
```assembly
mov rdi, <1_or_2>
mov rsi, <memory_addr>
mov rdx, <bytes>
mov rax, 1
syscall
```

read():
```assembly
mov rdi, 0
mov rsi, <memory_addr>
mov rdx, <bytes>
mov rax, 0
syscall
```
