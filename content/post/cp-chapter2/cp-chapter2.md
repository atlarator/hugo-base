---
title: "《算法竞赛》第二章笔记"
date: 2025-01-02T16:43:11+08:00
draft: false
tags: ["OI"]
---

## 尺取法

### 反向

```C++
for(int i = 0, j = n - 1; j > i; i++, j--){}
```

### 同向
例：寻找区间和：输入正整数n，数组a，正整数s，输出所有可能的a中两数字加和等于n的情况
```C++
#include<bits/stdc++.h>
using namespace std;
int a[1000];

void findsum(int *a, int n, int s)
{
	int i = 0, j = 0;
	int sum = a[0];
	while(j < n)
	{
		if(sum >= s)
		{
			if(sum == s) printf("%d %d\n", i, j);
			sum -= a[i];
			i++;
			if(i > j) { sum = a[i]; j++; }
		}
		if(sum < s) {j++; sum += a[j];}
	}
}

int main()
{
	int n; cin >> n;
	for(int i = 0; i < n; i++)
	{
		scanf("%d", &a[i]);
	}
	int s; cin >> s;
	findsum(a, n, s);
	return 0;
}
```

### 多指针

两个指针不够用时使用多指针进行优化

例：给出一串数及数字C，要求计算出所有符合A-B=C的数对有几对（不同位置的相同数值看作两个数字）

```C++
#include<bits/stdc++.h>
#define ll long long
using namespace std;
const int N = 2e5 + 5;
int a[N];
int main()
{
	int n, c; cin >> n >> c;
	for(int i = 1; i <= n; i++) cin >> a[i];
	sort(a + 1, a + 1 + n);
	ll ans = 0;
	for(int i = 1, j = 1, k = 1;
	i <= n;
	i++)
	{
		while(j <= n && a[j] - a[i] < c) j++;
		while(k <= n && a[k] - a[i] <= c) k++;
		if(a[j] - a[i] == c && 
		a[k - 1] - a[i] == c &&
		k - 1 >= 1) ans += k - j;
	}
	cout << ans;
	return 0;
}
```

## 二分法

一般的二分查找
```C++
int bin_search(int *a, int n, int x)
{
    int left = 0, right = n;
    while(left < right)
    {
        int mid = (left + right) / 2;
        if(a[mid] >= x) right = mid;
        else left = mid + 1;
    }
    return left;
}
```

整数二分法的典例：最大值最小化和最小值最大化

例：洛谷P1824（我甚至花了半个小时才看懂这道题要求啥），使用贪心法时间复杂度为平方，发现dis有上下界，可以使用二分法求解，时间复杂度为O(nlogn)

实数范围的二分法：没有取整问题，只需要验证right - left > eps即可  
实数二分例题：POJ 3122

三分法：缩小区间可使用三等分法（mid1和mid2均为三等分点）与近似三等分法（算中点，然后±eps算出mid1和mid2）  
例题：洛谷P3745

## 倍增法
例题：洛谷P4155，同时涉及化圆为线，贪心法，二分法，倍增法  
这里定义go[s][i]为从第s个区间出发，走2^i个最优区间后到达的区间  
go[s][i] = go[go[s][i-1]][i-1]  
从s起跳，先跳2^(i-1)步到达z=go[s][i-1]  
go[go[s][i-1]][i-1]=go[z][i-1]，再从z跳2^(i-1)步到区间go[z][i-1]  

ST算法：最值查询问题  
原理：一个大区间若能被两个小区间覆盖，那么大区间的最值等于两个小区间的最值  
定义dp[s][k] = min(dp[s][k-1], dp[s+1<<(k-1)][k-1])  
例题：洛谷P2880

## 前缀和与差分

### 一维
sum[n] = Σ(a[0]~a[n])  
差分是前缀和的逆运算  
例题：hdu 1556

### 二维
D[i][j]=a[i][j]-a[i-1][j]-a[i][j-1]+a[i-1][j-1]  
```C++
#include<bits/stdc++.h>
using namespace std;
int D[5000][5000];
int main()
{
	int n, m;
	scanf("%d%d", &n, &m);
	while(m--)
	{
		int x1, x2, y1, y2;
		scanf("%d%d%d%d", &x1, &y1, &x2, &y2);
		D[x1][y1] += 1;
		D[x2+1][y1] -= 1;
		D[x1][y2+1] -= 1;
		D[x2+1][y2+1] += 1;
	}
	for(int i = 1; i <= n; ++i)
	{
		for(int j = 1; j <= n; ++j)
		{
			D[i][j] += D[i-1][j] + D[i][j-1] - D[i-1][j-1];
			printf("%d ", D[i][j]);
		}
		printf("\n");
	}
	return 0;
}
```
### 三维
建议直接求前缀和，不写递推公式  
```C++
D[x1][y1][z1] += d;
D[x2 + 1][y1][z1] -= d;
D[x1][y1][z2 + 1] -= d;
D[x2 + 1][y1][z2 + 1] += d;
D[x1][y2 + 1][z1] -= d;
D[x2 + 1][y2 + 1][z1] += d;
D[x1][y2 + 1][z2 + 1] += d;
D[x2 + 1][y2 + 1][z2 + 1] -= d;
```

## 离散化
用数字的相对值取代它们的绝对值  
离散化：排序，离散化，归位  
STL中的lower_bound()和unique()可实现离散化
```C++
#include<bits/stdc++.h>
using namespace std;
const int N = 500010;
int olda[N];
int newa[N];
int main()
{
	int n; scanf("%d", &n);
	for(int i = 1; i <= n; i++)
	{
		scanf("%d", &olda[i]);
		newa[i], olda[i];
	}
	sort(olda + 1, olda + 1 + n);
	int cnt = n;
	//cnt = unique(olda + 1, olda + 1 + n) - (olda + 1);
	for(int i = 1; i <= cnt; i++)
	{
		newa[i] = lower_bound(olda + 1, olda + 1 + n, newa[i]) - olda;
	}
	for(int i = 1; i <= cnt; i++)
	{
		printf("%d", newa[i]);
	}
	printf("\n cnt = %d", cnt);
	return 0;
}
```
## 排序与排列
排序略  
排列：next_permutation()只输出比自己大的排列，排序范围同sort()  
```C++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	string test = "abcd";
	sort(test.begin(), test.end());
	do 
	{
		cout << test << endl;
	}while(next_permutation(test.begin(), test.end()));
	return 0;
}
```
自写全排列基于DFS

## 分治法
将大问题分解为k个规模类似的小问题  
例：汉诺塔
```C++
#include<bits/stdc++.h>
using namespace std;
int sum = 0, m;
void hanoi(char x, char y, char z, int n)
{
	if(n == 1)
	{
		sum++;
		if(sum == m) 
			cout << "#" << n << ":" << x << "->" << z << endl;
	}
	else
	{
		hanoi(x, z, y, n - 1);
		sum++;
		if(sum == m) 
			cout << "#" << n << ":" << x << "->" << z << endl;
		hanoi(y, x, z, n - 1);
	}
}
int main()
{
	int n; cin >> n >> m;
	hanoi('A', 'B', 'C', n);
	cout << sum << endl;
	return 0;
}
```

逆序对问题：树状数组或者归并排序  
(TODO)