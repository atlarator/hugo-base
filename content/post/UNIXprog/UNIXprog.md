---
title: "程序的开始与终止"
date: 2025-03-13T17:07:52+08:00
draft: false
tags: ["Operating System"]
---

## main 函数与程序的启动

main没有原型，启用main需要一个特殊的启动流程  

```C
int
main(int argc, char *argv[]) {
    printf("main is at 0x%lX\n", (unsigned long)&main);
}
```
使用c89标准编译后的输出结果与readelf的entry point不同  
使用gdb，发现``_start``，先跳转到``___start``，启动``_exit``和``__libc_init``，然后跳入``main``  
执行完程序内所有行为后回到``___start``  
阅读``crt0.S``发现内存布局和库函数相关的初始化数据（源实验环境为NetBSD）  
在``cc``中可以使用``-e``指定程序的入口函数  
在gdb中查看``$rip``发现非法的地址访问，产生了段错误，当将这个指定的入口函数使用``exit()``退出时程序段错误消失

## 程序的终止

在C89中没有return的main返回20，而在C99中，这个返回值变成了0  
return被视为对一个变量的使用  
``exit()``退出程序时会使用``atexit()``，冲刷所有的输出流，关闭所有开放的流，断开使用``tmpfile()``建立的所有链接，最后调用``_exit()``系统调用直接关闭线程  
``atexit()``将以相反的顺序寄存所有调用``exit()``的函数  
``exit()``也会调用``exit_handler.c``进行相应的操作  
