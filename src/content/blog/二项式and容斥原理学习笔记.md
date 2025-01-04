---
title: "二项式和容斥原理学习笔记"
description: "如题"
pubDate: "Dec 23 2024"
image: "/head_pic/Nahida_2.webp"
categories:
  - tech
tags:
  - 学习笔记
---

因为这b玩意实在是太难了，所以我得边学边记笔记。

## 容斥原理

先从容斥原理开始。

容斥原理的结论如下：

$$
|\bigcup\limits_{i = 1}^{n}S_{i}| 
 = \sum\limits_{m = 1}^{n}(-1)^{m - 1}\sum\limits_{a_{i} < a_{i - 1}}|\bigcap_{i = 1}^{m}S_{a_{i}}|
$$

证明的思路是考虑一个元素在每一个 $\bigcap\limits_{i = 1}^{m}S_{a_{i}}$ 里出现的次数，然后通过一番暴算，我们能够发现每个元素都只出现了 $1$ 次，这样，每个元素合起来就变成了总的并集。

详见[OI-wiki](https://oi.wiki/math/combinatorics/inclusion-exclusion-principle/)

## 二项式反演

### 反演的定义

现在我们有两个数组：$g$ 和 $f$。而 $f$ 和 $g$ 之间有对应关系：

$$
f_{n} = \sum\limits_{i = 0}^{n}a_{i} \times g_{i}
$$

然而题目里不可能这么简单，一般是已知 $f$ 让我们推 $g$。这种情况解个方程就可以。但是如果只有解方程的话，还不足以搞出**反演**这种东西出来，而上面的柿子在某些情况下会化出许多种优美的形式，所以才搞出了反演这种东西。

### 二项式反演的公式

我们可以从容斥原理推到二项式反演的公式。

有全集 $U = S_{1} \cup S_{2} \cup S_{3} \cup \cdots \cup S_{n}$，且任意 $i$ 个集合的交集和并集大小相同。设 $f_{i}$ 表示任意 $i$ 个集合的并集的大小，$g_{i}$ 表示任意 $i$ 个集合的补集的大小。特别的，$g_{0} = f_{0} = |U|$。根据补集的交集和原集的并集的容斥关系可以得出：

$$
\begin{aligned}
|\bigcap\limits_{i = 1}^{n}S_{i}| 
& = |U| - |\bigcup\limits_{i = 1}^{n}\overline{S_{i}}| \\
& = |U| - (|\overline{S_{1}}| + |\overline{S_{2}}| + \cdots + (-1)^{n - 1}|\overline{S_{1}} \cap \overline{S_{2}} \cap \cdots \cap \overline{S_{n}}|) \\
& = |U| - |\overline{S_{1}}| - |\overline{S_{2}}| - \cdots + (-1)^{n}|\overline{S_{1}} \cap \overline{S_{2}} \cap \cdots \cap \overline{S_{n}}| \\
& = \sum\limits_{i = 0}^{n}(-1)^{i}\binom{n}{i}g_{i}
\end{aligned}
\\
\begin{aligned}
|\bigcap\limits_{i = 1}^{n}\overline{S_{i}}|
& = |U| - |\bigcup\limits_{i = 1}^{n}S_{i}| \\
& = |U| - (|S_{1}| + |S_{2}| + \cdots + (-1)^{n - 1}|S_{1} \cap S_{2} \cap \cdots \cap S_{n}|) \\
& = |U| - |S_{1}| - |S_{2}| - \cdots + (-1)^{n}|S_{1} \cap S_{2} \cap \cdots \cap S_{n}|) \\
& = \sum\limits_{i = 0}^{n}(-1)^{i}\binom{n}{i}f_{i}
\end{aligned}
$$

然而，我们惊奇的发现：$|\bigcap\limits_{i = 1}^{n}S_{i}| = f_{n},|\bigcap\limits_{i = 1}^{n}\overline{S_{i}}| = g_{n}$，于是我们便得到了二项式反演的第一个公式：

