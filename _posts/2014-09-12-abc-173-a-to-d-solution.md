---
title: AtCoder Beginner Contest 173 A~D题解
author: GoodCoder666
layout: post
---

@[TOC]
# [A - Payment](https://atcoder.jp/contests/abc173/tasks/abc173_a)
## 题目大意
如果使用价值$1000$元的纸币（假设有）支付$N$元，服务员会找多少钱？

$1\le N\le 10000$
## 输入格式
$N$
## 输出格式
一行，即服务员找的钱数。
## 样例
| 输入 | 输出 |
| ---- | ---- |
| 1900 |  100 |
| 3000 |   0  |
## 分析
特判：
如果$N$除以$1000$能整除，那么不需要找钱，输出$0$；
如果有余，输出$1000 - (n~mod~1000)$。

## 代码
```cpp
#include <cstdio>
using namespace std;

int main(int argc, char** argv)
{
	int n;
	scanf("%d", &n);
	if(n % 1000 == 0) puts("0");
	else printf("%d\n", 1000 - n % 1000);
	return 0;
}
```
***
# [B - Judge Status Summary](https://atcoder.jp/contests/abc173/tasks/abc173_b)
## 题目大意
某人完成了某道算法竞赛题，有`AC`、`WA`、`TLE`、`RE`四种结果（*status*）。题目有$N$个测试样例，测试结果分别为$S_1, S_2, ..., S_N$，请分别统计并输出`AC`、`WA`、`TLE`、`RE`的个数。（格式详见输出格式）

$1\le N\le 10^5$
$S_i$是`AC`、`WA`、`TLE`或`RE`。
## 输入格式
$N$
$S_1$
$S_2$
$:$
$S_N$
## 输出格式
```
AC x [AC的个数]
WA x [WA的个数]
TLE x [TLE的个数]
RE x [RE的个数]
```
**注意：这里的“乘号”不是“×”，而是英文字母“x”！**
## 样例
### 样例输入1
```c
6
AC
TLE
AC
AC
WA
TLE
```
### 样例输出1
```c
AC x 3
WA x 1
TLE x 2
RE x 0
```
`AC`、`WA`、`TLE`、`RE`分别有$3, 1, 2, 0$个。
### 样例输入2
```c
10
AC
AC
AC
AC
AC
AC
AC
AC
AC
AC
```
### 样例输出2
```c
AC x 10
WA x 0
TLE x 0
RE x 0
```
他全都`AC`了……
## 分析
要统计个数，可以用别的方法，但我个人喜欢偷懒，使用了`map`（似乎大材小用了……）
## 代码
```cpp
#include <iostream>
#include <string>
#include <map>
using namespace std;

map<string, int> cnt;

int main(int argc, char** argv)
{
	ios::sync_with_stdio(false);
	int n;
	cin>>n;
	for(int i=0; i<n; i++)
	{
		string s;
		cin>>s;
		cnt[s] ++;
	}
	cout<<"AC x "<<cnt["AC"]<<endl;
	cout<<"WA x "<<cnt["WA"]<<endl;
	cout<<"TLE x "<<cnt["TLE"]<<endl;
	cout<<"RE x "<<cnt["RE"]<<endl;
	return 0;
}
```
***
# [C - H and V](https://atcoder.jp/contests/abc173/tasks/abc173_c)
## 题目大意
给定$H$行$W$列的方格。在第$i$行$j$列（$1\le i\le H$, $1\le j\le W$）的方格是$c_i$$_,$$_j$。它可能是`#`（黑色）或`.`（白色）。
可以选某些行和列（都可以不选），将行和列上的方格全部涂成红色。

给定整数$K$。有多少种选法使图中只剩$K$个黑方格？

