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

### 二项式定理

二项式，就是有两项的多项式。形如：

$$
a + b 
$$

很简单吧？现在我们来考虑这个东西：

$$
(a + b) ^ i
$$

这十分的让人迷惑对吧？没事，我们先来看看低次的：

$$
\begin{aligned}
(a + b) ^ 0 & = 1 \\ 
(a + b) ^ 1 & = a^1 + b^1 \\
(a + b) ^ 2 & = a^2 + 2a^1b^1 + b^2 \\
(a + b) ^ 3 & = a^3 + 3a^2b^1 + 3a^1b^2 + b^3
\end{aligned}
$$

似乎有点规律？我们先不管系数，看指数，猛地发现：

$$
\begin{aligned}
(a + b)^i & = ?a^i b^0 + ?a^{i - 1}b^1 + ?a^{i - 2}b^2 + \cdots + ?a^0b^i \\
& = \sum\limits_{j = 0}^{i}?a^j b^{i - j}
\end{aligned}
$$

我们再把每一行的每一项系数列出来？

$$
1 \\
1 \; 1 \\
1 \; 2 \; 1 \\
1 \; 3 \; 3 \; 1
$$

这不就是杨辉三角吗？而杨辉三角又可以写成这样：

$$
\binom{0}{0} \\
\binom{1}{0} \; \binom{1}{1} \\
\binom{2}{0} \; \binom{2}{1} \; \binom{2}{2} \\
\binom{3}{0} \; \binom{3}{1} \; \binom{3}{2} \; \binom{3}{3} 
$$

也就是说：杨辉三角的第 $i$ 行第 $j$ 个数是 $\binom{i}{j}$ 。这里，行和列都是从 $0$ 开始标号的。而回到上面的式子，我们发现，$(a + b)^i$ 的第 $j$ 项的系数是杨辉三角的 第 $i$ 行第 $j$ 个数，也就是 $\binom{i}{j}$。那么，我们便可以写出公式：

$$
(a + b) ^ i = \sum\limits_{j = 0}^{i}\binom{i}{j}a^j b^{i - j}
$$

这便是二项式定理了。

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

### 二项式定理

二项式，就是有两项的多项式。形如：

$$
a + b 
$$

很简单吧？现在我们来考虑这个东西：

$$
(a + b) ^ i
$$

这十分的让人迷惑对吧？没事，我们先来看看低次的：

$$
\begin{aligned}
(a + b) ^ 0 & = 1 \\ 
(a + b) ^ 1 & = a^1 + b^1 \\
(a + b) ^ 2 & = a^2 + 2a^1b^1 + b^2 \\
(a + b) ^ 3 & = a^3 + 3a^2b^1 + 3a^1b^2 + b^3
\end{aligned}
$$

似乎有点规律？我们先不管系数，看指数，猛地发现：

$$
\begin{aligned}
(a + b)^i & = ?a^i b^0 + ?a^{i - 1}b^1 + ?a^{i - 2}b^2 + \cdots + ?a^0b^i \\
& = \sum\limits_{j = 0}^{i}?a^j b^{i - j}
\end{aligned}
$$

我们再把每一行的每一项系数列出来？

$$
1 \\
1 \; 1 \\
1 \; 2 \; 1 \\
1 \; 3 \; 3 \; 1
$$

这不就是杨辉三角吗？而杨辉三角又可以写成这样：

$$
\binom{0}{0} \\
\binom{1}{0} \; \binom{1}{1} \\
\binom{2}{0} \; \binom{2}{1} \; \binom{2}{2} \\
\binom{3}{0} \; \binom{3}{1} \; \binom{3}{2} \; \binom{3}{3} 
$$

也就是说：杨辉三角的第 $i$ 行第 $j$ 个数是 $\binom{i}{j}$ 。这里，行和列都是从 $0$ 开始标号的。而回到上面的式子，我们发现，$(a + b)^i$ 的第 $j$ 项的系数是杨辉三角的 第 $i$ 行第 $j$ 个数，也就是 $\binom{i}{j}$。那么，我们便可以写出公式：

$$
(a + b) ^ i = \sum\limits_{j = 0}^{i}\binom{i}{j}a^j b^{i - j}
$$

这便是二项式定理了。

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
\longleftrightarrow
f_{n} = \sum\limits_{i = 0}^{n}(-1)^i\binom{n}{i}g_{i}
$$

然而它的变形似乎更加常用：

$$
g_{n} = \sum\limits_{i = 0}^{n}\binom{n}{i}f_{i}
\longleftrightarrow
f_{n} = \sum\limits_{i = 0}^{n}(-1)^{n - i}\binom{n}{i}g_{i}
$$

如何证明这个公式是正确的呢？