$$
f_{n} = \sum\limits_{i = 0}^{n}(-1)^{i}\binom{n}{i}g_{i}
\Longleftrightarrow
g_{n} = \sum\limits_{i = 0}^{n}(-1)^{i}\binom{n}{i}f_{i}
$$

如何用数学方法证明这个公式是正确的呢？直接代入就行。  
将 $f_{n} = \sum\limits_{i = 0}^{n}(-1)^{i}\binom{n}{i}g_{i}$ 代入：

$$
\begin{aligned}
g_{n} & = \sum\limits_{i = 0}^{n}(-1)^{i}\binom{n}{i}\sum\limits_{j = 0}^{i}(-1)^{j}\binom{i}{j}g_{j} \\
& = \sum\limits_{i = 0}^{n}\sum\limits_{j = 0}^{i}(-1)^{i + j}\binom{n}{i}\binom{i}{j}g_{j} \\
& = \sum\limits_{i = 0}^{n}\sum\limits_{j = 0}^{i}(-1)^{i + j}\binom{n}{j}\binom{n - j}{n - i}g_{j} \\
& = \sum\limits_{j = 0}^{n}\binom{n}{j}g_{j}\sum\limits_{i = 0}^{n - j}(-1)^{i}\binom{n - j}{i} \\
& = \sum\limits_{j = 0}^{n}\binom{n}{j}g_{j}[j = n] \\
& = g_{n}
\end{aligned}
$$

显然成立。当然，这个柿子里还有一些不那么显然的东西

