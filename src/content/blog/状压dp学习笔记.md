---
title: "状压dp学习笔记"
description: "如题"
pubDate: "Dec 06 2024"
image: "/Sigewinne_5.webp"
categories:
  - tech
tags:
  - 学习笔记
---

状压dp，全称状态压缩dp，就是把 $n$ 个元素的状态压缩进一个二进制数，然后把他当dp状态进行dp。这类题目 $n$ 通常极小。先来看经典的tsp问题：

## <a href = "https://www.luogu.com.cn/problem/P1433" target = "_blank">P1433 吃奶酪</a>  
我们设 $dp_{j,i}$ 表示当前访问到点 $j$ ，访问过的点状态为 $i$ 时的最小花费。
### 何为状态为 $i$ ？
每一个数都可以表示成二进制数，如 $114$ 就可以表示成 `01110010` 。这里，我们设当 $i$ 在二进制下的第 $j$ 位为 `1` 时，这个点访问过。如 `114` 就是点 $2,5,6,7$ 访问过。  
那么我们有转移：`dp[j][i | (1 << (j - 1))] = min(dp[j][i | (1 << (j - 1))],dp[i][k] + dis(k,j))` 。其中 `dis[k][j]` 表示 $k,j$ 两点之间的距离。我们就可以通过枚举状态 $i$ ，源点 $k$ ，新点 $j$ 得出答案，时间复杂度 $\mathcal{O}(n^2 \times 2^n)$ 。初始化的时候 `dp[i][1 << (i - 1)] = dis(0,i)` 。  
code:
```cpp
#include<bits/extc++.h>
#define float double
using namespace std;
int n;
float x[20],y[20];
float dp[20][(1 << 15) + 5];
float dis(int a,int b){return sqrt(pow(x[a] - x[b],2) + pow(y[a] - y[b],2));}
signed main()
{
    scanf("%d",&n);
    for (int i = 1; i <= n; i++)
        scanf("%lf%lf",x + i,y + i);
    memset(dp,0x7f,sizeof dp);
    for (int i = 1; i <= n; i++)
        dp[i][1 << (i - 1)] = dis(0,i);
    for (int i = 1; i < (1 << n); i++)
        for (int k = 1; k <= n; k++)
            if (i & (1 << (k - 1)))
                for (int j = 1; j <= n; j++)
                    if (!(i & (1 << (j - 1))))
                        dp[j][i | (1 << (j - 1))] = min(dp[j][i | (1 << (j - 1))],dp[k][i] + dis(k,j));
    float ans = 1145141919810;
    for (int i = 1; i <= n; i++)
        ans = min(ans,dp[i][(1 << n) - 1]);
    printf("%.2lf",ans);
    return 0;
}
```

这是tsp问题，接下来我们看个人认为比较常见且比较板的题：

## <a href = "https://www.luogu.com.cn/problem/P1879">P1879 [USACO06NOV] Corn Fields G</a>  
这种题，我们看到 $n \le 12$ ，我们就可以想到状压或者暴搜。题解区里也确实有暴搜做法，但是因为这是**状压学习笔记**，这里就不写暴搜做法了。  
我们设 $dp_{i,j}$ 表示枚举到了第 $i$ 行，第 $i$ 行的状态为 $a_j$ 。这里的状态就是当 $a_j$ 在二进制下的第 $k$ 位为 $1$ 时，$(i,k)$ 就种上了玉米。对于一行内的情况，我们可以预处理到 $a$ 数组中。而对于行与行之间的呢，设第 $i$ 行状态为 $a_j$ ，第 $i - 1$ 行状态为 $a_k$ 。那么如果 $a_j \& a_k \not = 0$ ，他们两行之间就是有同意列都种上了玉米，就不对。因此，我们就得到了dp方程：$dp_{i,j} = \sum\limits_{a_j \& a_k = 0}dp_{i-1,k}$ 。初始化的时候可以在预处理里实现：在找到一个合法方案 $j$ 时，$dp_{1,j} = 1$ 。  
code:
```cpp
#include<bits/extc++.h>
#define int long long
using namespace std;
const int mod = 1e8;
int n,m;
int a[400],cnt;
int dp[15][400];
int mp[15];
signed main()
{
    scanf("%lld%lld",&n,&m);
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
            int ch;
            scanf("%lld",&ch);
            mp[i] = (mp[i] << 1) + (ch == 1);//mp[i]表示输入的限制
        }
    }
    for (int i = 0; i < (1 << m); i++)//预处理+初始化
    {
        if ((i & (i << 1) || i & (i >> 1)))//如果每一个1的左边有1或右边有1就是非法情况。
            continue;
        cnt ++;
        a[cnt] = i;
        if ((mp[1] & i) == i)
            dp[1][cnt] = 1;
    }
    for (int i = 2; i <= n; i++)
        for (int j = 1; j <= cnt; j++)//枚举预处理好的状态
            if ((a[j] & mp[i]) == a[j])//如果这种状态符合输入的限制
                for (int k = 1; k <= cnt; k++)
                    if (!(a[j] & a[k]))//如果这两种状态不冲突
                        dp[i][j] = (dp[i][j] + dp[i - 1][k]) % mod;
    int ans = 0;
    for (int i = 1; i <= cnt; i++)//求答案
        ans = (ans + dp[n][i]) % mod;
    printf("%lld",ans);
    return 0;
}
```