$1\le H, W\le 6$
$1\le K\le HW$
$c_i$$_,$$_j$是`#`或`.`。
## 输入格式
$H~W~K$
$c_1$$_,$$_1$ $c_1$$_,$$_2$ $..$ $c_1$$_,$$_W$
$c_2$$_,$$_1$ $c_2$$_,$$_2$ $..$ $c_2$$_,$$_W$
$:$
$c_H$$_,$$_1$ $c_H$$_,$$_2$ $..$ $c_H$$_,$$_W$
## 输出格式
一行，即符合条件的选法数量。
## 样例
### 样例输入1
```c
2 3 2
..#
###
```
### 样例输出1
```c
5
```
有五种方法：
- 第$1$行和第$1$列
- 第$1$行和第$2$列
- 第$1$行和第$3$列
- 第$1$、$2$列
- 第$3$列
### 样例输入2
```c
2 3 4
..#
###
```
### 样例输出2
```c
1
```
只有一种方法：啥也不干！
### 样例输入3
```c
2 2 3
##
##
```
### 样例输出3
```c
0
```
无解。
### 样例输入4
```c
6 6 8
..##..
.#..#.
#....#
######
#....#
#....#
```
**博主提示：这是最大的数据，如果程序在本地运行此样例没有超时，则提交后不会TLE！**
### 样例输出4
```c
208
```
## 分析
本题没有巧妙的方法，且数据范围较小，所以使用二进制法枚举行和列。
因为输入颜色只有黑或白，所以题目中“涂成红色”只要涂成白色（`.`）即可。
## 代码
```cpp
#include <cstdio>
#include <cstring>
#define maxn 6
using namespace std;

char c[maxn][maxn];
int h, w, k;
int cnt = 0;

int main(int argc, char** argv)
{
	scanf("%d%d%d", &h, &w, &k);
	for(int i=0; i<h; i++)
		scanf("%s", c[i]);
	int m1 = 1 << h, m2 = 1 << w, ans = 0;
	for(int hs=0; hs<m1; hs++)
		for(int ws=0; ws<m2; ws++)
		{
			char tmp[maxn][maxn]; // 不能修改原数组，所以复制一个数组
			memcpy(tmp, c, sizeof(c));
			for(int i=0; i<h; i++) // 行
				if(hs & (1 << i)) for(int j=0; j<w; j++) // 列
					tmp[i][j] = '.';
			for(int i=0; i<w; i++) // 列
				if(ws & (1 << i)) for(int j=0; j<h; j++) // 行
					tmp[j][i] = '.'; // 注意：绝对不能写成tmp[i][j]!
			int cnt = 0;
			for(int i=0; i<h; i++)
				for(int j=0; j<w; j++) if(tmp[i][j] == '#')
					cnt ++;
			if(cnt == k) ans ++;
		}
	printf("%d\n", ans);
	return 0;
}
```
***
# [D - Chat in a Circle](https://atcoder.jp/contests/abc173/tasks/abc173_d)
## 题目大意
有$N$个人要在某地会和，编号为$i$的人的**友情值**为整数$A_i$。这些人要围成一圈，每人到达该地后会在某个位置插入圈子。每个人的**舒适度**是入圈时相邻两个人的友情值中较小的一个（第一个人的舒适度为$0$）。现在，由你决定他们的入圈顺序和插入位置，问：$N$个人的最大舒适度之和是多少？

$2\le N\le 2×10^5$
$1\le A_i\le 10^9$
## 输入格式
$N$
$A_1~A_2~...~A_N$
## 输出格式
一行，即$N$个人的最大舒适度之和。
## 样例
### 样例输入1
```c
4
2 2 1 3
```
### 样例输出1
```c
7
```
最大的舒适度之和为$0+3+2+2=7$。
![舒适度](https://img-blog.csdnimg.cn/20200719222459799.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_27,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dyaXRlXzFtX2xpbmVz,size_16,color_FFFFFF,t_70#pic_center)
### 样例输入2
```c
7
1 1 1 1 1 1 1
```
### 样例输出2
```c
6
```
## 分析
贪心算法。先从大到小排序（这是顺序），再将每个人在舒适度最大的地方入圈。
`priority_queue`搞定。
## 代码
```cpp
#include <cstdio>
#include <queue>
#include <algorithm>
#define maxn 200005
using namespace std;

int a[maxn], n;

struct Position
{
	int lf, rf, comfort;
	inline Position(int l, int r)
	{
		lf = l, rf = r;
		comfort = min(l, r);
	}
	inline bool operator<(const Position& p) const
	{
		return comfort < p.comfort;
	}
};

priority_queue<Position> q;

inline bool cmp(int x, int y) {return x > y;}

int main(int argc, char** argv)
{
	scanf("%d", &n);
	for(int i=0; i<n; i++)
		scanf("%d", a + i);
	sort(a, a + n, cmp);
	q.push(Position(a[0], a[1]));
	long long res = a[0];
	for(int i=2; i<n; i++)
	{
		Position p = q.top();
		if(q.size() > 1) q.pop();
		res += p.comfort;
		q.push(Position(a[i], p.lf));
		q.push(Position(a[i], p.rf));
	}
	printf("%lld\n", res);
	return 0;
}
```
