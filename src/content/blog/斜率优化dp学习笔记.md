---
title: "斜率优化dp学习笔记"
description: "如题"
pubDate: "Nov 22 2024"
image: "/head_pic/Nahida_3.webp"
categories:
  - tech
tags:
  - 学习笔记
---

这玩意甚至比二项式反演还难。

## 引入

斜率优化 dp，通常用于 dp 方程长成这个样子的题：

$$
dp_{i} = \min\{dp_{j} + a_{i}b_{j} + c_{i} + d_{j}\}
$$

因为有 $a_{i}b_{j}$ 这样的与 $i$ 和 $j$ 都有关的恶心项，所以不能用单调队列优化。

先来一道例题：[P3195 \[HNOI2008\] 玩具装箱](https://www.luogu.com.cn/problem/P3195)

既然叫斜率**优化** dp，那么我们肯定得先把朴素的 dp 方程写出来。设 $dp_{i}$ 为前 $i$ 个玩具都塞进去了的最小代价，并维护前缀和 $sum_{i} = \sum\limits_{j = 1}^{i}C_{i} + 1$，那么有朴素的 dp 方程：

$$
dp_{i} = \min\limits_{j = 0}^{i - 1}\{dp_{j} + (sum_{i} - sum_{j} - 1 - L)^{2}\}
$$

方程很好理解，这里就不说了。那么现在我们考虑优化。为了方便，把 $L$ 提前加 $1$，然后把式子化简一下：

$$
\begin{aligned}
dp_{i} & = \min\{dp_{j} + (sum_{i} - sum_{j} - L)^{2}\} \\
& = \min\{dp_{j} + sum_{i}^{2} + (sum_{j} + L)^{2} - 2sum_{i}(sum_{j} + L)\} \\
& = sum_{i}^{2} + 2sum_{i}L + \min\{dp_{j} + (sum_{j} + L)^{2} - 2sum_{i}sum_{j}\} \\
\end{aligned}
$$

化简的原则就是拆括号，然后把与内层循环无关的量扔出 $\min$。

考虑一次函数的斜截式 $y = kx + b$，移项变成 $b = y - kx$，原方程就该写作 $b_{i} = \min\{y - kx\}$，对于每一个 $i$，把在择优过程中不变的项（也就是只跟随 $i$ 变化的项，因为我们在讨论一个单独的 $i$，所以是“不变的项”）记在 $k$ 和 $b$ 里面，要变的（也就是会跟随 $j$ 变化的项）记在 $kx$ 和 $y$ 里面，具体一点，就是最小化的（也就是被扔出 $\min$ 的项和左边的 $dp_{i}$，还有一种描述是只跟 $i$ 有关的项）记作 $b$，和 $i,j$ 都有关的项记作 $kx$：只与 $i$ 有关的记作 $k$，只与 $j$ 有关的记作 $x$。剩下的只与 $j$ 有关的项记作 $y$。

看上面一大段文字会迷糊，不如来看看例子。在这道题的这个方程里，有：

$$
\begin{aligned}
x_{j} & = sum_{j} \\
y_{j} & = dp_{j} + (sum_{j} + L)^{2} \\
k_{i} & = 2sum_{i} \\
b_{i} & = dp_{i} - (sum_{i}^{2} + 2sum_{i}L)
\end{aligned}
$$

如果你看了还是迷糊，那么可以看看下面的例题里的式子，多看几遍，多推几遍就懂了。

那么我们看到一个东西：$b_{i} = dp_{i} - sum_{i}^{2}$，而 $sum_{i}^{2}$ 对于正在考虑的“固定”的 $i$ 是不变的，那么就可得：截距越小，决策更优（在有些题里是更大更优）。而 $k_{i}$ 也是不变的，那么就相当于：在平面直角坐标系里点了许多个点，第 $j$ 个点的坐标是 $(x_{j},y_{j})$，在编号属于 $[1,i)$ 的点里面找一个点 $j$，使得经过它的斜率为 $k_{i}$ 的直线截距最小。就像这样：

![](https://cdn.luogu.com.cn/upload/image_hosting/secqebmg.png)

想象这条绿色的线在一直向上移动，它碰到的第一个点就是最优决策点。用盯真法，这里就是 $P_{1}$。但是程序不会盯真，所以我们还得想想怎么让程序也会找最优点。很显然，最优点一定在下凸壳（有些是上凸壳）上。于是我们可以用单调栈维护一个凸壳。但是凸壳有了，那凸壳上还有那么多点啊，到底是哪一个呢？

在这幅图里，很显然是最低点 $P_{1}$，那会不会有其他的情况呢，是有的。就比如我们把这条线的斜率加大一点：

![](https://cdn.luogu.com.cn/upload/image_hosting/g2961kqd.png)

那现在就不是简单的最低点了，而是 $P_{2}$。但是，我们发现，$P_{1}$ 之所以不是最优解，是因为 $P_{1}P_{2}$ 这条线的斜率没有绿线大，导致 $P_{2}$ 先遇到绿线，$P_{1}$ 和前面的点（如果有的话）就都不会是最优决策点了。于是，我们可以把单调栈换成单调队列，然后在队尾，如果这个点与下一个点的连线比当前斜率 $k_{i}$ 要小（有些题是大），那么就把他扔出去，扔完之后的队尾就是最优决策点啦。因为每个点至多进出队列一次，所以时间复杂度为 $\mathcal{O}(n)$。

当然，还有一种做法，就是继续用单调栈，因为我们维护的是一个凸壳，所以斜率是具有单调性的，我们可以在单调栈上二分出第一个斜率大于等于当前斜率 $k_{i}$ 的第一个点就是最优决策点，时间复杂度 $\mathcal{O}(n \log n)$，没有单调队列做法优。

放一下代码：

```cpp
#include<bits/extc++.h>
#define sq(x) ((x) * (x))
#define int long long
typedef long double ld;
using namespace std;
const int maxn = 5e4 + 5;
int n,l;
int c[maxn],sum[maxn],dp[maxn];
int q[maxn],head,tail;
ld x(int i){return sum[i];}//上文里的x[i]
ld y(int i){return dp[i] + sq(sum[i] + l);}//y[i]
ld k(int i){return (ld)2 * sum[i];}//k[i]
ld slope(int i,int j){return (y(j) - y(i)) / (x(j) - x(i));}
signed main()
{
    scanf("%lld%lld",&n,&l);
    l++;
    for (int i = 1; i <= n; i++)
    {
        scanf("%lld",c + i);
        sum[i] = sum[i - 1] + c[i] + 1;
    }
    head = 1,tail = 0;
    q[++tail] = 0;
    for (int i = 1; i <= n; i++)
    {
        while (head < tail && slope(q[head],q[head + 1]) <= k(i))
            head++;//将与下一个点的连线斜率小于等于当前k[i]的扔出去
        dp[i] = dp[q[head]] + sq(sum[i] - sum[q[head]] - l);
        while (head < tail && slope(q[tail - 1],q[tail]) >= slope(q[tail - 1],i))
            tail--;//维护凸壳
        q[++tail] = i;//压进队列
    }
    printf("%lld",dp[n]);
    return 0;
}
```

你肯定注意到了“有些题”和这个题不一样，至于为什么不一样呢？因为有些题的 $k_{i}$ 是单减的，所以是上凸壳，有些甚至没有单调性，那么我们就要用平衡树或者李超线段树来维护了。