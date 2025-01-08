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
(TODO)