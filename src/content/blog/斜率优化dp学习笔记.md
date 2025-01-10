---
title: "斜率优化dp学习笔记"
description: "如题"
pubDate: "Jan 10 2025"
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

### 例题1：[P3195 \[HNOI2008\] 玩具装箱](https://www.luogu.com.cn/problem/P3195)

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
k_{i} & = 2sum_{i} \\
y_{j} & = dp_{j} + (sum_{j} + L)^{2} \\
b_{i} & = dp_{i} - (sum_{i}^{2} + 2sum_{i}L)
\end{aligned}
$$

如果你看了还是迷糊，那么可以看看下面的例题里的式子，多看几遍，多推几遍就懂了。

那么我们看到一个东西：$b_{i} = dp_{i} - sum_{i}^{2}$，而 $sum_{i}^{2}$ 对于正在考虑的“固定”的 $i$ 是不变的，那么就可得：截距越小，决策更优（在有些题里是更大更优）。而 $k_{i}$ 也是不变的，那么就相当于：在平面直角坐标系里点了许多个点，第 $j$ 个点的坐标是 $(x_{j},y_{j})$，在编号属于 $[1,i)$ 的点里面找一个点 $j$，使得经过它的斜率为 $k_{i}$ 的直线截距最小。就像这样：

![](https://cdn.luogu.com.cn/upload/image_hosting/secqebmg.png)

想象这条绿色的线在一直向上移动，它碰到的第一个点就是最优决策点。用盯真法，这里就是 $P_{1}$。但是程序不会盯真，所以我们还得想想怎么让程序也会找最优点。很显然，最优点一定在下凸壳（有些是上凸壳）上。于是我们可以用单调栈维护一个凸壳。但是凸壳有了，那凸壳上还有那么多点啊，到底是哪一个呢？

在这幅图里，很显然是最低点 $P_{1}$，那会不会有其他的情况呢，比如我们把这条线的斜率加大一点：

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

你肯定注意到了“有些题”和这个题不一样，至于为什么不一样呢？因为有些题的 $k_{i}$ 是单减的，所以是上凸壳，有些甚至没有单调性，那么我们就要用平衡树或者李超线段树来维护了。单调队列和单调栈只能用于维护横坐标和斜率都单调的题。

### 例题2：[P4655 [CEOI2017] Building Bridges](https://www.luogu.com.cn/problem/P4655)

这道题的斜率和横坐标就不单调了。

依旧是先列出朴素的方程：

$$
dp_{i} = \min\{dp_{j} + w_{i - 1} - w_{j} + (h_{i} - h_{j})^{2}\}
$$

其中 $w$ 是预处理好的前缀和。

接下来我们把它化简，并写出 $l,b,x,y$：

$$
\begin{aligned}
dp_{i} & = \min\{dp_{j} + w_{i - 1} - w_{j} + h_{i}^{2} + h_{j}^{2} - 2h_{i}h_{j} \} \\
& = w_{i - 1} + h_{i}^{2} + \min\{dp_{j} - w_{j} + h_{j}^{2} - 2h_{i}h_{j} \}
\end{aligned}
$$

写出 $l,b,x,y$：

$$

\begin{aligned}
k_{i} & = 2h_{i} \\
x_{j} & = h_{j} \\
b_{i} & = dp_{i} - w_{i - 1} - h_{i}^{2} \\
y_{j} & = dp_{j} - w_{j} + h_{j}^{2}
\end{aligned}
$$

但是我们发现：$x$ 和 $k$ 现在都不单调了，所以单调栈和单调队列立即发生爆炸。这时，我们就得请出[李超线段树](https://oi-wiki.org/ds/li-chao-tree/)了。

板子这里就不说了。如果用李超线段树的话，$l,x,b,y$ 略有不同：

$$
\begin{aligned}
k_{j} & = -2h_{j} \\
x_{i} & = h_{i} \\
b_{j} & = dp_{j} - w_{j} + h_{j}^{2} \\
y_{i} & = dp_{i} - w_{i - 1} - h_{i}^{2}
\end{aligned}
$$

具体来说，就是把方程从 $b = y - kx$ 变成 $y = kx + b$，经过移项得到 $dp_{i} = kx + b + \text{something}$。这里的 $\text{something}$ 通常是固定的，所以我们可得 $kx + b$ 最小的是最优决策。那么我们就可以用李超线段树维护线段，查询横坐标为 $x_{i}$ 时最小的纵坐标。

放一下代码：

```cpp
#include<bits/extc++.h>
#define int long long
#define sq(x) ((x) * (x))
#define inf 0x3f3f3f3f3f3f3f3f
using namespace std;
const int maxn = 1e5 + 5;
int n,cnt,rt;
int h[maxn],w[maxn],dp[maxn];
int k(int j){return -2 * h[j];}
int x(int i){return h[i];}
int b(int j){return dp[j] - w[j] + sq(h[j]);}
struct line
{
    int l,b;
    line(int k = 0,int b = 0):k(k),b(b){};
    int f(int x){return k * x + b;}
};
struct Nahida
{
    int ls,rs;
    line ln;
    bool fl;
}tree[maxn << 2];
void upd(line ln,int l,int r,int &rt)
{
    if (!rt)
        rt = ++cnt;
    int lpos = tree[rt].ln.f(l),rpos = tree[rt].ln.f(r);
    int lque = ln.f(l),rque = ln.f(r);
    if (!tree[rt].fl)
    {
        tree[rt].ln = ln;
        tree[rt].fl = 1;
        return;
    }
    else if (lque <= lpos && rque <= rpos)
        tree[rt].ln = ln;
    else if (lque < lpos || rque < rpos)
    {
        int mid = (l + r) >> 1;
        if (ln.f(mid) < tree[rt].ln.f(mid))
            swap(ln,tree[rt].ln);
        if (ln.f(l) < tree[rt].ln.f(l))
            upd(ln,l,mid,tree[rt].ls);
        else
            upd(ln,mid + 1,r,tree[rt].rs);
    }
}
int que(int pos,int l,int r,int rt)
{
    if (!rt)
        return inf;
    int ret = tree[rt].ln.f(pos);
    if (l == r)
        return ret;
    int mid = (l + r) >> 1;
    int tmp;
    if (pos <= mid)
        tmp = que(pos,l,mid,tree[rt].ls);
    else
        tmp = que(pos,mid + 1,r,tree[rt].rs);
    ret = min(ret,tmp);
    return ret;
}
signed main()
{
    scanf("%lld",&n);
    for (int i = 1; i <= n; i++)
        scanf("%lld",h + i);
    for (int i = 1; i <= n; i++)
    {
        scanf("%lld",w + i);
        w[i] += w[i - 1];
    }
    upd(line(k(1),b(1)),0,2e6,rt);//初始状态为dp[1]
    for (int i = 2; i <= n; i++)
    {
        dp[i] = que(x(i),0,2e6,rt) + w[i - 1] + sq(h[i]);//查询最小纵坐标
        upd(line(k(i),b(i)),0,2e6,rt);//加入线段树
    }
    printf("%lld",dp[n]);
    return 0;
}
```

### 例题3：[P4072 [SDOI2016] 征途](https://www.luogu.com.cn/problem/P4072)

这题比较特殊，他的 dp 方程是二维的。我们还是先把朴素的方程写出来。设 $dp_{i,j}$ 表示到第 $i$ 走完 $i$ 条路用了 $j$ 天的最小方差。那么有：

$$
dp_{i,j} = \min\{dp_{l,j - 1} + \text{这段的贡献} \}
$$

那么问题就在于如何求贡献，我们先把方差的式子列出来：

$$
v = \frac{\sum\limits_{i = 1}^{m}(len_{i} - \frac{sum}{m})^{2}}{m}
$$

其中 $len_{i}$ 表示第 $i$ 天走的距离，$sum$ 表示到目的地的总距离。

现在按照题意，把 $v$ 乘上一个 $m^{2}$：

$$
\begin{aligned}
v \times m^{2} & = \frac{\sum\limits_{i = 1}^{m}(len_{i} - \frac{sum}{m})^{2}}{m} \times m^{2} \\
& = m \times \sum\limits_{i = 1}^{m}(len_{i} - \frac{sum}{m})^{2} \\
& = m \times \sum\limits_{i = 1}^{m}(len_{i}^{2} + \frac{sum^{2}}{m^{2}} - 2 \times len_{i} \times \frac{sum}{m}) \\
& = \sum\limits_{i = 1}^{m}(m \times len_{i}^{2} + \frac{sum^{2}}{m} - 2 \times len_{i} \times sum) \\
& = sum^{2} + \sum\limits_{i = 1}^{m}(m \times len_{i}^{2} - 2 \times len_{i} \times sum)
\end{aligned}
$$

那现在我们就知道每一段的贡献是 $m \times len_{i}^{2} - 2m \times len_{i} \times sum$。如此，我们的答案就是 $dp_{n,m} + sum^{2}$。记录 $dis$ 为距离的前缀和，用 $dis$ 里的数替换 $len_{i}$ 和 $sum$，并把完整的 dp 方程写出来：

$$
dp_{i,j} = \min\{dp_{l,j - 1} + m(dis_{i} - dis_{l})^{2} - 2 \times (dis_{i} - dis_{l}) \times dis_{n} \}
$$

接下来，我们尝试将它化简：

$$
\begin{aligned}
dp_{i,j} & = \min\{dp_{l,j - 1} + m(dis_{i} - dis_{l})^{2} - 2 \times (dis_{i} - dis_{l}) \times dis_{n} \} \\
& = \min\{dp_{l,j - 1} + m(dis_{i}^{2} + dis_{l}^{2} - 2dis_{i}dis_{l}) - 2dis_{i}dis_{n} + 2dis_{l}dis_{n} \} \\
& = \min\{dp_{l,j - 1} + m \times dis_{i}^{2} + m \times dis_{l}^{2} - 2m \times dis_{i}dis_{l} - 2dis_{i}dis_{n} + 2dis_{l}dis_{n} \} \\
& = m \times dis_{i}^{2} - 2dis_{i}dis_{n} + \min\{dp_{l,j - 1} + m \times dis_{l}^{2} - 2m \times dis_{i}dis_{l} + 2dis_{l}dis_{n} \}
\end{aligned}
$$

并尝试写出单调队列形式的 $k,x,b,y$：

$$
\begin{aligned}
k & = 2m \times dis_{i} \\
x & = dis_{l} \\
b & = dp_{i,j} - (m \times dis_{i}^{2} - 2dis_{i}dis_{n}) \\
y & = dp_{l,j - 1} + m \times dis_{l}^{2} + 2dis_{l}dis_{n}
\end{aligned}
$$

我们发现他的斜率单调递增，可以用单调队列来维护。那具体怎么做呢？我们最外层枚举 $j$，对于每一个 $j$ 对应的那一层，我们单独用一个单调队列维护，每层转移开始时清空并重新扔一个新的起始状态进去。

> 为什么要每层都用一个单独的单调队列？

> 因为每一层都是独立的，相当于重新开始了一次 dp。

更多细节会在代码里说明：

```cpp
#include<bits/extc++.h>
#define int long long
#define sq(x) ((x) * (x))
using namespace std;
typedef long double ld;
const int maxn = 3005;
int n,m;
int dis[maxn];
int dp[maxn][maxn];
int q[maxn],head,tail;

//上文里的三哥俩
int k(int i){return 2 * m * dis[i];}
int x(int k){return dis[k];}
int y(int k,int j){return dp[k][j - 1] + m * sq(dis[k]) + 2 * dis[k] * dis[n];}

ld slope(int i,int k,int j){return ((ld)y(k,j) - (ld)y(i,j)) / ((ld)x(k) - (ld)x(i));}
signed main()
{
    scanf("%lld%lld",&n,&m);
    for (int i = 1; i <= n; i++)
    {
        scanf("%lld",dis + i);
        dis[i] += dis[i - 1];
    }
    memset(dp,0x3f,sizeof dp);
    dp[0][0] = 0;
    for (int j = 1; j <= m; j++)
    {
        //重中之重
        head = tail = 1;
        q[1] = j - 1;
        //因为前 j - 1 天至多走 j - 1 条路，所以初始的就是 j - 1
        for (int i = j; i <= n; i++)
        {
            while (tail > head && slope(q[head],q[head + 1],j) < k(i))
                head++;//维护凸壳
            int k = q[head];//本来以为会和上面的k(i)冲突的，但是实际上没有
            dp[i][j] = dp[k][j - 1] + m * sq(dis[i] - dis[k]) - 2 * (dis[i] - dis[k]) * dis[n];
            while (tail > head && slope(q[tail - 1],q[tail],j) > slope(q[tail],i,j))
                tail--;//加入队列
            q[++tail] = i;
        }
    }
    printf("%lld",dp[n][m] + sq(dis[n]));//输出答案
    return 0;
}
```