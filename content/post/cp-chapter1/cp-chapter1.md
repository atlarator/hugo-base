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

```C++
#include<bits/stdc++.h>
using namespace std;
int h[100000], ans[100000];
int main()
{
	stack<int> s;
	int n; scanf("%d", &n);
	for(int i = 1; i <= n; i++)
	{
		scanf("%d", &h[i]);
	}
	for(int i = n; i >= 1; i--)
	{
		while(!s.empty() && h[s.top()] <= h[i])
			s.pop();
		if(s.empty())
			ans[i] = 0;
		else
			ans[i] = s.top();
		s.push(i);
	}
	for(int i = 1; i <= n; i++)
		printf("%d\n", ans[i]);
	return 0;
}
```

### 哈夫曼编码

例题：poj 1521

```C++
#include<bits/stdc++.h>
using namespace std;

int main()
{
	priority_queue<int, vector<int>, greater<int>> q;
	string s;
	while(getline(cin, s) && s != "END")
	{
		sort(s.begin(), s.end());
		int num = 1;
		for(int i = 1; i <= s.length(); i++)
		{
			if(s[i] != s[i - 1])
			{
				q.push(num);
				num = 1;
			}
			else
				num++;
		}
		int ans = 0;
		if(q.size() == 1)
			ans = s.length();
		while(q.size() > 1)
		{
			int a = q.top(); q.pop();
			int b = q.top(); q.pop();
			q.push(a + b);
			ans += a + b;
		}
		q.pop();
		printf("%d %d %.1f\n", 
		s.length() * 8, 
		ans, 
		(double)s.length() * 8 / (double)ans);
	}
	return 0;
}
```

### 优先队列与堆

使用priority_queue可以避免手动管理堆的实现代码

例题：洛谷P3378

```C++
#include<bits/stdc++.h>
using namespace std;

int main()
{
	priority_queue<int, vector<int>, greater<int>> q;
	int n; scanf("%d", &n);
	while(n--)
	{
		int op; scanf("%d", &op);
		if(op == 1)
		{
			int x;
			scanf("%d", &x);
			q.push(x);
		}
		else if(op == 2)
		{
			printf("%d\n", q.top());
		}
		else
			q.pop();
	}
	return 0;
}
```

重点是中间的`priority_queue<int, vector<int>, greater<int>> q;`