---
title: "C++的那些事"
date: 2025-09-14T02:59:24Z
draft: false
tags: ["PL"]
---

### 内部细节

能const auto &就const auto &，能引用就引用

vector：分配一大块连续的内存空间，每次空间不够用的时候把原有的空间翻倍进行扩容（这个过程中原有的元素会重新移位），对前插不友好故没有前插的实现
deque：分配一些较为平均且碎片化的空间并向这些空间的首个元素设置索引指针。每次插入时如果空间不够就再分配一小块空间，故对前插和后插都很友好，但是比vector慢
map：把每对键-值作为节点的一棵红黑树，所以map的key必须实现了＜的运算符重载
set：本质上是没有key的map
unordered_map：哈希表，所以key必须是可哈希化的。当load factor过大时会进行rehash重新分配哈希值
array：固定的连续空间
list：双链表（对，它的内部实现就是你理解的那样）

多使用++it而不是it++，因为前缀是原地迭代，而后缀是先拷贝后迭代

unique_ptr：不得拷贝
shared_ptr：可以共享资源且拥有一个计数器
weak_ptr：可以共享资源，只能和shared_ptr一起使用防止循环引用

### C++11的新增内容：

auto类型推导，range-based for，右值引用&&和std::move()，constexpr，使用{}统一初始化，nullptr，enum class用于强类型枚举，无关操作系统的线程与并发库\<thread\>,\<future\>,\<mutex\>，可变参数模板，静态断言，控制特殊成员函数，更严格的对象语义检查，tuple，array，chrono...


