---
title: 从 border 到 lyndon - runs
tags: ["字符串"]
categories: "算法笔记"
mathjax: true
---

系统性的介绍 ```border``` 理论到 ```lyndon - runs``` 相关的内容。基本复读 ```command_block``` 的博客，详细了部分推导的过程，~~完全抛弃了形象理解~~。补充了少量题目，修正了少量笔误。

~~（好吧其实抄一遍只是为了更好理解推导过程，建议大家都去抄一遍（乐。~~

<!--more-->

## ```border``` 理论

### 定义和前置内容

以下字符串均为 $1$ 下标开始。

- $\text{Border}$：字符串的真前缀，满足其同时为原串的后缀。即若 $S[1,m]=S[n-m+1,n]$ 且 $m\ne n$，则称 $S[1,m]$ 是原串的一个 $\text{Border}$。后文简记为 $\text{Bd}$。

  记 $\text{Bd}(S)$ 为 $S$ 的 $\text{Bd}$ 集合，记 $\text{mxBd}(S)$ 为 $S$ 的最大 $\text{Bd}$。

- 周期：若对整数 $p$ 满足对 $\forall 1\le i\le n$ 都有 $S[i]=S[i+p]$ 或 $i+p>n$，则称 $p$ 是 $S$ 的周期。若 $p|n$，称 $p$ 为 $S$ 的一个整周期。

  不难发现，长为 $p$ 的 $\text{Bd}$ 对应着长为 $n-p$ 的周期。

### ```border``` 与周期的相关定理

#### ```border``` 的基本定理

- **定理一**：$\text{Bd}(S)=\text{mxBd}(S)+\text{Bd}(\text{mxBd}(S))$

  分别推就可以了，这提示我们原串的 $\text{Bd}$ 可以被 $\text{kmp}$ 的 $\text{fail}$ 树上的一条从根出发的路径来表示。

#### 关于周期的性质

- **定理二**（$\text{Weak Periodicity Lemma}$)：

  若 $p,q$ 为 $S$ 的周期，且 $p+q\le n$，则 $\gcd(p,q)$ 也是 $S$ 的周期。 

  证明：不妨设 $p>q$，令 $d=p-q$。

  - 当 $i>q$ 时，有 $s[i]=s[i-q]=s[i-q+p]=s[i+d]$。

  - 当 $i<p$ 时，有 $s[i]=s[i+p]=s[i+p-q]=s[i+d]$。

  - 这两种情况完整考虑了全部 $s$ 的字符，因此 $d$ 也是周期，辗转相减即可。

- **定理三**：若 $S$ 是 $T$ 的前缀，且 $T$ 有周期 $a$，$S$ 有整周期 $b$，$b|a$，$|S|\ge a$，则 $T$ 有周期 $b$。

  显然成立，考虑对 $i>a$，有 $s[i]=s[i\%a]=s[i\%a\%b]$，再适当补充 $a$ 和 $b$ 到合适位置就可以了。

- **定理四**：若 $S$ 在 $T$ 中有两次相邻且部分重叠的匹配，那么设对第一次匹配而言非重叠部分长为 $d$，两次匹配覆盖的部分整体有长为 $d$ 的周期。

  显然成立，可知 $S$ 有长为 $|S|-d$ 的 $\text{Bd}$，于是 $S$ 有周期 $d$。于是两次匹配恰好相差一个周期，那么整体也有该周期。

- **定理五**：若 $2|S|\ge|T|$，则 $S$ 在 $T$ 中的出现位置必为等差数列。

  证明：考虑去掉 $T$ 中未被匹配覆盖的位置，只考虑被覆盖位置。

  - 若出现次数不超过两次，那么显然为等差数列。

  - 若恰好出现三次。
  - 设前两次匹配之间的距离为 $d$，后两次间距离为 $q$。根据定理三，$S$ 有周期 $d,q$。又 $2|S|\ge |T|$，$d+q+|S|=|T|$ 得 $d+q\le |S|$，于是根据定理二，我们有 $r=\gcd(d,q)$ 是周期。设最小周期 $p_{min}$，则应用定理二，得知 $p_{min}|r$。
  - 根据定理四，定理三，可知 $S_1\cup S_2$ 有周期 $p_{min}$。那么若 $d>p_{min}$，那么第二次匹配一定可以前移，所以 $d=p_{min}$。于是 $d|q$，那么所有匹配的循环节都是对应的，于是每一个间隔都为 $p_{min}$，这样此时匹配一定为等差序列。
  - 更多次与此相同，得证。

#### ```border``` 的更多性质

- **定理六**：$S$ 的长度达到 $n/2$ 的 $\text{Bd}$ 构成一个等差数列。

  证明：设长度最大的 $\text{Bd}$ 为 $n-p$，另有一 $\text{Bd}$ 为 $n-q$ 且 $p<q\le n/2$。

  - 差必然为 $p$ 的倍数：由定理二可知 $\gcd(p,q)$ 亦为周期，又 $\gcd(p,q)\ge p$，于是 $p|q$。
  - 存在性：若其有周期 $p$，那么显然有 $s[i]=s[i+2p]$，其也有周期 $2p$。
  - 综上，命题得证。

