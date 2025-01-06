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

连通性判断(TODO)