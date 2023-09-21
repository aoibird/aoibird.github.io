---
layout: post
title: 禁忌搜索
categories: Computer/Algorithms
tags: ["Tabu search", "Travelling Salesman Problem (TSP)", "Anime"]
description: 禁忌搜索——一个可用于旅行商问题的搜索方法
---

## 什么是禁忌搜索
禁忌搜索是一种启发式搜索方法，是由 Fred W. Glover 在 1986 年提出的，它可以用于解决旅行商问题，“Tabu”的意思就是禁忌、禁止。搜索通常会因为在只在局部选取最优路径，而陷入局部最优解。因此禁忌搜索引入列两条规则：
1. 如果本次行动没有得到更好的解，那么更差的解是可以接受的；
2. 之前搜索过的解将会被“禁止”。

由于禁忌搜索是比较高层次的启发式方法，有很多不同种的机制可以用于禁忌搜索：第一种短期记忆，最近访问过的解将不会被访问；第二种中期记忆，设定某些规则让搜索偏向更有希望的区域；第三种长期记忆，当搜索陷入局部最优解时可以跳出。


## 求解旅行商问题

以实现带短期记忆的禁忌搜索为例，以下代码可以实现基本的旅行商问题的求解：

```c++
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;

struct Edge {
    int to, cost;
    Edge() : to(0), cost(0) {}
    Edge(int t, int c) : to(t), cost(c) {}
};

const int MAX_ITERS = 2000;
const int MAXL = 10;
const int MAXN = 2000 + 2;
const int INF = 1e9;
vector<vector<int>> G;
deque<vector<int>> tabu;
int N, M;

void input()
{
    G = vector<vector<int>> (N, vector<int> (N, INF));
    tabu.clear();
    for (int i = 0; i < M; i++) { int f, t, c; cin >> f >> t >> c; G[f][t] = c; }
}

int get_fitness(const vector<int> &v)
{
    int sum = 0;
    for (int i = 0; i < N; i++) {
        int from = v[i], to = v[(i + 1) % N];
        if (G[from][to] >= INF) return INF;
        sum += G[from][to];
    }
    return sum;
}

void random_swap(vector<int> &v)
{
    int x = (rand() % v.size());
    int y; while ((y = (rand() % v.size())) == x) { ; }
    swap(v[x], v[y]);
}

void print_vector(const vector<int> &v)
{
    for (int i = 0; i < v.size(); i++) printf("%d%c", v[i], i+1==v.size()?'\n':' ');
}

void solve()
{
    vector<int> solution; for (int i = 0; i < N; i++) { solution.push_back(i); }
    int fitness = get_fitness(solution);
    vector<int> best_solution = solution;
    int best_fitness = fitness;
    for (int i = 0; i < MAX_ITERS; i++) {
        random_swap(solution);
        fitness = get_fitness(solution);
        if (find(tabu.begin(), tabu.end(), solution) == tabu.end() && fitness < best_fitness) {
            best_fitness = fitness;
            best_solution = solution;
        }

        tabu.push_back(solution);
        while (tabu.size() > MAXL) { tabu.pop_front(); }
    }

    cout << best_fitness << endl;
    print_vector(best_solution);
}

int main()
{
    while (cin >> N >> M) {
        input();
        solve();
    }
}
```

```
4 6
0 1 2
1 2 3
1 3 9
2 0 1
2 3 6
3 2 4
```

```
16
```

为了简单起见，搜索邻近解的方法就只是随机交换，但这里启发法方法有很多。看上去它就是一个有限记忆的搜索方法罢了，不过如果结合其他更有效的启发式规则，也能够有效地求大型组合问题的近似解。

有趣的是，第一次听到这个算法是在《理科生坠入情网，故尝试证明。》（看番涨知识 ✓）

## 更多参考

- [Tabu search - Wikipedia](https://en.wikipedia.org/wiki/Tabu_search)