接下来就该进阶了。

## <a href = "https://www.luogu.com.cn/problem/P2831" target = "_blank">P2831 [NOIP2016 提高组] 愤怒的小鸟</a>  
设 $dp_i$ 表示死猪状态为 $i$ 时的最少消耗。那么有朴素的转移：枚举两个点 $i,j$ ，与原点确定抛物线，然后再枚举一个点 $k$ ，看 $k$ 是否再之前所确定的那条抛物线上。时间复杂度 $\mathcal{O}(T \times n^3 \times 2^n)$ 。总共要算 $18^3 \times 2^{18} \approx 1.5 \times 10^{10}$ 次，绝对要T。  
我们考虑怎么优化。预处理 $vis_{i,j}$ 表示原点与 $i$ 点和 $j$ 确定出来的抛物线经过的点，那么就不用枚举 $k$ 了，时间复杂度 $\mathcal{O}(T \times n^2 \times 2^n)$ ，算了一下大概要算 $8.4 \times 10^9$ 次，还是要T。  
我们继续优化。我们发现，如果先打 $1,3$ 两头猪再打 $2,4$ 两头猪，和先打 $2,4$ ， 再打 $1,3$ 效果事一样的，那么我们就可以预处理一个数组 $low_i$ 表示最小（或最大）的 $x$ ，使得 `i & (1 << x)` 为 $0$ 。那么我们就只用枚举一个 $i$ ，时间复杂度降到了 $\mathcal{O}(T \times n \times 2^n)$ 就可以过了。  
啊其实这是我从日记上扒的，但是都是我自己写的，所以就无所谓啦。  
code:
```cpp
#include<bits/extc++.h>
#define float double
#define pff pair<float,float>
using namespace std;
const int maxn = (1 << 18) + 5;
const float eps = 1e-8;
int n,m;
int low[maxn],vis[20][20];
int dp[maxn];
float x[20],y[20];
pff LuoHuan(float x1,float y1,float z1,float x2,float y2,float z2)//解方程
{
    pff ret;
    ret.second = (x1 * z2 - x2 * z1) / (x1 * y2 - x2 * y1);
    ret.first = (z1 - y1 * ret.second) / x1;
    return ret;
}
void solve()
{
    memset(vis,0,sizeof vis);
    memset(dp,0x3f,sizeof dp);
    scanf("%d%d",&n,&m);
    for (int i = 1; i <= n; i++)
        scanf("%lf%lf",x + i,y + i);
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= n; j++)
        {
            if (abs(x[i] - x[j]) < eps)//如果两个点的x相等，那么一定没有解。
                continue;
            pff tmp = LuoHuan(x[i]*x[i],x[i],y[i],x[j]*x[j],x[j],y[j]);
            if (tmp.first > -eps)//如果抛物线的开口方向向上，不符合题意。
                continue;
            for (int k = 1; k <= n; k++)
                if (abs(x[k] * x[k] * tmp.first + x[k] * tmp.second - y[k]) < eps)//预处理vis[i][j]
                    vis[i][j] |= (1 << (k - 1));
        }
    }
    dp[0] = 0;
    for (int i = 0; i < (1 << n); i++)
    {
        int j = low[i];
        dp[i | (1 << (j - 1))] = min(dp[i | (1 << (j - 1))],dp[i] + 1);//直接打的
        for (int k = 1; k <= n; k++)
            dp[i | vis[j][k]] = min(dp[i | vis[j][k]],dp[i] + 1);//尝试经过别的点打的
    }
    printf("%d\n",dp[(1 << n) - 1]);
}
signed main()
{
    for (int i = 0; i < (1 << 18); i++)
    {
        int j = 1;
        while (j <= 18 && (i & (1 << (j - 1))))
            j ++;
        low[i] = j;//预处理low[i]
    }
    int t;
    scanf("%d",&t);
    while (t --)
        solve();
    return 0;
}
```

