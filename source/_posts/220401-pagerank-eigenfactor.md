---
title: 两种图算法：PageRank & EigenFactor
comments: true
copyright: true
abbrlink: 1d43c0e5
date: 2022-04-01 12:02:45
updated: 2022-04-01 12:02:45
tags:
    - Python
    - PageRank
    - EigenFactor
    - Algorithm
categories: 数科知识
keywords:
    - PageRank
    - EigenFactor
    - Graph Algorithm
    - Scientometrics
description: 介绍两种相似的有向图结点重要性评价算法——PageRank 和 EigenFactor
top_img: https://download.mariozzj.cn/img/picgo/202204011146566.png
cover: https://download.mariozzj.cn/img/picgo/202204011147496.png
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
katex: true
aside: true
---

本文将介绍两种图数据的算法，PageRank 和 EigenFactor，都可以用于图数据的链接分析，是无监督算法。首先将介绍 PageRank，这将有助于理解 EigenFactor。

# PageRank

PageRank 算法于 1998 年由 Google 公司的 Larry Page 等提出，最初用于网页排序。

我们都知道网页上可以存在指向其他网页的超链接，以网页本身为结点，构建基于超链接指向的连边，我们就得到了一个图，现在就可以基于这个图数据进行分析。

## 基本理解

我们可以对结点（网页）进行重要性评价，除去网页质量本身，通过图的拓扑结构我们也能对结点进行评价，如**基于中心性的评价**认为，如果一个结点能被较多其他结点连接到（即入度较高），这个结点就处于较中心的位置，重要性较高。而这样一种方法的缺陷也很明显，就是它在计算影响力时，**仅仅考虑了连边的数量，而没有考虑连接到结点本身的其他结点的影响力**。这很容易理解，例如有两个网页各自都被三个其他网页链接到，但是前者是被三个不知名网站链接到，后者是被三个知名网站链接到，虽然我们都明白后者的影响力更高，但是在基于入度的评价中，二者影响力相同；或者再举一个社会网络中的例子，你和小明都被三个人认识，认识你的三个人是隔壁村的小学生，而认识小明的是三个国家的总统，虽然你们都仅被三个人认识，但是你们实际的影响力却是悬殊的。

而 PageRank 就解决了这样的问题，在考虑连入结点的边对结点影响力贡献的同时，也考虑了和结点相连结点的影响力对该结点的影响力的贡献。不难理解，PageRank 是一种递归算法，通过在图中不断迭代，实现计算结点的影响力。

## PageRank 基本定义
### 基本定义
给定一个包含 $n$ 个结点**强连通且非周期性的有向图**，在有向图上定义**随机游走模型**，即一阶马尔可夫链。其随机游走的特点是从一个结点到有有向边连出的所有结点的**转移概率相等**，转移矩阵为
$M$。这个马尔可夫链具有平稳分布 $R$ 
$$ MR=R $$
平稳分布 $R$ 称为这个有向图的 **PageRank**。$R$ 的各个分量称为各个结点的 PageRank 值。
$$ R= \left[\begin{matrix} PR(v_1) \\ PR(v_2) \\ \cdots \\ PR(v_n) \end{matrix}\right] $$
其中 $PR(v_i)（i=1,2,\cdots,n）$ ，表示结点的的 PageRank 值。

### 有向图、强连通图、周期性图

有向图中，要求边是有方向的，有起点和终点。网页、超链接组成的图就可以理解为**有向图**。

在有向图中，从一个结点出发到达另一个结点，所经过的边的一个序列称为一条路径，路径上边的个数称为路径的长度。如果一个有向图从其中任何一个结点出发可以到达其他任何一个结点，就称这个有向图是**强连通图**。

