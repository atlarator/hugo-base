---
title: "《算法竞赛》第三章笔记"
date: 2025-01-06T10:04:13+08:00
draft: false
tags: ["OI"]
---

## BFS与DFS

BFS = 队列，访问第i层节点时，第i层节点出队，第i+1层节点入队  
DFS = 递归  
DFS的常见操作：DFS时间戳，DFS序列二次输出，产生树的深度，产生子树节点总数  
先序遍历，中序遍历，后序遍历  
DFS框架：
```
ans;
void dfs(层数, 其他参数){
    if(出局判断){
        更新答案;
        return;
    }
    (剪枝)
    for(下一层可能的枚举情况)
        if(used[i] == 0){
            used[i] = 1;
            dfs(层数+1, 其他参数);
            used[i] = 0;
        }
    return;
}
```

对比：BFS可能耗费大量空间，DFS可能搜索大量无效节点，DFS适合用于寻找任意可行解，BFS适合用于寻找范围最优解，DFS代码量远小于BFS  
BFS常使用map和set进行去重，DFS常使用剪枝进行优化  

连通性判断
例：lanqiao178，遍历一个连通块，再遍历下一个连通块，直至遍历完所有的连通块，统计共有多少个连通块  
DFS：搜索周围所有的#，把搜索过的#进行标记，统计没有高地的岛屿数量  

## 剪枝

BFS判重：相同的点只处理一次  
例：lanqiao642，使用STL的map与set判重  

例：hdu1010: 奇偶判断，可行性剪枝  
先使用可行性：剪掉k>T, 剪掉tmp=T-k-(abs(c-x)+abs(d-y))  
奇偶剪枝：本题在tmp为奇数情况下必定无解  
使用曼哈顿距离判断两格是否可达  
优化搜索顺序，排除等效冗余  

## 洪水填充
从种子点出发在封闭区域内进行涂色  
DFS示例：
```C++
void floodfill(int x, int y, int fillColor, int oldColor)
{
    if(check(x, y) == true && color(x, y) == oldColor)
    {
        setColor(x, y, fillColor);
        floodfill(x + 1, y, fillColor, oldColor);
        floodfill(x - 1, y, fillColor, oldColor);
        floodfill(x, y - 1, fillColor, oldColor);
        floodfill(x, y + 1, fillColor, oldColor);
    }
}
```
时间、空间复杂度均为O(n^2)  

## BFS最短路径
相邻两个点距离相等时使用BFS计算最短路径时间复杂度为O(m)  
例：lanqiao602，字典序D< L < R < U，使用BFS进行迷宫寻路  
标准方法：存储路径上每一个节点的前驱节点  
邻居节点间距离不同时应使用Dijkstra算法  

## 双向广搜

有确定的起点和终点，分别从起点和终点出发进行相遇  
下一层节点数增加越快，双向广搜效率改善越强  
一个队列为空之前，若相遇，则有解，若一个队列为空之后还没有相遇则无解  

## BFS与优先队列

本质上是Dijkstra算法  
从起点开始逐层扩展邻居，扩展完成后弹出最小边权的边  
总复杂度为O((n+m)logn)
例：lanqiao1122  

## BFS与双端队列

高效解决边权只可能是0或1的问题  
例：洛谷P4467
建模为最短路径问题，不需要旋转则边权为0，需要旋转则边权为1  

扩展边权为0的邻居点时看作同一层，从队头插入，边权为1的边扩展时看作下一层的点，从队尾插入  
起点放入队列，弹出队头，扩展直连邻居，重复以上过程直至队列为空  

不允许节点重复入队，没有排序过程，时间复杂度为O(n)  

## A*算法

给定一个确定的起点与确定（或可预测）的终点，求起点到终点的最短路径  
贪心搜索：只看终点，不看起点，不回头重新选择，不能提前绕开障碍  
Dijkstra：只管起点，不管终点，遍历几乎所有的点就可以得到最终的结果  
(TODO)