- **定理七**：$S$ 的所有 $\text{Bd}$ 按长度排序后，可以被划分成 $O(\log n)$ 个等差数列。

  证明：

  - 长度达到 $n/2$ 的部分可划分为一个等差数列。
  - 现在，我们总可以找出一个 $T$，使 $|T|\le \frac{3}{4}n$，我们又知道所有长度小于 $T$ 的 $\text{Bd}$ 都是 $T$ 的 $\text{Bd}$（由定理一），那么问题规模被缩减为原先的 $\frac{3}{4}$，缩减 $O(\log)$ 轮后就会缩减至一。

  推论：

  - $S$ 的公差 $\ge d$ 的 $\text{Bd}$ 等差数列总大小是 $O(\frac{n}{d})$ 的。

### 最小后缀

#### 定义

记 $\text{suf}(S)$ 为 $S$ 的后缀集合，记 $\text{minsuf}(S)$ 为 $S$ 的最小后缀，记 $\text{Ssuf}(S)$ 为”在 $S$ 后面加一个串后可能成为最小后缀的后缀”的集合，也就是 $\{V\in \text{suf}(S)|\exist T,VT=\text{minsuf}(ST)\}$。

#### 相关定理

- **定理八**：对于任意两个 $\text{Ssuf}\ U,V$，$|U|<|V|\Rightarrow$ $U$ 是 $V$ 的前缀。 

  容易证明，又 $U$ 是 $V$ 的后缀，即 $U$ 是 $V$ 的 $\text{Bd}$。

- **定理九**：若 $S$ 有两个 $Ssuf\ U,V$，满足 $|U|<|V|$，则 $2|U|\le|V|$。

  证明：考虑反证

  - 设 $|U|<|V|<2|U|$，我们知道 $U$ 是 $V$ 的 $\text{Bd}$。也就是 $V$ 有长度 $<\frac{|V|}{2}$ 的周期，设一个周期对应的字符串为 $T$，于是设 $U=TC$，$V=TTC$。
  - 假设我们加入的字符串是 $R$，这时 $UR$ 是最小后缀，那么就有 $UR<VR$，也就是 $TCR<TTCR$，即 $CR<TCR$，那么 $C$ 开始的后缀更优，$UR$ 一定无法成为最小后缀，矛盾。
  - 原命题得证。

  推论：

  - $|\text{Ssuf} (S)|$ 是 $O(log|S|)$ 级别的。

### 例题

#### ```border``` 基本应用