假设 $k$ 是一个大于 1 的自然数，如果从有向图的一个结点出发返回到这个结点的路径的长度都是 $k$ 的倍数，那么称这个结点为周期性结点。如果一个有向图不含有周期性结点，则称这个有向图为**非周期性图**，否则为周期性图。
![有向图、强连通图、非周期性图](https://download.mariozzj.cn/img/picgo/20220330210548.png)

### 随机游走模型、一阶马尔可夫链

在有向图上定义随机游走模型，此时结点表示状态，连边表示状态的转移，这样的随机游走形成一阶马尔可夫链，也就是说，**随机游走者在 $(t+1)$ 时刻访问某结点的概率仅仅与 $t$ 时刻的状态有关**，和之前的状态无关。

![图(a)](https://download.mariozzj.cn/img/picgo/20220331193211.png)

我们又定义，其随机游走的特点是从一个结点到有有向边连出的所有结点的**转移概率相等**，转移矩阵为
$M$。也就是说，**从一个结点移动到其所有指向的其他节点的概率相等**，以强连通且非周期性的有向图(a)为例，假设当前时刻结点 $A$，那么下一时刻转移到结点 $B、C、D$ 的概率相等，即 $m_{AB}=m_{AC}=m_{AD}=\dfrac{1}{3}$，并以 $0$ 概率转移到 $A$，这一概率仅依赖于当前状态。以此类推我们可以算出其他转移概率，并得到转移矩阵 $M$：

$$ M = [m_{ij}]_{n \times n} = \left[\begin{matrix} 0 &\dfrac{1}{2} &1 &0 \\ \dfrac{1}{3} &0 &0 &\dfrac{1}{2} \\ \dfrac{1}{3} &0 &0 &\dfrac{1}{2} \\ \dfrac{1}{3} &\dfrac{1}{2} &0 &0  \end{matrix}\right] $$

随机游走**在某个时刻 $t$ 访问各个结点的概率分布**就是马尔可夫链在时刻 $t$ 的状态分布，可以**用一个 $n$ 维列向量 $R_t$ 表示**，那么在 $(t+1)$ 时刻访问各个结点的概率分布 $R_{t+1}$ 满足
$$ R_{t+1} = MR_t $$


### 求解 PageRank
当图结构确定时，转移矩阵也是确定的。回到 PageRank 的基本定义，在初始状态下我们并未处于任何结点，此时访问各个结点的概率相等，即 
$$ R_0 = \left[\begin{matrix} \dfrac{1}{n} \\ \dfrac{1}{n} \\ \cdots \\ \dfrac{1}{n} \end{matrix}\right] $$
而之后状态下的概率分布则会与上一时刻的状态有关，分别为 $MR_0，M^2R_0，\cdots，M^tR_0，\cdots$

根据马尔可夫链平稳分布定理，强连通且非周期的有向图上定义的随机游走模型（马尔可夫链） ，在图上的随机游走当时间趋于无穷时状态分布**收敛于唯一的平稳分布** $R$，即
$$ \lim\limits_{t \to \infty} M^tR_0 = R $$
且满足
$$ MR=R=\left[\begin{matrix} PR(v_1) \\ PR(v_2) \\ \cdots \\ PR(v_n) \end{matrix}\right]  $$
不难得出
$$ PR(v_i) \ge 0，i=1,2,\cdots,n $$ $$ \sum\limits_{i=1}^n PR(v_i) = 1 $$ $$ PR(v_i) = \sum\limits_{v_j \in M(v_i)} \dfrac{PR(v_j)}{L(v_j)}，i=1,2,\cdots,n $$
这里 $M(v_i)$ 表示指向结点 $v_i$ 的结点集合， $L_(v_j)$ 表示结点 $v_j$ 连出有向边的个数。

例如，我们可以以图(a)为例求解 PageRank。

![图(a)](https://download.mariozzj.cn/img/picgo/20220331193211.png)

根据图(a)得出转移矩阵
$$ M = [m_{ij}]_{n \times n} = \left[\begin{matrix} 0 &\dfrac{1}{2} &1 &0 \\ \dfrac{1}{3} &0 &0 &\dfrac{1}{2} \\ \dfrac{1}{3} &0 &0 &\dfrac{1}{2} \\ \dfrac{1}{3} &\dfrac{1}{2} &0 &0  \end{matrix}\right] $$
初始分布向量
$$ R_0 = \left[\begin{matrix} \dfrac{1}{4} \\ \dfrac{1}{4} \\ \dfrac{1}{4} \\ \dfrac{1}{4} \end{matrix}\right] $$
以转移矩阵 $M$ 连乘初始向量 $R_0$ 得到向量序列
$$ \left[\begin{matrix} \dfrac{1}{4} \\ \dfrac{1}{4} \\ \dfrac{1}{4} \\ \dfrac{1}{4} \end{matrix}\right]，
\left[\begin{matrix} \dfrac{9}{24} \\ \dfrac{5}{24} \\ \dfrac{5}{24} \\ \dfrac{5}{24} \end{matrix}\right]，
\left[\begin{matrix} \dfrac{15}{48} \\ \dfrac{11}{48} \\ \dfrac{11}{48} \\ \dfrac{11}{48} \end{matrix}\right]，
\left[\begin{matrix} \dfrac{11}{32} \\ \dfrac{7}{32} \\ \dfrac{7}{32} \\ \dfrac{7}{32} \end{matrix}\right]，
\cdots，
\left[\begin{matrix} \dfrac{3}{9} \\ \dfrac{2}{9} \\ \dfrac{2}{9} \\ \dfrac{2}{9} \end{matrix}\right]$$
最终得到平稳分布 $R$，即有向图的 PageRank 值
$$ R = \left[\begin{matrix} \dfrac{3}{9} \\ \dfrac{2}{9} \\ \dfrac{2}{9} \\ \dfrac{2}{9} \end{matrix}\right] $$

同时给出 Python 程序实现：
```Python
import numpy as np

def Simple_PageRank(M, tol=0):
    """Calculates PageRank

    Args:
        M: an n*n numpy.matrix, the stochastic matrix of a strongly connected aperiodic graph.
    
    Returns:
        R: the PageRank Vector for the graph
    """
    n = M.shape[0]
    R0 = np.mat(np.ones(n)).T/n
    diff = np.mat(np.ones(n)).T
    i = 0
    while np.square(diff).sum() > tol:
        R = M * R0
        diff = R - R0
        R0 = R; i+=1
        print("\riter:{}".format(i),end="")
    
    return R
```

```python
M = np.mat([[ 0 ,1/2, 1 , 0 ],
            [1/3, 0 , 0 ,1/2],
            [1/3, 0 , 0 ,1/2],
            [1/3,1/2, 0 , 0 ]])
Simple_PageRank(M)
```

```
iter:53
matrix([[0.33333333],
        [0.22222222],
        [0.22222222],
        [0.22222222]])
```

## PageRank 一般定义

### 基本定义不适用的情况
一般的有向图**未必满足强连通且非周期性的条件**。比如，在互联网，大部分网页没有连接出去的超链接， 也就是说从这些网页无法跳转到其他网页。所以 PageRank 的基本定义不适用。
![图(a')](https://download.mariozzj.cn/img/picgo/20220331211226.png)
如图(a')，结点 $C$ 没有链出，如果写出转移矩阵并进行迭代计算，会发现最终极限为全 0 向量：
```Python
M = np.mat([[ 0 ,1/2, 0 , 0 ],
            [1/3, 0 , 0 ,1/2],
            [1/3, 0 , 0 ,1/2],
            [1/3,1/2, 0 , 0 ]])
Simple_PageRank(M, tol=0)
```

```
iter:1171
matrix([[2.15506015e-162],
        [3.14084309e-162],
        [3.14084309e-162],
        [3.14084309e-162]])
```

而这与实际不符，因此需要介绍 PageRank 的一般定义。

### 一般定义：导入平滑项
PageRank 一般定义的想法是在基本定义的基础上导入平滑项。
给定一个含有 $n$ 个结点的**任意有向图**，在有向图上定义一个一般的随机游走模型，即一阶马尔可夫链。一般的随机游走模型的转移矩阵由两部分的线性组合组成，一部分是**有向图的基本转移矩阵 $M$**， 表示从一个结点到其连出的所有结点的转移概率相等，另一部分是**完全随机的转移矩阵**，表示从任意一个结点到任意一个结点的转移概率都是 $\dfrac{1}{n}$，线性组合系数为阻尼因子 $d$ （$0 \le d \le 1$）。 这个一般随机游走的马尔可夫链存在平稳分布，记作 $R$。定义平稳分布向量 $R$ 为这个有向图的一般 PageRank。$R$ 由公式 
$$ R=dMR+\dfrac{1-d}{n}\boldsymbol{1} $$ 
决定，其中 $\boldsymbol{1}$ 是所有分量为 1 的 n 维向量。

定义之中，转移矩阵由两部分组成，有向图的基本转移矩阵包含了有向图的结构，随机游走者依据有向图的边进行随机游走，而另一部分，即完全随机的转移矩阵，表示随机游走者有一定概率（概率为 $1-d$，一般根据经验参数取值 $d=0.85$）等几率跳到图上任意一个结点（跳转到任意一个结点的概率都为 $\dfrac{1}{n}$）

在一般 PageRank 定义下，互联网浏览者能按照以下方法在网上随机游走：在任意一个网页上，浏览者**以概率 $d$ 决定按照超链接随机跳转**（以等概率从连接出去的超链接跳转到下一个网页），或者**以概率 $(1-d)$ 决定完全随机跳转**（以等概率 $\dfrac{1}{n}$ 跳转到任意一个网页）。**第二个机制就保证了从没有连接出去的超链接网页也可以跳转出**。这样可以保证平稳分布，即一般 PageRank 值（马尔科夫链的平稳分布）的存在，因而一般 PageRank 可以适用于任意有向图。

### 在一般有向图上求解 PageRank
![图(e)](https://download.mariozzj.cn/img/picgo/20220331230233.png)
以图(e)为例，取 $d=0.8$，求解 PageRank：
由图可以写出转移矩阵
$$ M = [m_{ij}]_{n \times n} = \left[\begin{matrix} 0 &\dfrac{1}{2} &0 &0 \\ \dfrac{1}{3} &0 &0 &\dfrac{1}{2} \\ \dfrac{1}{3} &0 &1 &\dfrac{1}{2} \\ \dfrac{1}{3} &\dfrac{1}{2} &0 &0  \end{matrix}\right] $$
计算出转移矩阵的两部分
$$ dM=\dfrac{4}{5} \times \left[\begin{matrix} 0 &\dfrac{1}{2} &0 &0 \\ \dfrac{1}{3} &0 &0 &\dfrac{1}{2} \\ \dfrac{1}{3} &0 &1 &\dfrac{1}{2} \\ \dfrac{1}{3} &\dfrac{1}{2} &0 &0  \end{matrix}\right] = \left[\begin{matrix} 0 &\dfrac{2}{5} &0 &0 \\ \dfrac{4}{15} &0 &0 &\dfrac{2}{5} \\ \dfrac{4}{15} &0 &\dfrac{4}{5} &\dfrac{2}{5} \\ \dfrac{4}{15} &\dfrac{2}{5} &0 &0  \end{matrix}\right] $$
$$ \dfrac{1-d}{n} \boldsymbol{1} = \left[\begin{matrix} \dfrac{1}{20} \\ \dfrac{1}{20} \\ \dfrac{1}{20} \\ \dfrac{1}{20} \end{matrix}\right] $$
迭代公式为
$$ R_{t+1} = \left[\begin{matrix} 0 &\dfrac{2}{5} &0 &0 \\ \dfrac{4}{15} &0 &0 &\dfrac{2}{5} \\ \dfrac{4}{15} &0 &\dfrac{4}{5} &\dfrac{2}{5} \\ \dfrac{4}{15} &\dfrac{2}{5} &0 &0  \end{matrix}\right] R_t + \left[\begin{matrix} \dfrac{1}{20} \\ \dfrac{1}{20} \\ \dfrac{1}{20} \\ \dfrac{1}{20} \end{matrix}\right] $$
我们知道，初始分布向量
$$ R_0 = \left[\begin{matrix} \dfrac{1}{4} \\ \dfrac{1}{4} \\ \dfrac{1}{4} \\ \dfrac{1}{4} \end{matrix}\right] $$
得到向量序列
$$ \left[\begin{matrix} \dfrac{1}{4} \\ \dfrac{1}{4} \\ \dfrac{1}{4} \\ \dfrac{1}{4} \end{matrix}\right]，
\left[\begin{matrix} \dfrac{9}{60} \\ \dfrac{13}{60} \\ \dfrac{25}{60} \\ \dfrac{13}{60} \end{matrix}\right]，
\left[\begin{matrix} \dfrac{41}{300} \\ \dfrac{53}{300} \\ \dfrac{153}{300} \\ \dfrac{53}{300} \end{matrix}\right]，
\left[\begin{matrix} \dfrac{543}{4500} \\ \dfrac{707}{4500} \\ \dfrac{2543}{4500} \\ \dfrac{707}{4500} \end{matrix}\right]，
\cdots，
\left[\begin{matrix} \dfrac{15}{148} \\ \dfrac{19}{148} \\ \dfrac{95}{148} \\ \dfrac{19}{148} \end{matrix}\right]$$
最终得到平稳分布 
$$ R = \left[\begin{matrix} \dfrac{15}{148} \\ \dfrac{19}{148} \\ \dfrac{95}{148} \\ \dfrac{19}{148} \end{matrix}\right] $$
同时给出 Python 程序实现：
```Python
import numpy as np

def PageRank(M, d=0.85, tol=0):
    """Calculates PageRank with smoothing

    Args:
        M: an n*n numpy.matrix, the stochastic matrix of a strongly connected aperiodic graph.
        d: the probability of walking on the edges
         
    Returns:
        R: the PageRank Vector for the graph
    """
    n = M.shape[0]
    R0 = np.mat(np.ones(n)).T/n
    Ones = np.mat(np.ones(n)).T
    diff = np.mat(np.ones(n)).T
    i = 0
    while np.square(diff).sum() > tol:
        R = d * M * R0 + ((1-d)/n) * Ones
        diff = R - R0
        R0 = R; i+=1
        print("\riter:{}".format(i),end="")
    
    return R
```

```Python
M = np.mat([[ 0 ,1/2, 0 , 0 ],
            [1/3, 0 , 0 ,1/2],
            [1/3, 0 , 1 ,1/2],
            [1/3,1/2, 0 , 0 ]])
PageRank(M, d=0.8)
```

```
iter:69
matrix([[0.10135135],
        [0.12837838],
        [0.64189189],
        [0.12837838]])
```

# EigenFactor

EigenFactor，一说译作“特征因子”[^1]，于 2006 年由  West Bergstorm 等提出，类似于 PageRank 在网页和超链接构成的有向图中计算网页影响力，EigenFactor 一般在期刊和引用关系构成的有向图中计算期刊影响力。理解 PageRank 原理有助于我们理解 EigenFactor 的计算原理。

## 基本理解
在研读文献时，文献本身可能会对参考的文献进行引用，我们作为读者就有可能阅读文献的参考文献。而参考文献一般与当前文献不位于同一本期刊上，我们就需要去阅读另一本期刊。这样的一种从期刊到期刊的跳转就类似于 PageRank 中网页到网页的跳转，根据引用关系构成的拓扑结构，以及 EigenFactor 加入的期刊载文量信息，就可以迭代计算出期刊的影响力。

## 计算方法

给定一个包含 $n$ 个期刊结点的有向图，在有向图上定义**随机游走模型**，即一阶马尔可夫链。该随机游走模型的转移矩阵由两部分的线性组合组成，一部分是**修正引文量加权转移矩阵 $\boldsymbol{H’}$**，表示从一个期刊结点到其连出的其他期刊结点的转移概率，另一部分是**载文量加权转移矩阵 $A$**，表示从任意一个期刊结点到任意一个期刊结点的转移概率（由载文量向量 $a$ 拓展而来），线性组合系数为阻尼因子 $\alpha$ （$0 \le \alpha \le 1$）。该随机游走的马尔可夫链存在平稳分布，记作 $\pi^\star$。$\pi^\star$ 由公式
$$ \pi^\star = \alpha \boldsymbol{H'}\pi^\star + (1-\alpha)a $$
决定。平稳分布向量 $\pi^\star$ 和引文量加权转移矩阵 $\boldsymbol{H}$ 可以计算得出期刊特征因子向量
$$ \boldsymbol{EF} = 100\dfrac{\boldsymbol{H}\pi^\star}{\sum\limits_{i=1}^n [\boldsymbol H \pi^\star]_i} $$
和各期刊的论文影响分值 
$$AI_i = 0.01 \dfrac{\boldsymbol{EF}_i}{a_i}$$

### 载文量加权转移矩阵
载文量加权转移矩阵 $\boldsymbol{A}$ 由引文量向量 $a$ 乘以 全 1 行向量得来。
$$ \boldsymbol{A} = ae^{T} $$
其中引文量向量 $a$ 各行为归一化处理后的过去五年各期刊的载文量（$\sum\limits_{i=1}^na_i =1$）。

所以，$\boldsymbol{A}$ 每列的每行都相等，为基于载文量的概率，这与 PageRank 的全 1 向量不同。

### 引文量加权转移矩阵

根据引文关系，我们可以先确定五年交叉引用矩阵 $\boldsymbol{Z}$，该矩阵是一个 $n$ 阶矩阵，第 $i$ 行第 $j$ 列元素 $z_{ij}$ 取值规则是：某年期刊 $j$ 发表的文章中，共计引用期刊 $i$ 在过去五年内发表的文章的次数。在构建该矩阵时，忽略所有的期刊自引，也就是说 $\boldsymbol{Z}$ 的对角值都为 0。随后，对每一列做归一化处理，此时每列就可以代表阅读期刊时跳转到其他期刊的概率，得到**引文量加权转移矩阵** $\boldsymbol{H}$：
$$ \boldsymbol{H}_{ij} = \dfrac{\boldsymbol{Z}_{ij}}{\sum\limits_{k=1}^n\boldsymbol{Z}_{kj}} $$
因为计算时忽略了所有的自引，就有可能存在窗口期内某期刊并未引用集合内期刊的情况，即该结点为悬挂结点，矩阵某一列都为 0，类似于之前图(a')中结点 C 存在向外连边的情况。为了修正该问题，我们用载文量向量 $a$ 替换全 0 向量，得到**修正引文量加权转移矩阵 $\boldsymbol{H’}$**。其中，引文量向量 $a$ 的各行为归一化处理后的过去五年各期刊的载文量（$\sum\limits_{i=1}^na_i =1$）。

### 理解 EigenFactor
定义之中，转移矩阵由两部分组成，修正引文量加权转移矩阵包含了引文结构和引文量，读者**有一定概率（概率为 $\alpha$）依据有向图的边及权重进行游走**（边权重由引文量决定，如无引文量，由载文量决定），而另一部分，即载文量加权转移矩阵，表示读者**有一定概率（概率为 $1-\alpha$）跳到图上任意一个结点**（跳转到任意一个结点的概率和结点过去五年载文量成正比）。载文量加权矩阵保证了从没有引用的期刊也可以跳转出。

相比于 PageRank 在计算基本转移矩阵和随机转移矩阵时，对所有的边和结点赋予相同的权重，**EigenFactor 会考虑期刊的载文量和引文量，并赋予结点和边不同的概率权重**，能够在一定程度上更合理评价期刊的影响力。

## 计算案例
![图(e')](https://download.mariozzj.cn/img/picgo/202204010155388.png)
以图(e)为例，边上的数字为当年引文量，圈内的数字为五年载文量，取 $\alpha=0.8$，求解 EigenFactor 和 Artical Influence Score：
由图可以写出载文量向量、引文量加权向量、修正引文量加权向量
$$ a=\left[\begin{matrix}\dfrac{4}{20} \\ \dfrac{8}{20} \\ \dfrac{2}{20} \\ \dfrac{6}{20}\end{matrix}\right]，\boldsymbol{H}= \left[\begin{matrix} 0 &\dfrac{5}{6} &0 &0 \\ \dfrac{2}{6} &0 &0 &\dfrac{2}{6} \\ \dfrac{3}{6} &0 &0 &\dfrac{4}{6} \\ \dfrac{1}{6} &\dfrac{1}{6} &0 &0  \end{matrix}\right]，\boldsymbol{H'}= \left[\begin{matrix} 0 &\dfrac{5}{6} &\dfrac{4}{20} &0 \\ \dfrac{2}{6} &0 &\dfrac{8}{20} &\dfrac{2}{6} \\ \dfrac{3}{6} &0 &\dfrac{2}{20} &\dfrac{4}{6} \\ \dfrac{1}{6} &\dfrac{1}{6} &\dfrac{6}{20} &0  \end{matrix}\right] $$
迭代公式为
$$ \pi_{t+1} = \left[\begin{matrix} 0 &\dfrac{2}{3} &\dfrac{4}{25} &0 \\ \dfrac{4}{15} &0 &\dfrac{8}{25} &\dfrac{4}{15} \\ \dfrac{2}{5} &0 &\dfrac{2}{25} &\dfrac{8}{15} \\ \dfrac{2}{15} &\dfrac{2}{15} &\dfrac{6}{25} &0  \end{matrix}\right] \pi_t + \left[\begin{matrix} \dfrac{1}{25} \\ \dfrac{2}{25} \\ \dfrac{1}{50} \\ \dfrac{3}{50} \end{matrix}\right] $$
后续计算过程略。

同时给出 Python 程序实现：
```Python
import numpy as np

def EigenFactor(H, article_vec,d=0.85, tol=0):
    """Calculates EigenFactor

    Args:
        H: the stochastic matric, an n*n numpy.matrix
        article_vec: the normalized article vector, list
        d: the probability of walking on the edges
         
    Returns:
        EF: the EigenFactor Score of Journal list
    """
    n = H.shape[0]
    Hs = ((1 - H.sum(axis=0).T) * article_vec).T + H
    pi0 = np.mat(np.ones(n)).T/n
    a = np.mat(article_vec).T
    diff = np.mat(np.ones(n)).T
    i = 0
    while np.square(diff).sum() > tol:
        pi = d * Hs * pi0 + ((1-d)) * a
        diff = pi - pi0
        pi0 = pi; i+=1
        print("\riter:{}".format(i),end="")
    
    EF = (H*pi/(H*pi).sum())*100
    return EF

def ArticleInfluenceScore(EF, article_vec):
    """Calculates Article Influence Score

    Args:
        EF: EigenFactor Vector, n*1 numpy.matrix
        article_vec: the normalized article vector, list
         
    Returns:
        AIS: the Article Influence Score Score of Journal list
    """
    AIS = 0.01 * np.multiply(EF,1/np.mat(article_vec).T)
    return AIS
```

```Python
H =   np.mat([[ 0 ,5/6, 0  , 0  ],
              [1/3, 0 , 0  ,1/3 ],
              [1/2, 0 , 0  ,2/3 ],
              [1/6,1/6, 0  , 0  ]])
article_vec = [1/5,2/5,1/10,3/10]
EF = EigenFactor(H, article_vec,d=0.8)
display(EF)
AIS = ArticleInfluenceScore(EF, article_vec)
display(AIS)
```

```
iter:38
matrix([[31.65677392],
        [20.67062376],
        [35.33270853],
        [12.33989378]]) # EigenFactor Score
matrix([[1.5828387 ],
        [0.51676559],
        [3.53327085],
        [0.41132979]])  # ArticleInfluence Score
```


[^1]: https://blog.sciencenet.cn/blog-38899-240133.html