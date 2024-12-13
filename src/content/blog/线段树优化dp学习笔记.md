---
title: "线段树优化dp学习笔记"
description: "如题"
pubDate: "Nov 22 2024"
image: "/head_pic/Nahida_1.webp"
categories:
  - tech
tags:
  - 学习笔记
---

到底是什么算法让我觉得两道题就足以让我写一篇学习笔记呢？

虽然两年半以前写过一道dp，正解的优化是单调队列，但是我拿线段树过了（卡着空间过的），所以那个dp并不能叫线段树优化dp。

## [CF115E Linear Kingdom Races](http://codeforces.com/problemset/problem/115/E) 
这个算是最 “原汁原味” 线段树优化dp。  
设 $dp_{i,j}$ 表示第 $j$ 条路到第 $i$ 条路全修的最大收益。（至于第 $1$ 到 $j - 1$ 条路，那不是我们该考虑的，这就是dp的精髓）那么有转移：

$$
dp_{i,j} = \begin{cases}
dp_{i - 1,j} + (\sum\limits_{l_{k} \ge j,r_{k} = i}p_{k}) - c_{i} & j < i \\
(\max\limits_{1 \le y \le x < i}dp_{x,y}) + (\sum\limits_{l_{k} = r_{k} = i}p_{k}) - c_{i} & j = i \\
\end{cases}
$$

其中：$\sum\limits_{l_{k} \ge j,r_{k} = i}p_{k}$ 和 $\sum\limits_{l_{k} = r_{k} = i}p_{k}$ 分别表示右端点在 $i$ 且被区间 $[i,j]$ 覆盖的的比赛收益之和 和 左端点和右端点都在 $i$ 的比赛收益之和。
那为何在 $j < i$ 时只用考虑 $l_{k} \ge j,r_{k} = i$ 的比赛呢？因为 $r_{k} < i$ 的比赛都在 $dp_{i - 1,?}$ 考虑过了，而 $r_{k} > i$ 的不是现在该考虑的。  
但是，这题不可能这么简单，单是存状态和枚举状态就可以让我们爆炸了
先来优化空间：我们发现：$\max\limits_{1 \le y \le x < i}dp_{x,y}$ 就是对于 $i - 1$ 的答案，于是第二个方程就可以表示成 $ans + (\sum\limits_{l_{k} = r_{k} = i}p_{k}) - c_{i}$ 。而我们发现第一个方程又只会从 $i - 1$ 来转移，那么我们可以考虑什么？滚动数组啊。那么空间就优化完了。  
现在来优化时间。我们设每一个 $dp_{i,j}$ 都有一个待定值数组 $tmp$ ，那么 $dp_{i,j}$ 一定由 $tmp$ 里的某一个值转移而来。对于暴力来说，就是每次新枚举一个新的状态，都给他一个新的 $tmp$ 数组，但是我们要优化，我们就不可能每次用 $\mathcal{O}(n)$ 给他一个新的 $tmp$ 数组，我们应该继承上一个状态的 $tmp$ 数组，并操作它使它变成当前状态的 $tmp$ 数组。我们发现，每次转移，$tmp$ 数组的 $[1,i]$ 项都会加上 $c_{i}$ ，也会加上 $\sum\limits_{l_{k} \ge j,r_{k} = i}p_{k}$ 。这不就是区间操作吗？我们就可以用线段树来维护这个值。线段树里装的就是待定值数组 $tmp$ 。而对于 $i = j$ 的情况，因为本来只有 $[1,i - 1]$ 的下标里有值，而我们要用到 $tmp_{i}$ 的值，我们就可以给他赋值一个 $ans$ ，正如同方程里的一样，而题目里让我们求最大的收益，那么线段树查询的就是区间 $[1,i]$ 里的最大值。这样，我们就做完了。  
有些细节比如如何记录 $r_{k} = i$ 的比赛会在代码里说明。

```cpp
#include<bits/extc++.h>
#define int long long
#define ls (rt << 1)
#define rs (rt << 1 | 1)
using namespace std;
const int maxn = 2e5 + 5;
int n,m;
int a[maxn];
struct edge
{
    int l,p;
    edge *nxt;
}*head[maxn];
struct Nahida//不要关心这个名字
{
    int l,r;
    int val,lazy;
}tree[maxn << 2];
void push_up(int rt){tree[rt].val = max(tree[ls].val,tree[rs].val);}
void push_down(int rt)
{
    if (!tree[rt].lazy)
        return;
    tree[ls].val += tree[rt].lazy;
    tree[rs].val += tree[rt].lazy;
    tree[ls].lazy += tree[rt].lazy;
    tree[rs].lazy += tree[rt].lazy;
    tree[rt].lazy = 0;
}
void build(int l,int r,int rt)
{
    tree[rt].l = l;
    tree[rt].r = r;
    if (l == r)
    {
        tree[rt].val = tree[rt].lazy = 0;
        return;
    }
    int mid = (l + r) >> 1;
    build(l,mid,ls);
    build(mid + 1,r,rs);
}
void upd(int ql,int qr,int x,int rt = 1)
{
    int l = tree[rt].l;
    int r = tree[rt].r;
    if (ql <= l && r <= qr)
    {
        tree[rt].val += x;
        tree[rt].lazy += x;
        return;
    }
    push_down(rt);
    int mid = (l + r) >> 1;
    if (ql <= mid)
        upd(ql,qr,x,ls);
    if (qr > mid)
        upd(ql,qr,x,rs);
    push_up(rt);
}
void adde(int l,int r,int p)
{
    auto tmp = new edge;
    tmp->l = l;
    tmp->p = p;
    tmp->nxt = head[r];
    head[r] = tmp;
}
signed main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    int l,r,p;
    for (int i = 1; i <= m; i++)
    {
        cin >> l >> r >> p;
        adde(l,r,p);//用链式前向星（邻接表）记录右端点为i的比赛。
    }
    build(1,n,1);
    int ans = 0;
    for (int i = 1; i <= n; i++)//滚动数组滚掉一维，就可以存下了。
    {//这里没有重新建树就是继承了上一个的tmp数组
        upd(i,i,ans);//单点修改，增添一个ans
        upd(1,i,-a[i]);//上面说的：区间更改
        for (auto j = head[i]; j; j = j->nxt)
            upd(1,j->l,j->p);//也是区间修改
        ans = max(ans,tree[1].val);//记录答案
    }
    cout << ans;
    return 0;
}
```

## 好题推荐：
[P2605 基站选址](https://www.luogu.com.cn/problem/P2605)