- [P3435 POI2006 OKR-Periods of Words](https://www.luogu.com.cn/problem/P3435)
- [P2375 NOI2014 动物园](https://www.luogu.com.cn/problem/P2375)
- [P5829 【模板】失配树](https://www.luogu.com.cn/problem/P5829)
- [P3426 POI2005 SZA-Template](https://www.luogu.com.cn/problem/P3426)

#### 周期和匹配的性质

- [P4156 WC2016 论战捆竹竿](https://www.luogu.com.cn/problem/P4156)
- [Loj#6681. yww 与树上的回文串](https://loj.ac/p/6681)
- [CF1286E Fedya the Potter Strikes Back](https://www.luogu.com.cn/problem/CF1286E)
- [P5287 HNOI2019 JOJO](https://www.luogu.com.cn/problem/P5287)
- [P4482 BJWC2018 Border 的四种求法](https://www.luogu.com.cn/problem/P4482)

#### 最小后缀的性质

- [P5211 ZJOI2017 字符串](https://www.luogu.com.cn/problem/P5211)

## ```Lyndon Word``` 和 ```Lyndon``` 分解 

### 定义和前置内容

- $\text{Lyndon Word}$：若字符串 $s$ 的最小后缀是其本身，则称其为 $\text{Lyndon Word}$，简记为 $\text{Ly}$。容易发现，”$s$ 是 $s$ 所有循环位移中最小的一个”是该定义的一个等价定义。

  $\texttt{ababc}$ 是 $\text{Ly}$，而 $\texttt{acabc}$ 不是。

- $\text{Lyndon}$ 分解：将字符串 $s$ 分解为 $s_1,s_2,\dots,s_n$，满足其均为 $\text{Ly}$，且 $s_1\le s_2 \le \dots \le s_n$。下面我们会证明串的 $\text{Lyndon}$ 分解是存在且唯一的。

### ```Lyndon Word``` 的性质

- **定理 1.1**：对任意 $\text{Ly} \ s$，其不存在 $\text{Bd}$。

  显然。若其存在 $\text{Bd} \ t$，那么有 $t<s$ 且 $t$ 是 $s$ 的真后缀，矛盾。 

- **定理 1.2**：对于 $Ly\ s=ab$，有 $a<b$。（$a,b$ 均非空。）

  显然，有 $ab<b$，又 $a<ab$，于是 $a<b$。

- **引理 1.1**：若 $a$ 不是 $b$ 的前缀，有 $ac_1<bc_2 \Leftrightarrow a<b$。

  显然。

- **定理 1.3**：对于 $\text{Ly}\ b$ 和任意字符串 $a$，有 $a<b \Leftrightarrow ab<b$。

  证明：

  - 由引理 1.1，若 $a$ 不是 $b$ 的前缀，原命题成立。
  - 若 $a$ 是 $b$ 的前缀，令 $b=at$，此时只考虑由 $a<b$ 推出的部分。只需证明 $aat<at$，也就是 $at<t$，即 $b<t$，因 $b$ 是 $\text{Ly}$，得证。

- **定理 1.4**：$s$ 是 $\text{Ly}$ 当且仅当 $s=ab,\ a<b$ 且 $a,b$ 均为 $\text{Ly}$。（$a,b$ 非空，$|s|>1$）。

  证明：

  - 充分性（后推前）：$ab$ 的严格后缀有 $\text{suf}’(a)b,b,\text{suf}'(b)$ 三类。

    - $a$ 为 $\text{Ly}$，因而 $a$ 没有 $\text{Bd}$，因而 $\text{suf}'a$ 不是 $a$ 的前缀，由引理 1,1，显然 $a>\text{suf}'(a)b$。
    - 由定理 1.3，$ab<b<\text{suf'}(b)$。

  - 必要性（前推后）：

    取 $s$ 的最小严格后缀 $s[i,n]$，由定理 1.2 有 $s[1,i-1]<s[i,n]$。容易证明 $s[i,n]$ 是 $\text{Ly}$。

    现在考虑前缀 $s[1,i-1]$，若其存在 $\text{Bd}\ s[1,k]$，那么我们有 $s[k+1,n]>s[i,n]$ 以及 $s[1,k]=s[i-k,i-1]$，于是 $s>s[i-k,n]$，矛盾，于是该前缀没有 $\text{Bd}$。

    于是，对 $1<j\le i-1$ 的 $j$，$s[1,i-1]+s[i,n]=s<s[j,n]=s[j,i-1]+s[i,n]$，又因 $s$ 无 $\text{Bd}$，有 $s[j,i-1]$ 不是 $s[1,i-1]$ 的前缀，应用引理 1.1，我们就得到 $s[1,i-1]<s[j,i-1]$，即 $s[1,i-1]$ 是 $\text{Ly}$。

  - 原命题得证。

- **定理 1.5**：若 $s$ 和字符 $\overline x$ 满足 $s\overline x$ 是某个 $\text{Ly}$ 的前缀，则对 $\overline y > \overline x$，$s\overline y$ 是 $\text{Ly}$。

  证明：

  - 设 $s\overline x t$ 是 $\text{Ly}$，考虑 $1<j<|s|$。
  - 若 $s[j,n]\overline x$ 是 $s$ 的前缀，则对 $s[j,n]\overline x<s[j,n]\overline y$ 使用引理 1.1，得到 $s\overline y<s[j,n]\overline y$。
  - 若不是，那么对 $s[j,n]\overline x t>s\overline xt$ 连续使用引理 1.1，得到 $s[j,n]\overline y>s\overline y$。
  - 综上，总有 $s\overline y<s[j,n]\overline y$，原命题得证。

- **定理 1.6**：$\text{Lyndon}$ 分解唯一。

  证明：考虑存在两种分解，分别为 $s_1,s_2,\dots,s_n$ 和 $s_1',s_2',\dots,s_m'$。

  - 设 $i$ 为第一个满足 $s_i\ne s_i'$ 的下标，不妨设 $|s_i|>|s_i'|$，且 $s_i=s_i's_{i+1}'\dots s_k'[1,j]$。
  - 有 $s_i<s_k'[1,j]\le s_k' \le \dots \le s_i' < s_i$，矛盾。
  - 原命题得证。

- **定理 1.7**： $\text{Lyndon}$ 分解存在。

  证明：

  - 开始时将 $s$ 分解为 $n$ 个串，每个串均为单个字符，显然为 $\text{Ly}$。
  - 不断重复如下过程：若存在相邻两串满足 $s_i<s_{i+1}$，合并之，直至不可合并位置。由定理 1.4，这样的两个串合并后仍是 $\text{Ly}$。容易证明合并次数有限，结束时便可得到一个合法的分解。
  - 原命题得证。

### 构造 ```Lyndon``` 分解

下面介绍 $\text{Duval}$ 算法，其可以在 $O(n)$ 时间内求得字符串的 $\text{Lyndon}$ 分解。核心思想为不断求目标串的最长 $\text{Ly}$ 前缀。

- **定理 2.1**：设 $s=u^ku'\overline a$，其中 $u$ 是 $\text{Ly}$，$u'$ 是 $u$ 的真前缀，$\overline a$ 是某个字符满足 $\overline a\ne u[|u'|+1]$，即前缀 $u'$ 不可延伸。那么：
  - 若 $\overline a>u[|u'|+1]$，根据定理 1.5，$u'\overline a$ 是 $\text{Ly}$，且 $u'\overline a>u^ku'\overline a>u$，于是根据定理 1.4，我们可以将 $u^k$ 全部合并，此时 $s$ 即为一个 $\text{Ly}$。
  - 若 $\overline a<u[|u'|+1]$，$s$ 的最长前缀 $\text{Ly}$ 为 $u$。考虑若我们选择 $u^ku'$ 的一个长度超过 $|u|$ 的前缀，那该前缀必有 $\text{Bd}$，一定不是 $\text{Ly}$。若选择 $u^ku'\overline at$，依照定理 1.1，一定有 $u'\overline a t<u^ku'\overline at$，一定不是 $\text{Ly}$，因此仅可选择 $u$。

只要我们不断保持定理 2.1 的形式，就可以不断分离出最长的 $\text{Ly}$ 前缀，进而得到分解。下面介绍算法流程。

我们维护两个指针 $l,r$，其中 $s[1,l-1]$ 已经分解完成，$s[l,r]$ 为满足定理 2.1 中形式的正在被处理的子段，设 $s[l,r]=u^ku'$，其中 $u$ 是 $\text{Ly}$，$u'$ 是 $u$ 的真前缀，记录 $|u|,k,|u'|$。现在考虑我们加入字符 $s[r+1]$：（当 $r+1>n$ 时视新字符为 $0$。）

- $s[r+1]=s[r-|u|+1]$：延长 $u'$。
- $s[r+1]>s[r-|u|+1]$：根据定理 2.1，$s[l,r+1]$ 是 $\text{Ly}$，成为新的 $u$。
- $s[r+1]<s[r-|u|+1]$：根据定理 2.1，最长合法前缀为 $u$，我们在分解中加入 $k$ 个 $u$，前推 $l$ 到 $u'$ 起始位置，并令 $r=l$。

容易证明 $l+r$ 总是递增，因此算法时间复杂度为 $O(n)$。

稍微修改一下算法，我们就可以用其来求出 $s$ 每个前缀的最小/最大后缀。只需要在流程中实时维护即可。

- $s[r+1]=s[r-|u|+1]$：继承上个位置的答案并后移一个周期。
- $s[r+1]>s[r-|u|+1]$：根据定理 2.1，$s[l,r+1]$ 是 $\text{Ly}$，答案即为 $l$。
- $s[r+1]<s[r-|u|+1]$：指针左移，并遂算法流程一起重算 $u'$ 部分的答案。

### 例题

- [P6114 【模板】Lyndon 分解](https://www.luogu.com.cn/problem/P6114)
- [HDU6761 Minimum Index](http://acm.hdu.edu.cn/showproblem.php?pid=6761)
- [CF594E Cutting the Line](https://www.luogu.com.cn/problem/CF594E)

## ```Runs``` 理论

### 定义和前置内容

- $\text{Runs}$：三元组 ${\bf r}=(l,r,p)$ 是 $s$ 的一个 $\text{run}$，当且仅当：

  - $s[l,r]$ 的最小周期为 $p$，满足 $2p\le r-l+1$。
  - 该循环是极长的，即 $s[l-1]\ne s[l+p-1]$ 且 $s[r+1]\ne s[r-p+1]$。

  记 $e_{\bf(r)}=\frac{r-l+1}{p}$ 为 $\bf{r}$ 的指数，记 $\text{Runs}(s)$ 为 $s$ 的所有 $\text{run}$ 组成的集合。

  记 $\rho_{\text{run}}(n)$ 为长度为 $n$ 的字符串中 $\text{run}$ 数量的最大值。

  记 $\sigma_{\text{run}}(n)$ 为长度为 $n$ 的字符串中 $\text{run}$ 指数和的最大值。

- $\text{Lyndon Root}$：令 ${\bf r}=(l,r,p)$ 是串 $s$ 的一个 $\text{run}$。称长为 $p$ 的区间 $[i,j]$ 为 $\text{Lyndon Root}$，当且仅当 $s[i,j]$ 是 $\text{Ly}$，且 $l\le i \le j \le r$，即周期的最小表示。 后面简记为 $\text{LyRoot}$。

### ```The Runs Theorem```

- **定理 3.1**：两个周期为 $p$ 的 $\text{run}$ 的交长度 $<p$。

  容易证明，若交的长度 $>p$，则可以进行拓展，和定义矛盾。

- **定理 3.2**（$\text{The Runs Theorem}$)：

  $\rho _{\text{run}}(n)<n,\sigma_\text{run}(n)<3n-3$。

  证明：

  - 对于 $\Sigma$ 中的全序关系 $<$，定义其正序 $<_0$ 和其反序 $<_1$。在比较时，我们在字符串末尾填充无穷多空白字符 $\texttt{\$} \notin \Sigma$，并约定对任意 $\overline a\in \Sigma$，$\texttt{\$}<_0 \overline a$，$\overline a<_1\texttt{\$}$。

  - 定义 $\text{Ly}_{s,f}(i)=[i,j]$ 满足 $s[i,j]$ 是以 $i$ 开头最长的 $<_f$ 意义下的 $\text{Ly}$。

  - **定理 3.3**：对于 $\text{Ly}_{s,f}(i)$，有且仅有一个 $f\in\{0,1\}$ 可以导出非平凡 $\text{Ly}$ （称长为 $1$ 的 $\text{Ly}$ 为平凡的）。

    证明：

    -  找到 $k>i$ 为第一个使 $s[k]\ne s[i]$ 的位置，于是存在 $f$ 使得 $s[k]<_fs[i]$。不难得到此时 $\text{Ly}_{s,f}(i)=[i,i]$，且在 $¬f$ 意义下 $s[i,k]$ 是 $\text{Ly}$。于是 $\text{Ly}_{s,¬f}(i)=[i,j]$ 其中 $j\ge k>i$。
    - 原命题得证。

  - **定理 3.4**：令 ${\bf r}=(l,r,p)$ 是 $s$ 的一个 $\text{run}$，找出 $f$ 使得 $s[r+1]<_fs[r+1-p]$，则 $\bf r$ 的所有关于 $<_f$ 的 $\text{LyRoot} \ \lambda = [i,j]$ 都与 $\text{Ly}_{s,f}(i)$ 相等。

    容易证明，考虑从第一个 $\text{LyRoot}$ 开始的位置，将 $s[l,r]$ 写成 $u^ku'$ 的形式，应用定理 2.1，即可得到上述结论。此时，我们记 $f$ 为 $\bf r$ 的正序。

  - 对 $\text{run}\ {\bf r}=(l,r,p)$，记 $\text{LyR}({\bf r})$ 为在 $\bf r$ 的正序下所有 $\text{LyRoot}$ 的集合，但（若存在）排除以 $l$ 开头的 $\text{LyRoot}$。我们有 $|\text{LyR}({\bf r})|\ge \lfloor e_{\bf r}-1\rfloor\ge 1$。

  - 记 $\text{Beg}(\mathbb{S})$ 为集合 $\mathbb{S}$ 中所有字符串开始位置的集合。

  - **定理 3.5**：对 $s$ 的两个不同 $\text{run} \ {\bf r}=(l,r,p),{\bf r'}=(l',r',p')$，有 $\text{Beg}(\text{LyR({\bf r})})\cap \text{Beg}(\text{LyR}({\bf r'}))=\varnothing$。

    证明：

    - 假设存在 $i\in \text{Beg}(\text{LyR({\bf r})})\cap \text{Beg}(\text{LyR}({\bf r'}))$，且有 $\text{LyRoot} \ \lambda =[i,j]\in \text{LyR}({\bf r})$，$\lambda'=[i,j']\in \text{LyR}({\bf r'})$。令 $<_f$ 为 $\bf r$ 的正序，于是有 $\lambda=\text{Ly}_{s,f}(i)$。
    - 依据定理 3.1，一定有 $\lambda\ne \lambda'$，于是 $\lambda'=\text{Ly}_{s,\neg f}(i)$。依据定理 3.3，二者之中必有一个为 $[i,i]$，不妨设 $\lambda = [i,i]$，于是有 $j'>i$。
    - 依据定理 3.4，有 $p=|\lambda|=1$，$p'=|\lambda'|=j-i+1$，又有 $l,l'<i$。于是推知 $s[i]=s[i-1]=s[i-1+j'-i+1]=s[j']$，但因 $s[i,j']$ 是 $\text{Ly}$，其不存在 $\text{Bd}$，矛盾。
    - 原命题成立。

  - 定理 3.5 指出 $\text{run}$ 拥有两两不交且不含 $1$ 的下标集合。因此，我们已经成功证明了定理 3.2 的第一部分，即 $\rho_{\text{run}}(n)<n$。

  - 我们现在有 $\sum|\text{LyR}({\bf r})|\le n-1$，又 $e_{\bf r}-2\le \lfloor e_{\bf r}-1\rfloor \le |\text{LyR}({\bf r})|$，于是有 $\sum e_{\bf r}-2\le \sum |\text{LyR}({\bf r})| \le n-1$，结合定理第一部分，我们就得到定理的第二部分，也就是 $\sigma_{\text{run}}(n)\le 3n-3$。

  - 原命题得证。

  引理：$\rho_{\text{run},k}(n)<\frac{n}{k-1}$，其中 $\rho_{\text{run},k}$ 指指数达到 $k$ 的 $\text{run}$ 数。这是容易证明的。

### 关于 ```Runs``` 的计算

#### 寻找 ```Runs```

枚举周期 $p$，每隔 $p$ 个位置设置一个关键点，那么一个 $\text{run}$ 至少覆盖两个关键点。对相邻的关键点分别向前向后求最长公共前缀，若覆盖范围相接，则找到了一个可能的 $\text{run}$。注意，此时的 $p$ 并不一定是该 $\text{run}$ 的最小周期，因此需要按 $(l,r)$ 分类，每类中保留 $p$ 最小的一个。

用哈希实现复杂度为 $O(n\log^2n)$，用后缀数组可以做到 $O(n\log n)$。

#### 计算 $\text{Ly}_s(i)$

维护每个后缀的 $\text{Lyndon}$ 分解，取出分解的第一项即可。那么在维护时，每次我们会在 $\text{Lyndon}$ 分解序列的第一项插入单个字符形成的 $\text{Ly}$，然后若 $s_1<s_2$ 则不断合并即可，可以简单的用哈希来实现比大小。

在比较 $s_1<s_2$ 时，我们可以应用定理 1.3 以及引理 1.1，将 $s_1<s_2$ 化为 $s_1s_2\dots s_n<s_2\dots s_n$。

#### 寻找 ```Runs 2``` 

由定理 3.4，一个 $\text{run}$ 所含的 $\text{Ly}$ 循环节一定为某个字典序意义下某个后缀的最长 $\text{Ly}$ 前缀。且根据定理 3.1，两个不同的 $\text{run}$ 不能包含同个循环节。于是现在只需对每个 $\text{Ly}_{s,f}(i)=[l,r]$ 的 $l,r$ 分别向前向后求最长公共前缀即可。

用哈希实现，即可做到 $O(n\log n)$。

### 例题

- [Loj#173. Runs](https://loj.ac/p/173)

## ```Lyndon Tree```

### 定义和前置内容

- 标准分解：定义 $\text{Ly}\ s$  的标准分解为 $s=ab$，其中 $b$ 为 $s$ 的最小真后缀。据定理 1.4，我们知道 $a$ 也是 $\text{Ly}$ 且 $a<b$。

- $\text{Lyndon Tree}$：

  一棵有根二叉树，其每个节点代表一个 $\text{Ly}$。根节点对应原串 $s$，要求 $s$ 是 $\text{Ly}$。

  对某个节点 $t$，其两个儿子为其标准分解中的 $a,b$，当 $t$ 是平凡的时，其为叶节点。

  将 $s$ 在 $<_f$ 意义下的 $\text {Lyndon Tree}$ 记为 $\text{LyTree}_f(s)$。

  对于非 $\text{Ly}$，定义其 $\text{LyTree}$ 为其 $\text{Lyndon}$ 分解中各 $\text{Ly}$ 的 $\text{LyTree}$ 组成的森林。

### 相关定理

- **定理 5.1**：记 $\text{lca}([l,r])$ 为叶节点 $l\dots r$ 在 $\text{LyTree}$ 上的 $\text{lca}$。若 $s[l,r]$ 是 $\text{Ly}$，则 $\text{lca}([l,r])=[l_0,r_0]$，满足 $l_0=l\le r\le r_0$。

  证明：$l=r$ 显然，只需证明 $l<r$ 的部分。

  - 设 $v_0=[l_0,m_0],v_1=[m_0+1,r_0]$ 分别为 $u=\text{lca([l,r])}$ 的左右儿子。于是有 $l_0\le l \le m_0 < m_0+1 \le r \le r_0$，考虑设 $s[l_0,m_0]=ka,s[l,r]=ab,s[m_0+1,r]=bk'$，其中 $a,b$ 非空。有 $(ka,bk')$ 是 $kabk'$ 的标准分解。
  - 有 $ab$ 是 $\text{Ly}$，应用引理 1.1，有 $ab<b \Rightarrow abd<bd$，则当 $k$ 非空时 $kabk'$ 的最小严格后缀应为 $abk'$，矛盾，所以 $k$ 必空，即 $l_0=l$。
  - 原命题得证。

- **定理 5.2**：从任意一个 $l$ 开始的最长 $\text{Ly}$ 子串 $s[l,r]$ 必然在 $s$ 的 $\text{LyTree}$ 中。

  容易证明，应用定理 5.1，得到 $\text{LyTree}$ 中必然存在 $s[l,r']$ 满足 $r'\ge r$，又 $[l,r]$ 是一个最长子串，因此 $r'=r$。

- **定理 5.3**：$s[l,r]$ 是 $l(l>1)$ 开始的最长 $\text{Ly}$ $\Rightarrow$ 节点 $u=\text{lca}([l,r])$ 是右儿子。

  容易证明，依照定理 5.2，$s[l,r]$ 一定在树上，且其是极长的，因而一定不是某个 $\text{Ly}$ 的前缀。

  推论：依据定理 3.4，对任意 $\text{run} \ {\bf r}$，若其正序为 $f$，则其所有 $\text{LyRoot}$ 都在 $\text{LyTree}_f(s)$ 的右儿子中出现。

### 子串半周期查询

题意：快速查询母串 $s$ 的某个子串是否有不超过长度一半的周期，如有则求出最小周期。

记 $\text{exrun}(l,r)$ 为满足 $l'\le l,r\le r',p\le(r-l+1)/2$ 的一个 $\text{run}\ {\bf r}=(l',r',p)$，即完整覆盖区间且循环节长度小于区间一半的 $\text{run}$。根据 $\text{Border}$ 论部分的定理二，即 $\text{WPL}$，$\text{exrun}$ 一定是唯一的，否则可以缩短循环节。于是，子串半周期查询便等价于求得 $\text{exrun}$。

下面给出算法：

构造 $\text{LyTree}_0(s),\text{LyTree}_1(s)$，分别找到 $a_0=\text{lca}_0([l,\lceil\frac{l+r}{2}\rceil]),a_1=\text{lca}_1([l,\lceil\frac{l+r}{2}\rceil])$。检查二者右儿子作为 $\text{LyRoot}$ 对应的 $\text{run}$ 是否满足条件。

正确性证明：

设存在 ${\bf r}=(l',r',p)$ 为我们所需要求得的 $\text{run}$，由于 $p\le \frac{r-l+1}{2}$，一定存在一个 $\text{LyRoot}\ \lambda=[l_\lambda,r_\lambda]$ 包含 $\lceil\frac{l+r}{2}\rceil$ 这个位置。

设 $\bf r$ 的正序为 $f$，根据定理 5.3，有 $\lambda$ 在 $\text{LyTree}_f(s)$ 中作为右儿子出现。我们又知道 $a_f$ 对应的 $\text{Ly}$ 亦满足其包含位置 $\lceil\frac{l+r}{2}\rceil$，同时其长度 $\ge |[l,\lceil\frac{l+r}{2}\rceil]|>p$。因而可以推导出 $a_f$ 是 $\lambda$ 的祖先。

若其右儿子 $b=[l_b,r_b]\ne \lambda$，根据 $\text{lca}$ 定义，有 $b$ 一定包含 $\lceil\frac{l+r}{2}\rceil$ 且不包含 $l$，则 $b$ 是 $\lambda$ 祖先。由于 $\lambda$ 是右儿子，于是 $l<l_b<l_\lambda$。

此时：

- 若 $r_b\le r'$，那么 $\bf r$ 覆盖 $[l_b,r_b]$，即 $b$ 有周期 $p$，这使得 $b$ 有 $\text{Bd}$，与 $b$ 是 $\text{Ly}$ 矛盾。
- 若 $r_b>r'$，那么有 $s[r'+1]<_f s[r'+1-p]$。考虑有 $l'<l_b$，此时若 $b$ 的开始位置不是某个 $\text{LyRoot}$ 的开始位置，因为 $b$ 一定至少包含 $\lambda$，我们容易得到 $s[l_\lambda,r_b]<_fs[l_b,r_b]$，若 $b$ 的开始位置恰是某个 $\text{LyRoot}$ 的开始位置，那么在经过若干轮重复后，在 $[r'+1-p]$ 的对应循环位置上，会出现 $s[l_b,r_b]$ 的对应位 $>_f$ $s[l_\lambda,r_b]$ 的对应位的情况。于是总有 $s[l_\lambda,r_b]<_fs[l_b,r_b]$ 的情况，同样与 $b$ 是 $\text{Ly}$ 矛盾。

因此，$\lambda$ 必定为 $a_f$ 的右儿子。

## 本原平方串

### 定义和前置内容

- 本原平方串：若一个串的最小周期恰好为 $\frac{|s|}{2}$，则称之为本原平方串（$\text{primitive square}$)。

### 相关定理

- **引理 6.1**：$\text{Border}$ 一节中的定理三。

- **引理 6.2**：若非空串 $s,t$ 满足 $ss$ 是 $tt$ 的前缀，且 $2|s|>t$，则 $|t|-|s|$ 是 $s$ 的周期。

  容易证明，考虑 $s[i]=t[i]=tt[|t|+i]=ss[|t|+i]=s[|t|-|s|+i]$ 即可。

- **定理 6.1**：若非空串 $u,v,w$ 满足 $uu$ 是 $vv$ 的前缀，$vv$ 是 $ww$ 的前缀，且 $uu$ 是本原平方串，则 $|u|+|v|\le |w|$。

  证明：若 $|w|\ge 2|v|$ 则上式显然成立，考虑 $|w|<2|v|$。

  - 由引理 6.2，$v$ 有周期 $|w|-|v|$。

  - 假设 $|u|+|v|>|w|$，也就是 $|w|-|v|<|u|$，那么 $|w|-|v|$ 也是 $u$ 的周期。

    - 若 $2|u|\le v$，那么 $uu$ 是 $v$ 的前缀，于是 $uu$ 有小于 $|u|$ 的周期 $|w|-|v|$，与本原平方串矛盾。

    - 若 $2|u|>v$，由引理 6.2 得 $|v|-|u|$ 是 $|u|$ 的周期。

      我们知道 $v$ 有两不同周期 $|u|,|w|-|v|$，且这两周期，由于 $v$ 是本原平方串的长度超过一半的前缀，此时如若对其使用 $\text{WPL}$，会导出 $v$ 存在小于 $|u|$ 且整除 $|u|$，即 $v$ 的前缀 $u$ 具有整周期，$uu$ 不为本原平方串的矛盾，因此此时 $\text{WPL}$ 一定不可用，那么 $|u|+|w|-|v|>|v|$，也就是 $|u|+|w|>2|v|$。

      令 $vs_{1}=w,u=s_{1}s_{2},v=us_{3}=s_{1}s_{2}s_3$，那么 $w=s_1s_2s_3s_1$。

      - $|u|+|w|>2|v|\Rightarrow |s_1s_2|+|s_1s_2s_3s_1|>2|s_1s_2s_3|\Rightarrow |s_1|>|s_3|$。
      - $|u|+|v|>|w|\Rightarrow |s_1s_2|+|s_1s_2s_3|>|s_1s_2s_3s_1| \Rightarrow |s2|>0$。

      现在，$u[i(i\le |s_3|)]=s_1[i]=uu[|s_1s_2|+i]=vv[|s_1s_2|+i]$$=v[|s_1s_2|+i]=s_2s_3[|s_2|+i]$，又有 $s_1[i]=w[i]=ww[|w|+i]=ww[|s_1s_2s_3s_1|+i]=vv[|v|+|s_1|+i]=v[|s_1|+i]$$=s_2s_3[i]$，于是 $s_2s_3[i]=s_2s_3[|s_2|+i]$，即 $s_2s_3$ 有周期 $|s_2|$，又 $s_2s_3$ 是 $u$ 的前缀（对比 $vv,ww$ 在 $s_1s_2s_3s_1$ 后的部分），其亦有周期 $|v|-|u|=|s_3|$。于是 $s_2s_3$ 有周期 $r=\gcd(|s_2|,|s_3|)$，应用引理 6.1 就得到 $u$ 亦有此周期。注意到 $u$ 是 $v$ 的前缀，这表明 $u$ 有周期 $|w|-|v|=|s_1|$，那么就得到 $u$ 有周期 $r'=\gcd(|s_1|,|s_2|,|s_3|)$，这使得 $u$ 有整周期 $r'<|u|$，和本原平方串矛盾。

  - 原命题得证。

  推论 1：串 $s$ 中（位置不同的）本原平方串数不超过 $O(|s|\log|s|)$。这是容易证明的，只需考虑每个开头位置的本原平方串长呈斐波那契增长，即每个位置不超过 $O(\log |s|)$ 轮。

  推论 2：串 $s$ 中本质不同的本原平方串数不超过 $O(|s|)$。容易证明，若有某位置开头有三个本原平方串 $uu,vv,ww\ (|u|<|v|<|w|)$，那么 $|w|\le |u|+|v|>2|u|$，这里并不是 $uu$ 最后一次出现，不统计。那么每个位置最多只有两个最后一次出现的本原平方串，得证。

- **定理 6.2**：每个本原平方串一定属于恰好一个和它周期对应的 $\text{run}$。

  容易证明，每个本原平方串 $uu$ 自身在不可延伸的情况下就能够成为一个 $\text{run}$，因而 $\text{run}$ 一定存在，再由定理 3.1，显然不能有两个周期相同的 $\text{run}$ 均包含该本原平方串。

### 求本原平方串

由定理 6.2，只需对 $\text{run}$ 寻找其包含的本原平方串。对 $\text{run}\ {\bf r}=(l,r,p)$，$[l,r]$ 中任意长为 $2p$ 的区间的最小周期均为 $p$（若不然，考虑有更小周期 $p'$，此时 $p+p'<2p$，应用 $\text{WPL}$ 便得到其有一小于且整除 $p$ 的周期，即 $p$ 有更小整周期，这与 $\text{run}$ 矛盾）。因此所有长为 $2p$ 的子串都是本原平方串。

复杂度 $\sum r-l+2-2p$，同时每个本原平方串被我们考虑恰好一次，就得到该式的量级为 $O(n\log n)$。

### 例题

- [P6629 ZJOI2020 字符串](https://www.luogu.com.cn/problem/P6629)
- [Uoj#429. 【集训队作业2018】串串划分](https://uoj.ac/problem/429)