1. $\displaystyle\binom{n}{i}\binom{i}{j} = \binom{n}{j}\binom{n - j}{n - i}$  
这个可以用数学方法证明，也可组合意义的方法证明，这里主要说明组合意义的证法。  
求 $|U| = n,|A| = i,b = |j|,B \subseteq A \subseteq U$ 的方案数。这里有两种方法：  
法一：先在 $U$ 里取 $i$ 个元素构成集合 $A$，方案数为 $\binom{n}{i}$，然后在 $i$ 个元素里再取 $j$ 个元素构成集合 $B$，方案数为 $\binom{i}{j}$，合起来就是 $\binom{n}{i}\binom{i}{j}$  
法二：先在 $U$ 里取 $j$ 个元素构成集合 $B$，方案数为 $\binom{n}{j}$，再在剩下的 $n - j$ 个元素里取 $i - j$ 个元素，方案数为 $\binom{n - j}{i - j}$，又有 $\binom{n - j}{i - j} = \binom{n - j}{n - i}$，所以方案数就是 $\binom{n}{j}\binom{n - j}{n - i}$。  
得证。
2. $\displaystyle\sum\limits_{i = 0}^{n - j}(-1)^{i}\binom{n - j}{i} = [j = n]$  
设 $k = n - j$，令 $k = 0$，原式的值为 $1$，令 $k > 0$，原式为 $\sum\limits_{i = 0}^{k}\binom{k}{i}(-1)^{i}$，我们来添一个项：$\sum\limits_{i = 0}^{k}\binom{k}{i}(-1)^{i} 1^{i}$，发现，这玩意不就是[二项式定理的](https://www.shuxuele.com/algebra/binomial-theorem.html)的右边那坨吗？那么原式就是 $(-1 + 1)^{k} = 0^{k} = 0$。得证。

现在再看那个式子就比较好懂了。注意再第二点那里，因为数学老师告诉我们 $0^{0}$ 没有意义，所以在证明的时候为了严谨需要分类讨论。在做题的时候，为了方便可以钦定 $0^{0} = 1$。

### 小结

二项式反演的 $4$ 种形式：

形式一

$$
f_{n} = \sum\limits_{i = 0}^{n}(-1)^{i}\binom{n}{i}g_{i}
\Longleftrightarrow
g_{n} = \sum\limits_{i = 0}^{n}(-1)^{i}\binom{n}{i}f_{i}
$$

形式二（形式一的变形，比较常用）：

$$
f_{n} = \sum\limits_{i = 0}^{n}\binom{n}{i}g_{i}
\Longleftrightarrow
g_{n} = \sum\limits_{i = 0}^{n}(-1)^{n - i}\binom{n}{i}f_{i}
$$

形式三：

$$
f_{n} = \sum\limits_{i = n}^{m}(-1)^{i}\binom{i}{n}g_{i}
\Longleftrightarrow
g_{n} = \sum\limits_{i = n}^{m}(-1)^{i}\binom{i}{n}f_{i}
$$

形式四（形式三的变形，非常常用）：

$$
f_{n} = \sum\limits_{i = n}^{m}\binom{i}{n}g_{i}
\Longleftrightarrow
g_{n} = \sum\limits_{i = n}^{m}(-1)^{i - n}\binom{i}{n}f_{i}
$$

为什么形式四非常常用呢？因为题里多半都是求**恰好**的方案数。而我们好求的一般都是**至少**的方案数。比如：有 $m$ 个条件，$g_{i}$ 表示恰好满足 $i$ 个条件的方案数，$f_{i}$ 表示钦定满足 $i$ 个条件，剩下的随便的方案数，也就是至少。为啥是至少呢？因为有可能在那随便选的方案里装上某个条件。从 $f_{i}$ 的表述里就可以看出，$f_{i}$ 比 $g_{i}$ 好求很多，我们就可以用 $f$ 推得 $g$。

推导思路和证明思路来自[__allenge的博客](https://www.cnblogs.com/GDOI2018/p/14491894.html)。

## 例题 for 暴力容斥

### [P1450 [HAOI2008] 硬币购物](https://www.luogu.com.cn/problem/P1450)

我们先考虑没有限制的情况：$dp_{i}$ 表示没有限制的情况下买价格为 $i$ 的物品的方案数，容易发现这就是一个完全背包，可以在 $\mathcal{O}(val)$ 的复杂度下预处理，其中 $val$ 表示最大价格。然后接下来考虑限制的情况。发现正着考虑不超过限制的情况不太好做，**正难则反**。  
我们考虑超过限制的情况。超过限制就是第 $i$ 个硬币强制选 $d_{i} + 1$ 个，剩下的依然随便选，那么情况数就是 $dp_{s - (d_{i} + 1) \times c_{i}}$，答案就要减去 $\sum\limits_{i = 1}^{4}dp_{s - (d_{i} + 1) \times c_{i}}$。这是单个硬币超出限制的情况，如果有多个硬币，就得请出我们的容斥原理了。但是，容斥原理的公式要求任意两个集合的交集和并集大小相等，这很明显不相等啊，我们又发现：只有 $4$ 种硬币，那我们直接枚举不就行了吗？无非就是带一个 $16$ 的常数嘛。

核心代码：

```cpp
int ans = dp[s];//dp是预处理好的完全背包
for (int k = 1; k < (1 << 4); k++)
{
    int tmp = s;
    for (int i = 0; i < 4; i++)
        if (k & (1 << i))
            tmp -= (d[i] + 1) * c[i];
    if (tmp >= 0)
        ans += (__builtin_popcount(k) & -1 : 1) * dp[tmp];//容斥
}
```

### [P6521 [CEOI2010 day2] pin](https://www.luogu.com.cn/problem/P6521)

发现考虑不同的方案数有点难，**正难则反**。  
依然先考虑**至少**。我们设 $cnt_i$ 为至少有 $i$ 位相同的方案数。这个可以用哈希求解。我们设哈希函数：

```cpp
int hsh(char ch){return isdigit(ch) ? ch - '0' : ch - 'a' + 10;}
int hsh(char ch1,char ch2){return hsh(ch1) * 36 + hsh(ch2);}
int hsh(char ch1,char ch2,char ch3)
{return hsh(ch1) * 36 * 36 + hsh(ch2) * 36 + hsh(ch3);}
```

分别用于求解一个字符，两个字符，三个字符的哈希值。很明显，这个哈希函数生成的哈希值不会超过 $6 \times 10 ^ 4$。那么我们记录 $tmp_{i}$ 表示有多少个字符序列的哈希值是 $i$，然后要求解方案数呢，就相当于求解**在 $tmp_{i}$ 个元素里随便取两个不同元素的方案数**，很明显是 $\frac{tmp_{i} \times (tmp_{i} - 1)}{2}$。最后，我们求得了至少有 $i$ 个元素相同的方案数，因为最多有 $4$ 个元素相同，所以我们直接暴力容斥就行。

code:

```cpp
for (int x = 1; x <= 4; x++)//一个字符相同的
{
    memset(tmp,0,sizeof tmp);
    for (int i = 1; i <= n; i++)
        tmp[hsh(s[i][x])] ++;
    for (int i = 0; i <= 6e4; i++)
        cnt[1] += (tmp[i] * (tmp[i] - 1)) >> 1;
}
for (int x = 1; x <= 4; x++)//两个字符相同
{
    for (int y = x + 1; y <= 4; y++)
    {
        memset(tmp,0,sizeof tmp);
        for (int i = 1; i <= n; i++)
            tmp[hsh(s[i][x],s[i][y])] ++;
        for (int i = 0; i <= 6e4; i++)
            cnt[2] += (tmp[i] * (tmp[i] - 1)) >> 1;
    }
}
for (int x = 1; x <= 4; x++)//三个字符相同
{
    for (int y = x + 1; y <= 4; y++)
    {
        for (int z = y + 1; z <= 4; z++)
        {
            memset(tmp,0,sizeof tmp);
            for (int i = 1; i <= n; i++)
                tmp[hsh(s[i][x],s[i][y],s[i][z])] ++;
            for (int i = 0; i <= 6e4; i++)
                cnt[3] += (tmp[i] * (tmp[i] - 1)) >> 1;
        }
    }
}
//以下是暴力容斥
ans[3] = cnt[3];
ans[2] = cnt[2] - 3 * ans[3];
ans[1] = cnt[1] - ans[2] * 2 - ans[3] * 3;
ans[0] = ((n * (n - 1)) >> 1) - ans[1] - ans[2] - ans[3];
printf("%lld",ans[4 - d]);//由于求解的是相同的，所以不同的就是4 - d个
```

## 例题 for 二项式反演

### [P10986 [蓝桥杯 2023 国 Python A] 2023](https://www.luogu.com.cn/problem/P10986)

我们看到**恰好**两个字，就想到先推**至少**的情况，再用二项式反演的式子推到**恰好**。

设 $f_{m}$ 表示至少有 $m$ 个 `2023` 的方案数。我们钦定有 $m$ 个 `2023`，再用[插板法](https://oi-wiki.org/math/combinatorics/combination/#%E6%8F%92%E6%9D%BF%E6%B3%95)，在这些 `2023` 的缝里插入那些乱七八糟的数。因为插入的时候会再凑出一些 `2023`，所以是**至少**。那么，在 $m$ 个 `2023` 中插入 $n - 4m$ 个数的方案是

$$
f_{m} = \binom{(n - 4m) + (m + 1) - 1}{(n - 4m) - 1}
$$

设 $g_{m}$ 表示恰好有 $m$ 个 `2023` 的方案数，那么有：

$$
f_{m} = \sum\limits_{i = m}^{n}\binom{i}{m}g_{i}
$$

根据二项式反演公式，我们得到：

$$
g_{m} = \sum\limits_{i = m}^{n}(-1)^{i - m}\binom{i}{m}f_{i}
$$

那么我们就求得了 $g_{m}$，就是答案。时间复杂度 $\mathcal{O}(n)$

### [BZOJ2839 集合计数](https://www.luogu.com.cn/problem/P10596)

还是由**至少**转到**恰好**。

设 $f_{k}$ 表示钦定有 $k$ 个相同元素的情况，那么，剩下的 $n - k$ 个元素能够组成 $2^{n - k}$ 个集合，而，这 $2^{n - k}$ 个集合又能够再组成 $2^{2^{n - k}}$ 个集合。那么显然有 

$$
f_{k} = \binom{n}{k}2^{2^{n - k}}
$$

设 $g_{k}$ 表示恰好有 $k$ 个相同元素的情况，那么有：

$$
f_{k} = \sum\limits_{i = k}^{n}\binom{i}{k}g_{i}
$$

反演一下得到：

$$
g_{k} = \sum\limits_{i = k}^{n}(-1)^{i - k}\binom{i}{k}f_{i}
$$

我们就得到了 $g_{k}$。时间复杂度 $\mathcal{O}(n)$。

### [P5505 [JSOI2011] 分特产](https://www.luogu.com.cn/problem/P5505)

首先设 $f_{i}$ 表示至少有 $i$ 个同学没有拿到特产的方案数，那么有：

$$
f_{i} = \binom{n}{i} \times \prod\limits_{j}^{m}\binom{a_{j} + n - i - 1}{n - i - 1}
$$

后面那坨表示将 $a_{j}$ 个物品分成 $n - i$ 个可空集的方案数，而前面的是因为我们并没有钦定哪几个人没有，所以要乘一个在 $n$ 个人里面选 $i$ 个的方案数。

接下来我们开始反演。设 $g_{i}$ 表示正好有 $i$ 个人没有，那么有反演公式：

$$
g_{i} = \sum\limits_{j = i}^{n}(-1)^{j - i}\binom{j}{i}f_{j}
$$

而我们要让每一个人都拿到，答案就是 $g_{0}$。这样，我们就做完了。时间复杂度 $\mathcal{O}(n)$

### [BZOJ4665 小 w 的喜糖](https://www.luogu.com.cn/problem/P10597)

依然是正难则反+二项式反演 ~~演都不带演~~

还是从**至少**做起。设 $f_{i}$ 表示至少有 $i$ 个位置相同，$g_{i}$ 表示正好 $i$ 个位置相同。那么有反演：

$$
f_{i} = \sum\limits_{j = n}^{m}\binom{j}{n}g_{j} 
\Longleftrightarrow
g_{i} = \sum\limits_{j = n}^{m}(-1)^{i - n}\binom{j}{n}f_{j}
$$

我们的答案就是 $g_{0}$

现在的问题就在于怎么求 $f_{i}$。发现初始数组的顺序对于答案来说没有影响，那么我们就记录每种颜色的数量并去重，然后开始 dp。设 $dp_{i,j}$ 表示循环到第 $i$ 个颜色，钦定有 $j$ 个元素相同的方案数。那么有

$$
dp_{i,j} = \sum\limits_{k = 0}^{\min(cnt_{i},tmp)}dp_{i - 1,j - k} \times \binom{cnt_{i}}{k} \times \binom{tmp - j}{cnt_{i} - k}
$$

~~初学时属实没想到这玩意还能带 dp~~

很明显，$f_{i} = dp_{m,i}$，其中 $m$ 表示颜色总数。然后我们就得出了答案，时间复杂度为 $\mathcal{O}(n^2)$。

code:
```cpp
sort(a + 1,a + n + 1);
int m = unique(a + 1,a + n + 1) - a - 1,tmp = 0;
dp[0][0] = 1;
for (int i = 1; i <= m; i++)
{
    tmp += cnt[a[i]];
    for (int j = 0; j <= tmp; j++)
        for (int k = 0; k <= min(j,cnt[a[i]]); k++)
            dp[i][j] = (dp[i][j] + dp[i - 1][j - k] * c(tmp - j,cnt[a[i]] - k) % mod * c(cnt[a[i]],k) % mod) % mod;
}
int ans = 0;
for (int i = 0; i <= n; i++)
    ans = ((ans + (i & 1 ? -1 : 1) * dp[m][i]) % mod + mod) % mod;
```

## 好题推荐

[P4448 [AHOI2018初中组] 球球的排列](https://www.luogu.com.cn/problem/P4448)  
[P3158 [CQOI2011] 放棋子](https://www.luogu.com.cn/problem/P3158)  
[P3160 [CQOI2012] 局部极小值](https://www.luogu.com.cn/problem/P3160)