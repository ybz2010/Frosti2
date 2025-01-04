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
 = \sum\limits_{m = 1}^{n}(-1)^{m - 1}\sum\limits_{a_i < a_{i - 1}}|\bigcap_{i = 1}^{m}S_{a_{i}}|
$$

证明的思路是考虑一个元素在每一个 $\bigcap\limits_{i = 1}^{m}S_{a_{i}}$ 里出现的次数，然后通过一番暴算，我们能够发现每个元素都只出现了 $1$ 次，这样，每个元素合起来就变成了总的并集。

详见[OI-wiki](https://oi.wiki/math/combinatorics/inclusion-exclusion-principle/)

## 二项式反演

### 反演的定义

现在我们有两个数组：$f$ 和 $g$。而 $g$ 和 $f$ 之间有对应关系：

$$
g_{n} = \sum\limits_{i = 0}^{n}a_{i} \times f_{i}
$$

然而题目里不可能这么简单，一般是已知 $g$ 让我们推 $f$。这种情况解个方程就可以。但是如果只有解方程的话，还不足以搞出“反演”这种东西出来，而上面的柿子在某些情况下会化出许多种~~恶心~~**优美**的形式，所以才搞出了反演这种东西。

### 二项式反演的公式

我们可以从容斥原理推到二项式反演的公式。

有全集 $U = S_{1} \cup S_{2} \cup S_{3} \cup \cdots \cup S_{n}$，且任意 $i$ 个集合的交集和并集大小相同。设 $g_{i}$ 表示任意 $i$ 个集合的并集的大小，$f_{i}$ 表示任意 $i$ 个集合的补集的大小。特别的，$f_{0} = g_{0} = |U|$。根据补集的交集和原集的并集的容斥关系可以得出：

$$
\begin{aligned}
|\bigcap\limits_{i = 1}^{n}S_{i}| 
& = |U| - |\bigcup\limits_{i = 1}^{n}\overline{S_{i}}| \\
& = |U| - (|\overline{S_{1}}| + |\overline{S_{2}}| + \cdots + (-1)^{n - 1}|\overline{S_{1}} \cap \overline{S_{2}} \cap \cdots \cap \overline{S_{n}}|) \\
& = |U| - |\overline{S_{1}}| - |\overline{S_{2}}| - \cdots + (-1)^{n}|\overline{S_{1}} \cap \overline{S_{2}} \cap \cdots \cap \overline{S_{n}}| \\
& = \sum\limits_{i = 0}^{n}(-1)^i\binom{n}{i}f_{i}
\end{aligned}
\\
\begin{aligned}
|\bigcap\limits_{i = 1}^{n}\overline{S_{i}}|
& = |U| - |\bigcup\limits_{i = 1}^{n}S_{i}| \\
& = |U| - (|S_{1}| + |S_{2}| + \cdots + (-1)^{n - 1}|S_{1} \cap S_{2} \cap \cdots \cap S_{n}|) \\
& = |U| - |S_{1}| - |S_{2}| - \cdots + (-1)^{n}|S_{1} \cap S_{2} \cap \cdots \cap S_{n}|) \\
& = \sum\limits_{i = 0}^{n}(-1)^i\binom{n}{i}g_{i}
\end{aligned}
$$

然而，我们惊奇的发现：$|\bigcap\limits_{i = 1}^{n}S_{i}| = g_{n},|\bigcap\limits_{i = 1}^{n}\overline{S_{i}}| = f_{n}$，于是我们便得到了二项式反演的第一个公式：

$$
g_{n} = \sum\limits_{i = 0}^{n}(-1)^i\binom{n}{i}f_{i}
\Longleftrightarrow
f_{n} = \sum\limits_{i = 0}^{n}(-1)^i\binom{n}{i}g_{i}
$$

然而它的变形似乎更加常用：

$$
g_{n} = \sum\limits_{i = 0}^{n}\binom{n}{i}f_{i}
\Longleftrightarrow
f_{n} = \sum\limits_{i = 0}^{n}(-1)^{n - i}\binom{n}{i}g_{i}
$$

如何用数学方法证明这个公式是正确的呢？直接代入就行。  
将 $g_{n} = \sum\limits_{i = 0}^{n}(-1)^i\binom{n}{i}f_{i}$ 代入：

$$
\begin{aligned}
f_{n} & = \sum\limits_{i = 0}^{n}(-1)^i\binom{n}{i}\sum\limits_{j = 0}^{i}(-1)^j\binom{i}{j}f_{j} \\
& = \sum\limits_{i = 0}^{n}\sum\limits_{j = 0}^{i}(-1)^{i + j}\binom{n}{i}\binom{i}{j}f_{j} \\
& = \sum\limits_{i = 0}^{n}\sum\limits_{j = 0}^{i}(-1)^{i + j}\binom{n}{j}\binom{n - j}{n - i}f_{j} \\
& = \sum\limits_{j = 0}^{n}\binom{n}{j}f_{j}\sum\limits_{i = 0}^{n - j}(-1)^i\binom{n - j}{i} \\
& = \sum\limits_{j = 0}^{n}\binom{n}{j}f_{j}[j = n] \\
& = f_{n}
\end{aligned}
$$

显然成立。当然，这个柿子里还有一些不那么显然的东西

1. $\displaystyle\binom{n}{i}\binom{i}{j} = \binom{n}{j}\binom{n - j}{n - i}$  
这个可以用数学方法证明，也可组合意义的方法证明，这里主要说明组合意义的证法。  
求 $|U| = n,|A| = i,b = |j|,B \subseteq A \subseteq U$ 的方案数。这里有两种方法：  
法一：先在 $U$ 里取 $i$ 个元素构成集合 $A$，方案数为 $\binom{n}{i}$，然后在 $i$ 个元素里再取 $j$ 个元素构成集合 $B$，方案数为 $\binom{i}{j}$，合起来就是 $\binom{n}{i}\binom{i}{j}$  
法二：先在 $U$ 里取 $j$ 个元素构成集合 $B$，方案数为 $\binom{n}{j}$，再在剩下的 $n - j$ 个元素里取 $i - j$ 个元素，方案数为 $\binom{n - j}{i - j}$，又有 $\binom{n - j}{i - j} = \binom{n - j}{n - i}$，所以方案数就是 $\binom{n}{j}\binom{n - j}{n - i}$。  
得证。
2. 