## <a href = "https://www.luogu.com.cn/problem/P3959" target = "_blank">P3959 [NOIP2017 提高组] 宝藏</a>  
我们看这个：要让访问所有宝藏屋的代价和最小，那不就是最小生成树吗？但是 $n$ 如此小，肯定有鬼。一看：代价不直接是边长 $L$ ，而是 $K \times L$ 。这里还多了一个 $K$ ，所以贪心不对。因为有这个 $K$ ，我们考虑把这个生成数分成 $k$ 层。我们设 $dp_{k,i}$ 为访问到了第 $k$ 层，总共访问的状态为 $i$ 的最小代价。那么我们肯定要从 $i$ 的子集转移而来。我们枚举每一个子集，并枚举子集里的点，找出它到补集中的任意一个点的最短路，然后把子集里的每一个点到补集的最短路求和，就可以得到答案。形式化讲：$dp_{k,i} = \min\limits_{j \subset i}(dp_{k - 1,j} + \sum\limits_{u \not \in j,u\in \complement_{i}j}w_{u,j})$ ，其中，$w_{u,j}$ 表示子集 $j$ 里的点 $u$ 到补集 $\complement_{i}j$ 里的任意一个点的最短路，也就是 $\min\limits_{v \in \complement_{i}j}(dis_{u,v})$ 。初始化会在代码里说明。  
code:  
```cpp
#include<bits/extc++.h>
#define int unsigned long long
#define inf 0x7f7f7f7f7f7f7f7f
using namespace std;
const int maxn = (1 << 12) + 5;
int n,m;
int dis[15][15];
int w[15][maxn],dp[15][maxn];
signed main()
{
    scanf("%llu%llu",&n,&m);
    memset(dp,0x7f,sizeof dp);
    memset(dis,0x7f,sizeof dis);
    memset(w,0x7f,sizeof w);
    for (int i = 1; i <= n; i++)
        dis[i][i] = 0;
    for (int i = 1; i <= m; i ++)
    {
        int a,b,c;
        scanf("%llu%llu%llu",&a,&b,&c);
        dis[a][b] = dis[b][a] = min(dis[a][b],c);
    }
    for (int i = 1; i <= n; i++)
        for (int j = 1; j < (1 << n); j++)
            for (int k = 1; k <= n; k++)
                if (!(j & (1 << (i - 1))) && (j & (1 << (k - 1))))
                    w[i][j] = min(w[i][j],dis[i][k]);//预处理w[i][j]
    for (int i = 1; i <= n; i++)
        dp[1][(1 << (i - 1))] = 0;//初始化
    for (int i = 1; i < (1 << n); i++)
    {
        for (int j = i & (i - 1); j; j = i & (j - 1))
        {
            int tmp = 0;
            for (int k = 1; k <= n; k++)
            {
                if ((1 << (k - 1)) & (i - j))
                {
                    if (w[k][j] > inf)
                        tmp = inf;
                    else
                        tmp += w[k][j];//记录子集里的每一个点到补集的最短路求和
                }
            }
            for (int k = 2; k <= n; k++)
                if (dp[k - 1][j] != inf && tmp != inf)
                    dp[k][i] = min(dp[k][i],dp[k - 1][j] + tmp * (k - 1));
        }
    }
    int ans = inf;
    for (int i = 2; i <= n; i++)//记录答案
        ans = min(ans,dp[i][(1 << n) - 1]);
    printf("%llu",ans == inf ? 0 : ans);
    return 0;
}
```

至此你就大概会了状压dp了。  
~~虽然这个笔记是给我自己写的~~