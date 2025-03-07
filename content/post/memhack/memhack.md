---
title: "玩转虚拟内存"
date: 2025-03-07T08:42:42+08:00
draft: false
tags: ["Operating System"]
---

<a href="https://github.com/VictorZhangAI/memhack">Source Code</a>

看了jyy今年的虚拟内存，有感而发给GPT丢了几个prompt，产生了这些东西：

## mmap

mmap() 是一个用于内存映射的syscall，可用于快速分配大段内存空间。
```C
void* mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
int munmap(void addr, size_t length);
```
addr为建议的映射地址（一般为NULL），length为映射的字节大小，prot为内存的保护权限，flag为映射类型，fd为映射的文件描述符，offset为文件的起始偏移量。  
其中，length和offset必须是页大小的整数倍  
```C
#include<stdio.h>
#include<stdlib.h>
#include<sys/mman.h>
#include<fcntl.h>
#include<sys/stat.h>
#include<unistd.h>
#include<string.h>

int main()
{
        int fd = open("test.txt", O_RDWR);
        if(fd < 0)
        {
                perror("open");
                return 1;
        }

        struct stat st;
        fstat(fd, &st);

        char *map = mmap(NULL, st.st_size, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
        if(map == MAP_FAILED)
        {
                perror("mmap");
                close(fd);
                return 1;
        }
        printf("file content: %s\n", map);
        strncpy(map, "Hello, mmap!", 12);
        munmap(map, st.st_size);
        close(fd);
        return 0;
}
```
这里的test.txt必须是一个有原始内容的文件。
```
Hello as an initial sentence here.
```
程序运行后，文件内容变为``Hello, mmap!initial sentence here.``  
mmap()还可以共享内存（匿名映射）：
```C
#include <stdio.h>
#include <stdlib.h>
#include <sys/mman.h>
#include <string.h>
#include <unistd.h>

int main() {
    char *shared_mem = mmap(NULL, 4096, PROT_READ | PROT_WRITE, MAP_ANONYMOUS | MAP_SHARED, -1, 0);
    if (shared_mem == MAP_FAILED) {
        perror("mmap");
        return 1;
    }

    strcpy(shared_mem, "Hello, Shared Memory!");
    printf("shared contents: %s\n", shared_mem);

    munmap(shared_mem, 4096);
    return 0;
}
```

## vvar和vdso

vvar是内核暴露给用户空间的一些只读变量，用于加速某些syscall，可以通过
```shell
cat /proc/self/maps | grep vvar
```
来获取vvar的地址。  
vdso是一个特殊的共享库，提供一些常用的syscall供用户态实现。刚才的grep后改为vdso即可查看vdso的地址空间。某种意义上，vvar和vdso使得Linux不再是纯粹的Monolithic Kernel了  
我们可以尝试禁用vdso来观察前后的syscall速度。  
```shell
echo 0 | sudo tee /proc/sys/vm/vdso_enabled 
```
在speed.c中有一段代码用于计算时间（然而这个命令在WSL上用不了，WSL的vdso似乎是阉割过的）  

## pmap

pmap可以用于显示进程的内存映射  
```shell
pmap [pid]
pmap -x [pid] # 更详细的信息
```

在pmap_test.c中有一个示范程序，编译后在另一个终端中输入  
```shell
pmap <pid_of_pmap_test>
```

进行内存布局观察。

这种观察终究是有局限性的，于是我尝试扫描整个内存空间。事实证明我低估这件事的工作量了。  

## 由系统分配的进程

在``/proc``中使用多次ls之后发现了一些一直不改变的数字，我们使用  
```shell
ps -fp [pid] | more
# 这里的一些cmd在没有more的情况下可能显示不全
```
看看它们分别是什么。别小看这件事情，某种意义上你刨一下发现这是个巨大的历史残留......  
| pid | CMD |
|---|---|
|1|/sbin/init|
|2|/init|
|7|plan9 --control-socket 7 --log-level 4 --server-fd 8 --pipe-fd 10 --log-truncate|
|107|/usr/lib/systemd/systemd-udevd|
|1219|/sbin/agetty -o -p -- \u --noclear --keep-baud - 115200,38400,9600 vt220|
|200|snapfuse /var/lib/snapd/snaps/snapd_23545.snap /snap/snapd/23545 -oro,nodev,allow_other,suid|

这个plan9出来的时候没绷住，感觉像是一位已经死了的故人突然复活了一样

## 遍历整个地址空间

在segment_full.c中我尝试遍历整个64位地址空间，这个程序花了我整整十分钟，然而结果全是invalid。  

询问后GPT建议我只扫描实际映射的内存区域：  
```C
FILE *maps = fopen("/proc/self/maps", "r");
char line[256];
while (fgets(line, sizeof(line), maps)) {
    printf("%s", line);
}
fclose(maps);
```

不行，这时候的扫描结果依然全是invalid。  
接下来就是未知领域了，我先尝试在root下运行这个程序，还是不行。主要问题在于我们不能使用并发进行提速，否则大概率会破坏映射。gpt给了另一个建议，使用mincore()进行查找。  
然而这个程序甚至把我的gdb干崩了......  
在segment_part.c中你可以找到这段程序。  
话说回来，我们看看mincore()  
```C
int mincore(void *addr, size_t length, unsigned char *vec);
```
addr为查询的起始地址，必须是页对齐的地址  
length为要查询的内存大小  
vec为输出缓冲区  
这个syscall不会触发page fault，也不会分配新的物理页  

说实话，虽然好几个程序都运行失败了，但是虚拟内存还是蛮好玩的:trollface: