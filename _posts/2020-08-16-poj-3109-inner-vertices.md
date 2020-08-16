---
layout: post
title: POJ 3109 Inner Vertices (坐标离散化、差分、树状数组)
categories: Computer/Algorithms
tags: [Algorithms, Competitive programming]
---

## 问题描述
一个无限平面上有 N 个黑点其余为白点，如果某个白点水平左右、垂直上下都存在黑点，那么该点会变为黑点，求最终平面有多少黑点。

## 分析
用到了[坐标离散化](https://oi-wiki.org/misc/discrete/)、[差分](https://oi-wiki.org/basic/prefix-sum/)、[树状数组](https://oi-wiki.org/ds/fenwick/)，原理不再赘述。

如果某个白点水平左右、垂直上下都存在黑点，那么该点会变为黑点，也就是垂直线段和水平线段的交点。那么只需要知道每个水平线段“划过”了多少个垂直线段，同时为了不重复计数，水平线段内、垂直线段内不包含黑点。

如何构成水平线段？一个黑点如果水平向左有黑点，那么与最近的那个黑点构成一个水平线段，记为水平最近点，用数组维护。

如何构成垂直线段？一个黑点如果垂直向上有黑点，那么与最近的那个黑点构成一个垂直线段（也就是按照横坐标排序的下一个黑点）。

如何知道水平线段划过了多少垂直线段？维护一个计数数组，记录每个纵坐标上的垂直线段总数，每个垂直线段将对应纵坐标区间的计数加 1，一个水平线段划过的垂直线段总数就是该点的计数扣除水平最近点的计数（在水平最近点左边的没有被划过，应该扣除）。这个计数数组是差分序列，因为需要对一个区间同时加 1，并且用树状数组维护，因为需要对对应纵坐标求和（对应纵坐标的计数值是差分的前缀和）。

例如，A、B 没有水平最近点，因此没有构成水平线段，C 与 B 构成水平线段划过了 `2 - 1 = 1` 条垂直线段，D 与 A 构成水平线段划过了 `2 - 0 = 2` 条垂直线段，E 与 C 构成水平线段划过了 `3 - 2 = 1` 条垂直线段。

```
y
5 .     .   .
4 | B   | C | E
3 | |   .   |
2 A |   |   D
1   .   .
0 1 2 3 4 5 6 x
```

具体过程如下：
1. 因为是无限平面，需要对坐标进行离散化。
2. 然后按照横坐标排序。
3. 遍历每个黑点：
   1. 如果该点与下一个黑点横坐标相同则构成一个垂直线段，将线段内（不含端点）对应纵坐标计数同时加 1。
   2. 如果存在水平最近点，该点纵坐标计数减去该水平最近点计数即为从该点到水平最近点划过的垂直线段数。
   3. 记录该点作为水平最近点。

## 代码
```c++
// POJ 3109 Inner Vertices
#include <cctype>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;

const int MAXN = 100000+5;
PII A[MAXN];
int N, CX, CY;
int vertical[MAXN];
int vis[MAXN];

int sum(int *bit, int i)
{
    int s = 0; for ( ; i > 0; i-=i&-i) { s += bit[i]; } return s;
}

void add(int *bit, int n, int i, int x)
{
    for ( ; i <= n; i+=i&-i) bit[i] += x;
}

void compress(PII *A, int N, int &cx, int &cy)
{
    vector<int> xs, ys;
    for (int i = 0; i < N; i++) xs.push_back(A[i].first), ys.push_back(A[i].second);
    sort(xs.begin(), xs.end()); sort(ys.begin(), ys.end());
    xs.erase(unique(xs.begin(), xs.end()), xs.end());
    ys.erase(unique(ys.begin(), ys.end()), ys.end());
    for (int i = 0; i < N; i++) {
        A[i].first = lower_bound(xs.begin(), xs.end(), A[i].first) - xs.begin() + 1;
        A[i].second = lower_bound(ys.begin(), ys.end(), A[i].second) - ys.begin() + 1;
    }
    cx = xs.size(), cy = ys.size();
}

int main()
{
    while (scanf("%d", &N) == 1) {
        for (int i = 0; i < N; i++) scanf("%d%d", &A[i].first, &A[i].second);

        compress(A, N, CX, CY); // 坐标离散化

        sort(A, A+N);
        int cnt = N;
        memset(vertical, 0, sizeof(vertical));
        memset(vis, -1, sizeof(vis));
        for (int i = 0; i < N; i++) {
            if (i+1 < N && A[i+1].first == A[i].first
                && A[i+1].second-A[i].second-1 >= 1) {// 构成垂直线段，计数 +1
                int l = A[i].second+1, r = A[i+1].second-1;
                add(vertical, CY, l, 1);
                add(vertical, CY, r+1, -1);
            }
            int curr = sum(vertical, A[i].second);
            // 从该点到水平最近点划过的垂直线段数
            if (vis[A[i].second] != -1) cnt += curr - vis[A[i].second];
            // 作为水平最近点，同时记录计数
            vis[A[i].second] = curr;
        }
        printf("%d\n", cnt);
    }
}
```

## 参考
- 《挑战程序设计竞赛（第2版）》秋叶拓哉 岩田阳一 北川宜稔 著
- [前缀和 & 差分 - OI Wiki](https://oi-wiki.org/basic/prefix-sum/)
