---
title: "ASM in C"
date: 2025-01-16T08:15:22+08:00
draft: false
tags: ["MISC"]
---

## 基础
asm是一个GNU C扩展语法的关键词，当想使用ANSI C进行编译的时候使用__asm__代替asm  
C++中默认拥有asm关键字，而当编译时使用 -fno-asm flag 时应当使用__asm__进行内联汇编操作  

asm默认拥有volatile属性，使用inline修饰时编译器会尝试缩小asm的编译结果占用  
编译器不进行asm的token分析，为在一个asm语句中分多句汇编进行书写可采用``\n\t``分割  
有些特殊的汇编语言也可采用``;``进行分割  

## 使用方法

使用goto进行修饰时表名汇编可能进行地址跳转  
```C
int src = 1;
int dst;   
//copy src to dst and add 1 to dst
asm ("mov %1, %0\n\t"
    "add $1, %0"
    : "=r" (dst) 
    : "r" (src));

printf("%d\n", dst);
//dst = 2
```

gcc会在发现不需要汇编代码输出的时候忽视asm，同理，在for循环总是输出相同结果时拆循环优化  
特殊的，i386汇编在进行断言检测时无法被忽略  
使用volatile可以避免这种优化保证代码在优化下依然照常执行  
手动加入变量依赖也可以避免优化  

asmSymbolicName可以让内联汇编代码可读性更强  
FlagOutput用于进行比较  
为防止覆写，asm设置了clobber register，可在asm中随意使用  
cc: flag覆写  
memory: 内存读写  
redzone: 汇编代码会写入红区  

```C
// *z++ = *x++ * *y++ 
asm ("vecmul %0, %1, %2"
     : "+r" (z), "+r" (x), "+r" (y), "=m" (*z)
     : "m" (*x), "m" (*y));
```

```C
//an example of asm goto
asm goto (
    "btl %1, %0\n\t"
    "jc %l2"
    : /* No outputs. */
    : "r" (p1), "r" (p2) 
    : "cc" 
    : carry);

return 0;

carry:
return 1;
```

OperandModifier: 通用类型：  
c: constant; cc: stronger %c; n: %c that expects value negated; a: memory reference; l: label printing  

（相关的专用寄存器见官方文档）  

## 更名
```C
//变量更名
int foo asm("myfoo") = 2
//函数更名
int func(int x, int y) asm ("MYFUNC");
```

## 寄存器关联
与全局寄存器关联
```C
register int *foo asm ("r12");
```

### 空间占用

不保证过长的跳转或在汇编宏过大时不进行优化处理  

## Reference
<a href="https://gcc.gnu.org/onlinedocs/gcc/Using-Assembly-Language-with-C.html">
GCC assembly</a>