---
title: "《算法竞赛》第一章笔记"
date: 2024-12-11T10:17:29+08:00
draft: false
tags: ["OI"]
---

### 单调栈

栈中元素从栈顶到栈底依次递增  
一个新数字进栈前要弹出所有比它小的数，所有数字都入栈  
例题：洛谷P2947

### 哈夫曼编码

原理略  
例题：poj 1521

### 优先队列与堆

使用priority_queue可以避免手动管理堆的实现代码  
例题：洛谷P3378  
重点是中间的`priority_queue<int, vector<int>, greater<int>> q;`