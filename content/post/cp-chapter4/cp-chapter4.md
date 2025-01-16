---
title: "《算法竞赛》第四章笔记"
date: 2025-01-13T14:20:50+08:00
draft: false
tags: ["OI"]
---

## 并查集

合并优化与路径压缩  
初始化：每个点属于独立的集  
合并，查找，统计集的个数  
例：hdu1213，直接使用并查集模板即可  
合并优化
```C++
int height[N];
void init_set()
{
    for(int i = 1; i <= N; i++)
    {
        s[i] = i;
        height[i] = 0;
    }
}
void merge_set(int x, int y)
{
    x = find_set(x);
    y = find_set(y);
    if(height[x] == height[y])
    {
        height[x] = height[x] + 1;
        s[y] = x;
    }
    else
    {
        if(height[x] < height[y]) s[x] = y;
        else s[y] = x;
    }
}
```

路径压缩：在返回时将i所属的集全部归到根节点  

```C++
int find_set(int x)
{
    if(x != s[x]) s[x] = find(s[x]);
    return s[x];
}
```

例：hdu3038，直接将序列抽象为并查集；  
poj1182，d(A->C) = (d(A->B) + d(B->C)) % 3  

## 树状数组

树状数组和线段树的共同目的是在动态数组中维护前缀和  
节点上tree[]的值为树下直连子节点之和  
lowbit(x)=x&-x，用于找出二进制中的最后一个1  
tree[x]存储的是区间上  
简单应用：单点修改+区间查询  
```C++
#include<bits/stdc++.h>
using namespace std;

const int N = 1000;
#define lowbit(x) ((x) & - (x))
int tree[N] = {0};

void update(int x, int d)
{
    while(x <= N)
    {
        tree[x] += d;
        x += lowbit(x);
    }
}

int sum(int x)
{
    int ans = 0;
    while(x > 0)
    {
        ans += tree[x];
        x -= lowbit(x);
    }
    return ans;
}

int a[11] = {0, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13};

int main()
{
    for(int i = 1; i <= 10; i++)
        update(a[i]);
    cout << "old: [5, 8]=" << sum(8) - sum(4) << endl;
    update(5, 100);
    cout << "new: [5, 8]=" << sum(8) - sum(4) << endl;
    return 0;
}
```

使用update()初始化数组元素，使用sum()计算前缀和，修改元素，时间复杂度均为log(2n)  

例：hdu1556，使用树状数组进行单点修改反而会增加复杂度，应当将单点修改改为区间修改  
使用差分数组可进行区间修改  
对差分数组D进行如下操作：
<ul>
<li>D[L] + d</li>
<li>D[R+1] - d</li>
<li>使用差分数组前缀和进行求和操作</li>
</ul>
例：洛谷P3372，区间修改+区间查询，使用二阶树状数组  
a1+a2+...+ak = kΣDi - Σ(i-1)Di  
求前缀和使用两个树状数组处理  

树状数组的扩展应用：  
二维区间修改+区间查询  
例：洛谷P4514
(TODO)
