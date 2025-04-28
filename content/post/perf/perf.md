---
title: "关于perf的应用"
date: 2025-03-09T08:31:14+08:00
draft: false
tags: ["MISC"]
---

示例：  
```shell
perf stat -B dd if=/dev/zero of=/dev/null count=1000000
```
如果不知道怎么运行，直接--help，-B是显示大数  
使用事件计数，并限制在用户态/内核态：
```shell
perf stat -e cycles:[u][k] dd if=/dev/zero of=/dev/null count=1000000
```
```shell
perf stat -e instructions:[u][k] dd if=/dev/zero of=/dev/null count=1000000
```
(TODO)