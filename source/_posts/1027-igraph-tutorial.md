---
title: igraph 上手教程——使用 Python 开展社会网络分析和可视化
comments: true
copyright: true
abbrlink: 52d95125
date: 2021-10-27 15:37:01
updated: 2021-10-27 15:37:01
tags: 
  - SNA
  - igraph
categories:
  - 工具干货
keywords: igraph
description: 本文就 python-igraph 的使用手册原文进行翻译，探索式学习 igraph 包，使用 Python 即可完成社会网络分析和可视化。
top_img: https://download.mariozzj.cn/img/picgo/202110271543237.svg
cover: https://download.mariozzj.cn/img/picgo/202110271543237.svg
copyright_author: 
copyright_author_href:
copyright_url:
copyright_info:
katex: false
aside: true
---

> 原文地址： https://blog.mariozzj.cn/posts/52d95125/
>
> 国内镜像： https://cnblog.mariozzj.cn/posts/52d95125/
>
> 原官方教程（英文）：https://igraph.org/python/tutorial/0.9.7/tutorial.html

-----

# igraph

igraph 是一个开源免费网络分析工具集合，在效率和便捷性上表现较好。之前所学和网络上的教程大多基于 R 语言，而实际上 igraph 为 R、Python、Mathematica 和 C/C++ 均有支持，可以前往 [igraph 官网]([igraph – Network analysis software](https://igraph.org/)) 了解。

之前使用过 R 包的 igraph，但是我的 R 语言实在学艺不精，之后也没怎么用到过。在我的数据分析场景下，Python 可能相对于熟悉一些，且可以联动实现数据处理和分析，感觉非常方便。

igraph 的 Python 包的使用文档正在完善中，网络上几经查找并没有完全完整的中文版本手册，所以我本着学习这个程序包的目的顺便将其最新（0.9.7）的英文版本操作手册的概览教程翻译下来，主要来源于 [Tutorial (igraph.org)](https://igraph.org/python/tutorial/0.9.7/tutorial.html) ，如有翻译不地道的地方也请各位指正！整体阅读下来感觉手册写的还是比较简明、全面的。



# 安装 igraph

使用 `pip` 安装 `igraph`

```shell
pip install python-igraph
```

可以继续安装 `pycairo` 用于支持网络的可视化

```shell
pip install pycairo
```



启动 Python 运行如下代码，检查是否安装成功：

```python
import igraph as ig
petersen = ig.Graph.Famous("petersen")
ig.plot(petersen)
```

![image-20211026112756046](https://download.mariozzj.cn/img/picgo/202110261128175.png)

如安装无误，展示的是著名的 Petersen 图。



# igraph 使用教程

## 从零搭建图

引入 `igraph` 包后，可以自行创建一个图：

```python
>>> g = ig.Graph()
>>> g
<igraph.Graph at 0x294fbc389a0>
>>> print(g)
IGRAPH U--- 0 0 --
```

在这里，`g` 是一个 Graph 实例。打印出来的结果中的两个数字是节点数和连边数。如果我们打印上一节的 Petersen 图，会这么返回：

```python
>>> print(petersen)
IGRAPH U--- 10 15 --
+ edges:
 0 --  1  4  5    3 --  2  4  8    6 --  1  8  9    9 --  4  6  7
 1 --  0  2  6    4 --  0  3  9    7 --  2  5  9
 2 --  1  3  7    5 --  0  7  8    8 --  3  5  6
```

这里还给出了具体的连边信息，Petersen 图中有 10 个节点和 15 条连边，每个节点都与三个节点相连。

我们可以调用 `add_vertices(num)` 方法为我们创建的图增加 num 个节点，使用 `add_edges([pairs])` 方法增加连边。`pairs` 为两个节点编号组成的元组，可以输入一至多个，如

```python
>>> g.add_vertices(6)
>>> g.add_edges([(0, 1), (1, 2), (2, 0), (2, 3), (3, 4), (4, 5), (5, 3)])
>>> print(g)
IGRAPH U--- 6 7 --
+ edges:
0--1 1--2 0--2 2--3 3--4 4--5 3--5
```

目前的这个网络中有 6 个节点和 7 条连边。在构建的网络中，节点 ID 是由 0 开始的连续整数，添加连边时的编号对就对应着这些 ID。而连边其实也是有 ID 的，也是由 0 开始的连续整数。节点和连边 ID 的连续性不会改变，所以如果我们删除节点和连边，可能会造成部分节点和连边 ID 的变化。

如果两个节点之间存在连边，我们可以使用 `get_eid()`方法获取他们的连边的编号：

```python
>>> g.get_eid(2,3)
3
```

可以使用 `delete_vertices()` 方法删除图中的节点，可以使用 `delete_edges()` 删除图中的连边，括号内的参数为一至多个节点/连边的 ID。

```python
>>> g.delete_edges(3)
>>> ig.summary(g)
IGRAPH U--- 6 6 -- 
```

`summary()` 方法可以只展示图的基本信息，相比于 `print()` 可以避免大图中大量连边占用输出。



## 生成图

`igraph` 包中有多种图生成器，大体上可以分为两种：确定性（Deterministic）和随机性（Stochastic）图生成器。如果调用时输入相同的参数，确定性图生成器创建出来的图是完全相同的，而随机性图生成器则会生成不同的图。

**确定性图生成器**包括创建树图、正则格（Regular Lattice）、环（Ring）、扩展弦环（Extended Chordal Ring）等著名图。如`Graph.Tree()` 方法可以创建树图：

```python
>>> g = ig.Graph.Tree(127, 2)
>>> ig.summary(g)
IGRAPH U--- 127 126 --
>>> g2 = ig.Graph.Tree(127, 2)
>>> g2.get_edgelist() == g.get_edgelist()
True
>>> g2.get_edgelist()[0:2]
[(0, 1), (0, 2)]
```

创建了树图 `g`，共计 127 个节点，除叶子节点外每个节点都有 2 个孩子节点，所以共有 126 条连边。后面使用相同参数创建 `g2`，可发现 `g` 和 `g2` 连边完全相同。这里用到的 `get_edgelist()` 方法会返回连边元组组成的列表。

随机性图生成器可以创建 ER 随机图（Erdős-Rényi Random Network）、Barabási-Albert 网络模型、随机几何图形（Geometric Random graphs）等。如`Graph.GRG()` 方法可以创建随机几何图：

```python
>>> g = ig.Graph.GRG(100, 0.2)
>>> ig.summary(g)
IGRAPH U---- 100 516 --
+ attr: x (v), y (v)
```

在创建随机几何图 `GRG(n,d)` 的两个参数中，`n` 指定了节点数量，`d` 则为连边距离阈值，所有点在单元空间中随机分布，距离小于 `d` 的则建立连边，这样一种建图规则会导致即使给出相同的参数，创建出来的图也是不一样的：

```python
>>> g2 = ig.Graph.GRG(100, 0.2)
>>> g.get_edgelist() == g2.get_edgelist()
False
>>> g.isomorphic(g2)
False
```

同样参数创建的 `g2` 与 `g` 的边-节点 ID 关系不同。使用`isomorphic()` 方法能够判定两个图是否同构（isomorphic），很显然从结果看，这次实验中二者也不是同构的。

在 GRG 的 summary 中，我们看到了图的参数（attr）被打印出来，接下来将继续介绍。



## 设置与获取属性

`igraph` 使用 ID 区分不同的节点和连边，之前提到过这些 ID 是由 0 开始的连续的整数，当我们删除节点和连边时这种连续性不会被破坏，所以执行删除操作时可能涉及到其他节点/连边 ID 的变化。

假设我们使用 `igraph` 做社会网络分析，节点代表人，连边代表人之间的社会联系，一种可能的建立节点 ID 和人物名对应关系的方法是创建一个 Python 列表将 ID 和姓名对应起来，这种方式的缺陷是这个列表需要随网络的调整而同步调整，这样或许有些麻烦。

 `igraph` 支持为节点、连边、图对象添加属性。首先，我们创建一个简单的社会网络：

```python
>>> g = ig.Graph([(0,1), (0,2), (2,3), (3,4), (4,2), (2,5), (5,0), (6,3), (5,6)])
```

假设我们要为节点（人）添加姓名、年龄、性别属性，同时对于连边（社会联系）添加是否为正式联系的标注属性。对于 `igraph` 包中的每个 `Graph` 对象，可以调用其成员变量 `vs` 和 `es` ，即节点序列 `VertexSeq`（Vertice Sequence）和连边序列 `EdgeSeq`（Edge Sequence），可以将其当作 Python 中的字典（dict）对象使用：

```python
>>> g.vs
<igraph.VertexSeq object at 0x1b23b90>
>>> g.vs["name"] = ["Alice", "Bob", "Claire", "Dennis", "Esther", "Frank", "George"]
>>> g.vs["age"] = [25, 31, 18, 47, 22, 23, 50]
>>> g.vs["gender"] = ["f", "m", "f", "m", "f", "m", "m"]
>>> g.es["is_formal"] = [False, False, True, True, True, False, True, False, False]
```

当你将 `vs`/`es` 当作字典使用时，就是将属性值分配给图中的所有节点/连边。当然我们也可以将 `vs`/`es` 当作 Python 的列表（list）使用，给出索引值获取特定节点/连边，对于获取到的节点/连边，也可以（像字典一样使用）修改它的属性：

```python
>>> g.es[0]
igraph.Edge(<igraph.Graph object at 0x4c87a0>,0,{'is_formal': False})
>>> g.es[0].attributes()
{'is_formal': False}
>>> g.es[0]["is_formal"] = True
>>> g.es[0]
igraph.Edge(<igraph.Graph object at 0x4c87a0>,0,{'is_formal': True})
```

从示例可以看出，`es` 对象是边序列，其中的每一个元素都是 `Edge` 对象，打印出来的值是该边所属的图、边 ID、和属性组成的字典。`Edge` 对象有一些有用的属性，如：`source`，展示边的源节点；`target`，展示边的目标节点；`index`，获得边 ID；`tuple`，获得源节点和目标节点组成的元组；以及这里用到的 `attributes()` ，获得有边的所有属性及值组成的字典。

`Graph.es` 对象一般是一个图中所有边的集合，索引值 i 一般都会返回 ID 为 i 的边，对 `Graph.vs` 也同理。但是需要注意，并不是所有的边序列对象都是某个图的所有边的集合，对节点序列对象也是如此，在后文会介绍特例。

之前提到过，不仅对于节点和边，图对象也可以设置参数：

```python
>>> g["date"] = "2009-01-10"
>>> print(g["date"])
2009-01-10
```

最后，如果需要删除属性，可以使用 Python 关键字 `del`，像删除字典中键值对一样删除属性：

```python
>>> g.vs[3]["foo"] = "bar"
>>> g.vs["foo"]
[None, None, None, 'bar', None, None, None]
>>> del g.vs["foo"]
>>> g.vs["foo"]
Traceback (most recent call last):
  File "<stdin>", line 25, in <module>
KeyError: 'Attribute does not exist'
```



## 图的结构性指标计算

`igraph` 提供许多计算图的结构性指标的方法，本节仅选取部分进行介绍，将继续沿用上一节我们建立的图。



大多数人能想到的最简单的指标就是节点的度数。节点的度等于与该节点建立连边的节点数，在有向图中度还分入度（指向该节点的连边数）和出度（由该节点发出的连边数），这些指标 `igraph` 都可以计算，例如要计算度，可以使用 `degree()` 方法。

```python
>>> g.degree()
[3, 1, 4, 3, 2, 3, 2]
```

如果要在有向图中计算入度和出度，只需调用时添加 `mode` 参数，如 `g.degree(mode="in")`，`g.degree(mode="out")`。也可以添加一个或多个节点 ID，以获取特定节点的度。

```python
>>> g.degree(6)
2
>>> g.degree([2,3,4])
[4, 3, 2]
```

这样一种填入一个或多个节点 ID 的方式适用于大多数 `igraph` 中结构性指标的计算方法。除了填入 ID，当然还可以填入 `VertexSeq` 和 `EdgeSeq` 实例，下一篇文章会予以介绍。有一些指标相比于使用整个图，使用部分节点和连边计算的结果是没有意义的，这种情况下可能就不支持这样的输入，但是可以对结果进行索引来获取部分结果（如 `Graph.evcent()` ）



除了度数，`igraph` 还可以计算中心性指标，包括节点和边的中介中心性（方法：`Graph.betweenness()`、`Graph.edge_betweenness()` ）或者 Google 的 PageRank （`Graph.pagerank()`）等。这里介绍边中介中心性的计算。边中介中心性是网络中所有最短路径中经过该边的路径的数目。

```python
>>> g.edge_betweenness()
[6.0, 6.0, 4.0, 2.0, 4.0, 3.0, 4.0, 3.0. 4.0]
```

计算得到所有边的中介中心性，然后我们可以综合运用之前的方法，找到那些中介中心性较高的边：

```python
>>> ebs = g.edge_betweenness()
>>> max_eb = max(ebs)
>>> [g.es[idx].tuple for idx, eb in enumerate(ebs) if eb == max_eb]
[(0, 1), (0, 2)]
```

大多数结构性指标计算可以就部分节点/连边得出，即可以用 `VertexSeq`，`EdgeSeq`，`Vertex`，`Edge` 对象计算：

```python
>>> g.vs.degree()
[3, 1, 4, 3, 2, 3, 2]
>>> g.es.edge_betweenness()
[6.0, 6.0, 4.0, 2.0, 4.0, 3.0, 4.0, 3.0. 4.0]
>>> g.vs[2].degree()
4
```



## 使用参数查找特定节点和连边

### 选择节点和连边

假设在某个社会网络中，你需要找出具有最大点度或中介中心度的人，除了使用之前给出的方式结合一些你可能掌握的 Python 方法，`igraph` 提供了更简单的方式：

```Python
>>> g.vs.select(_degree=g.maxdegree())["name"]
["Alice", "Bob"]
```

这种表达乍一看有些奇怪，我们可以一步步拆解来理解。`select()` 是 `VertexSeq` 的一个方法，它的唯一目标是通过节点的指标来过滤节点集合，通过添加位置参数或关键字参数可以实现。一般来说位置参数没有显式定义的名称（如`_degree`），在关键字参数前读取。

* 如果首个位置参数为 `None`，会返回一个空序列：

  ```python
  >>> seq = g.vs.select(None)
  >>> len(seq)
  0
  ```

* 如果首个位置参数为可调用对象（如函数、绑定方法等），这个对象会逐一被节点序列 `vs` 中的节点调用。若函数返回结果为 `True`，当前节点会被包含在结果中，否则被排除在结果之外。

  ```python
  >>> graph = Graph.Full(10)
  >>> only_odd_vertices = graph.vs.select(lambda vertex: vertex.index % 2 == 1)
  >>> len(only_odd_vertices)
  5
  ```

* 如果首个位置参数是可迭代的对象（如列表、生成器等），该对象的返回值必须为整数，且这些整数会作为当前节点序列（并不一定是整个图的所有节点）的索引值使用，仅有处于这些索引值位置的节点会进入结果集。返回值中的浮点数、字符串和无效的节点 ID 值会被忽略：

  ```python
  >>> seq = graph.vs.select([2, 3, 7])
  >>> len(seq)
  3
  >>> [v.index for v in seq]
  [2, 3, 7]
  >>> seq = seq.select([0, 2])         # filtering an existing vertex set
  >>> [v.index for v in seq]
  [2, 7]
  >>> seq = graph.vs.select([2, 3, 7, "foo", 3.5])
  >>> len(seq)
  3
  ```

* 如果首个位置参数为整数，所有余下的位置参数都应是整数，这些参数被当作索引值使用，处于这些索引值位置的节点会进入结果集。

  ```python
  >>> seq = graph.vs.select(2,3,7)
  >>> len(seq)
  3
  ```

除了位置参数之外，`select()` 方法支持一系列关键字参数（具有显式定义的参数名）：

| 关键字参数   | 含义                                              |
| ------------ | ------------------------------------------------- |
| `name_eq`    | 参数/属性必须等于给出的参数值                     |
| `name_ne`    | 参数/属性必须不等于给出的参数值                   |
| `name_lt`    | 参数/属性必须小于给出的参数值                     |
| `name_le`    | 参数/属性必须小于等于给出的参数值                 |
| `name_gt`    | 参数/属性必须大于给出的参数值                     |
| `name_ge`    | 参数/属性必须大于等于给出的参数值                 |
| `name_in`    | 参数/属性必须在给出的参数值之中，参数值须为序列   |
| `name_notin` | 参数/属性必须不在给出的参数值之中，参数值须为序列 |

例如，下面这一行代码能返回 `vs` 中 `age` 小于 30 的记录：

```python
>>> g.vs.select(age_lt=30)
```

当然，`select()` 也可以以简略形式调用，下面这行与上面那行等价：

```python
>>> g.vs(age_lt=30)
```

> 不能写作 `g.vs(age < 30)` ，因为参数列表中只有 `=` 是被允许的。

有时可能会出现已定义属性与结构指标值名相同的情况，如点度（degree）和你定义的 `degree` 属性，为解决歧义问题，如需将结构指标名用于筛选节点，必须在指标名前加 `_`。

```python
>>> g.vs(_degree_gt=2)
```

除了度以外，还有一些特殊的结构指标名用于筛选节点：

* 使用 `_source` 或 `_from` 用于边筛选，筛选那些来源节点为特定节点的边，如筛选来源为 ID 为 2 的边：

  ```python
  >>> g.es.select(_source=2)
  ```

* 使用 `_target` 或 `_to` 用于边筛选，筛选那些目标节点为特定节点的边，用法同上。

* `_within` 参数传入 `VertexSeq` 或索引值序列（列表或集合），选取包含在给出的节点（索引）序列中的节点。

  ```python
  >>> g.es.select(_within=[2,3,4])
  >>> g.es.select(_within=g.vs[2:5])
  ```

* `_between` 参数传入两个由 `VertexSeq` 或包含索引值的列表，或 `Vertex` 对象组成的元组，选取那些从一组的节点发出，指向另一组的节点的边集合。

  ```python
  >>> men = g.vs.select(gender="m")
  >>> women = g.vs.select(gender="f")
  >>> g.es.select(_between=(men,women))
  ```

  

### 使用某些属性寻找单一节点或连边

有时我们会用许多属性限定在图中寻找某一个特定的节点或连边。如果这样的条件下有多个结果返回，我们可能不关心返回哪一个结果，或者我们预先知道这样的条件下只会返回一个结果，比如我们用 `name` 属性查找某个姓名为指定值的节点。

`VertexSeq` 和 `EdgeSeq` 对象提供 `find()` 方法供以上场景使用。与 `select()` 不同的是，`find()` 仅返回结果集的第一个结果（如果没有结果则报错）。比如：

```python
claire = g.vs.find(name="Claire")
>>> type(claire)
igraph.Vertex
>>> claire.index
2
```

如果没有这样的结果则会报错：

```python
>>> g.vs.find(name="Joe")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: no such vertex
```



### 使用 `name` 找节点

相比于 ID，节点的名字似乎更容易被记忆。`igraph` 对 `name` 属性做了特殊处理，将其设置为“索引”，这样用 `name` 也可以快速地查找到节点，而且为了让使用更简单，`name` 可以作为“索引”在所有 ID 可以出现的地方出现，包含列表等情况。

比如计算某一节点的度：

```python
>>> g.degree("Dennis")
3
>>> g.vs.find("Dennis").degree()
3
```

`igraph` 在后台维持着 ID 和 `name` 的对应关系，当图改变时这个对应关系也会对应调整。和 ID 不同，`igraph` 允许重复的 `name` ，但是当你在使用 `name` 寻找某个 `name` 与其他节点重复的节点时，`igraph` 只会返回他们之中的一个，如果要寻找其他的节点可能只能用其他办法了。



## 图与邻接矩阵

邻接矩阵是表示图的一种方式，在邻接矩阵中，行列均为节点编号，对应的值表明节点之间是否有连边。如矩阵中的第 i 行第 j 列的值表明节点 i 和节点 j 是否有连边。要获取图的邻接矩阵，可以使用 `get_adjacency()` 方法。

```python
>>> g.get_adjacency()
Matrix([[0, 1, 1, 0, 0, 1, 0], [1, 0, 0, 0, 0, 0, 0], [1, 0, 0, 1, 1, 1, 0], [0, 0, 1, 0, 1, 0, 1], [0, 0, 1, 1, 0, 0, 0], [1, 0, 1, 0, 0, 0, 1], [0, 0, 0, 1, 0, 1, 0]])
>>> print(g.get_adjacency())
[[0, 1, 1, 0, 0, 1, 0]
 [1, 0, 0, 0, 0, 0, 0]
 [1, 0, 0, 1, 1, 1, 0]
 [0, 0, 1, 0, 1, 0, 1]
 [0, 0, 1, 1, 0, 0, 0]
 [1, 0, 1, 0, 0, 0, 1]
 [0, 0, 0, 1, 0, 1, 0]]
```

从输出可以看出，ID 为 2 的节点（ [1, 0, 0, 1, 1, 1, 0] ）和 ID 为 0、3、4、5 的节点相连，而和 ID 为 1、6 的节点之间没有连边。



## 布局与绘图

之前的介绍中，图是一个相对抽象的数学概念，我们没有将其映射至 2D 或 3D 空间。如果我们想将图可视化，我们首先需要将节点对应到二维或三维空间内。图绘制（Graph Drawing）——图理论的一个分支，尝试运用多种图布局算法解决这个映射问题。`igraph` 引入了部分布局算法，并且可以结合 Cairo 包（本文开始时安装过）将其绘制为 PDF、PNG、SVG 等形式。



### 布局算法

布局方法是 `Graph` 对象的成员方法，一般都以 `layout_` 起头：

| 方法全名                           | 简称                                   | 算法描述                                              |
| ---------------------------------- | -------------------------------------- | ----------------------------------------------------- |
| `layout_circle`                    | `circle` , `circular`                  | 将节点放在一个圆环上                                  |
| `layout_drl`                       | `drl`                                  | 常用于大图，分散递归布局                              |
| `layout_fruchterman_reingold`      | `fr`                                   | Fruchterman-Reingold 力导向布局                       |
| `layout_fruchterman_reingold_3d`   | `fr3d` , `fr_3d`                       | Fruchterman-Reingold 力导向布局（3D版）               |
| `layout_kamada_kawai`              | `kk`                                   | Kamda-Kawai 力导向布局                                |
| `layout_kamada_kawai_3d`           | `kk3d` , `kk_3d`                       | Kamda-Kawai 力导向布局（3D版）                        |
| `layout_lgl`                       | `large` , `lgl` , `large_graph`        | 常用于大图，大图布局                                  |
| `layout_random`                    | `random`                               | 将节点完全随机放置                                    |
| `layout_random_3d`                 | `random_3d`                            | 将节点完全随机放置在三维空间                          |
| `layout_reingold_tilford`          | `rt` , `tree`                          | Reingold-Tilford 树状布局，对类树状图有用             |
| `layout_reingold_tilford_circular` | `rt_citcular` , `tree`                 | 极坐标转换后Reingold-Tilford 树状布局，对类树状图有用 |
| `layout_sphere`                    | `sphere` , `spherical` , `circular_3d` | 将节点放在球面                                        |

布局方法可以直接被调用，也可以使用通用的 `layout()` 方法：

```python
>>> layout = g.layout_kamada_kawai()
>>> layout = g.layout("kamada_kawai")
```

`layout()` 方法的首个参数必须为布局算法的简称（如上表），余下的位置参数和关键字参数都为布局算法使用，例如下面两行等价语句：

```python
>>> layout = g.layout_reingold_tilford(root=[2])
>>> layout = g.layout("rt", [2])
```

布局方法返回 `Layout` 对象，这个对象像一个列表组成的列表，每个列表对应着节点在 2D 或 3D 空间中的位置坐标。`Layout` 对象包含一些有用的方法，可以批量转换、缩放、旋转坐标。当然，其主要的作用还是将数据传递给 `plot()` 方法，获得 2D 图绘制。



### 使用布局绘图

我们可以用之前的社会网络绘图，采用 Kamada-Kawai 布局：

```python
>>> layout = g.layout("kk")
>>> ig.plot(g, layout=layout)
```

这将绘制出和下图近似的网络图（实际展现出的样子会随机变化）：

![Kamada-Kawai 布局社会网络图](https://igraph.org/python/tutorial/0.9.7/_images/tutorial_social_network_1.png)

如果想使用 `matplotlib` 作为绘图引擎，需要使用 `target` 参数创建轴：

```python
>>> import matplotlib.pyplot as plt
>>> fig, ax = plt.subplots()
>>> ig.plot(g, layout=layout, target=ax)
```



目前还不是很美观，我们可以将人名标注在节点附近，将节点按照性别上色。图中的节点标签默认来源于 `label` 参数，节点的颜色由 `color` 标签决定，所以可以调整后重新绘制：

```python
>>> g.vs["label"] = g.vs["name"]
>>> color_dict = {"m": "blue", "f": "pink"}
>>> g.vs["color"] = [color_dict[gender] for gender in g.vs["gender"]]
>>> ig.plot(g, layout=layout, bbox=(300, 300), margin=20)
>>> # ig.plot(g, layout=layout, bbox=(300, 300), margin=20, target=ax) # matplotlib 版本
```

![The visual representation of our social network - with names and genders](https://igraph.org/python/tutorial/0.9.7/_images/tutorial_social_network_2.png)

我们重用了之前的布局对象，但是我们也注明了我们需要一个较小的画布（300 × 300 像素）和 20 像素的边距使得标签能够完全展示。

除了在节点和连边的属性中设置，你当然也可以在 `plot()` 时传入参数达到相同效果：

```python
color_dict = {"m": "blue", "f": "pink"}
>>> plot(g, layout=layout, vertex_color=[color_dict[gender] for gender in g.vs["gender"]])
```

如果我们想将节点属性数据和绘图完全分离开来，这是一种推荐的方式。当然，我们也可以完全用一个字典来存储上述布局参数，最后使用 `**` 操作符将样式参数一并传入：

```python
>>> visual_style = {}
>>> visual_style["vertex_size"] = 20
>>> visual_style["vertex_color"] = [color_dict[gender] for gender in g.vs["gender"]]
>>> visual_style["vertex_label"] = g.vs["name"]
>>> visual_style["edge_width"] = [1 + 2 * int(is_formal) for is_formal in g.es["is_formal"]]
>>> visual_style["layout"] = layout
>>> visual_style["bbox"] = (300, 300)
>>> visual_style["margin"] = 20
>>> plot(g, **visual_style)
```

![The visual representation of our social network - with names, genders and formal ties](https://igraph.org/python/tutorial/0.9.7/_images/tutorial_social_network_3.png)



### 节点和边的绘图布局参数

上一节看到了很多节点和边的布局参数，传入这些参数能够覆盖 `igraph` 绘图的默认设置参数。实际上，有大量的参数可以用于调整布局：

#### 节点绘图布局参数

| 参数名        | 关键参数             | 用处                                                         |
| ------------- | -------------------- | ------------------------------------------------------------ |
| `color`       | `vertex_color`       | 节点的颜色                                                   |
| `font`        | `vertex_font`        | 节点字体                                                     |
| `label`       | `vertex_label`       | 节点标签                                                     |
| `label_angel` | `vertex_label_angle` | 节点标签摆放在节点的相对方向角度，例如 0 度时是正右方        |
| `label_color` | `vertex_label_color` | 节点标签的颜色                                               |
| `label_dist`  | `vertex_label_dist`  | 节点标签距离节点的距离，与节点大小相关                       |
| `label_size`  | `vertex_label_size`  | 节点标签的字体大小                                           |
| `order`       | `vertex_order`       | 绘制节点的顺序；序号较小的节点会被优先绘制                   |
| `shape`       | `vertex_shape`       | 节点的形状；可用形状有：`rectangle`（正方形）、`circle`（圆形）、`hidden`（隐藏）、`triangle-up`（三角形）、`triangle-down`（倒三角），可以查看 `drawing_shapes` 获取更多形状 |
| `size`        | `vertex_size`        | 节点的大小（单位：像素）                                     |



#### 连边绘图布局参数

| 参数名        | 关键参数           | 用处                                                         |
| ------------- | ------------------ | ------------------------------------------------------------ |
| `color`       | `edge_color`       | 边的颜色                                                     |
| `curved`      | `edge_curved`      | 边的曲率；0 代表直边，负数时边向顺时针方向弯曲，正数时边向逆时针方向弯曲，`True` 视为 0.5，`False` 视为 0；使用该参数可以让多条边不互相遮挡，可以查看 `plot()` 的 `autocurve` 参数 |
| `font`        | `edge_font`        | 边的字体                                                     |
| `arrow_size`  | `edge_arrow_size`  | 有向图中，边的箭头的大小（相对于 15 像素）                   |
| `arrow_width` | `edge_arrow_width` | 有向图中，边的箭头的宽度（相对于 10 像素）                   |
| `width`       | `edge_width`       | 边的宽度（单位：像素）                                       |



#### `plot()` 的通用关键字参数

用于 `plot()` 的关键字参数如下：

| 关键字参数  | 用处                                                         |
| ----------- | ------------------------------------------------------------ |
| `autocurve` | 决定绘图时多边是否自动弯曲；10 000 条以下边的情况默认为 `True`，其余为 `False` |
| `bbox`      | 画布的边界框（Bounding BOX）大小，须为指定宽度和高度的元组；默认为 `(600,600)` |
| `layout`    | 采用的布局。可以是 `Layout` 实例，包含坐标元组的列表，或者是布局算法的名称；默认为 `auto`，根据图的大小和连接程度自动决定布局 |
| `margin`    | 画布的 上/右/下/左 边距，须为数组或列表；如果填写的元素少于 4 个，其中的元素会被重复使用 |



### 绘图时指定颜色

 `igraph` 可以用以下方式指定颜色（用于边、节点、标签参数）

#### X11 颜色名

可以查看维基百科中的 [X11颜色名](http://en.wikipedia.org/wiki/X11_color_names) 获得完整列表，或者查看 `igraph.drawing.colors.knows_colors` 字典的键，在这里不是大小写敏感，所以填入 `DarkBlue` 与 `darkblue` 效果相同。



#### CSS 样式颜色字符串

可按以下样式形成字符串填入（RGB分别代表红、绿、蓝）

* `#RRGGBB`，六位十六进制，每种颜色从 0 至 255（16 × 16）。如 `#0088ff` 。
* `#RGB` ，三位十六进制，每种颜色从 0 至 15，如 `#08f` 。
*  `rgb(R, G, B)`，可填入 0 至 255 的十进制整数或百分比。如 `rgb(0, 127, 255)` 或 `rgb(0%, 50%, 100%)`。



#### RGB 列表

样例：`[255, 128, 0]`，`(255, 128, 0)`，`"255, 128, 0"`。



### 保存绘图

`igraph` 可以将绘图保存为文件，这样可以用于诸如出版等场景。方法也很简单，只需要将目标文件名作为附加参数附在图对象后面即可。支持的格式由扩展引擎决定，`igraph` 可以保存 Cairo 支持的所有格式，如 SVG，PDF和 PNG 等。SVG 和 PDF 文件可以被转换为 `.ps` 和 `.eps` 格式，PNG 可以被转换为 `.tif` 。

```python
>>> ig.plot(g, "social_network.pdf", **visual_style)
```



## `igraph` 与外部包联动

`igraph` 提供许多方法从外部读取图和保存图：

| 格式            | 简称                        | 读取方法                 | 写入方法                  |
| --------------- | --------------------------- | ------------------------ | ------------------------- |
| 邻接列表/LGL    | `lgl`                       | `Graph.Read_Lgl()`       | `Graph.write_lgl()`       |
| 邻接矩阵        | `adajacency`                | `Graph.Read_Adjacency()` | `Graph.write_adjacency()` |
| DL              | `dl`                        | `Graph.Read_DL()`        | 暂无                      |
| 边列表          | `edgelist`，`edges`，`edge` | `Graph.Read_Edgelist()`  | `Graph.write_edgelist()`  |
| GraphViz        | `graphviz`，`dot`           | 暂无                     | `Graph.write_dot()`       |
| GML             | `gml`                       | `Graph.Read_GML()`       | `Graph.write_gml`         |
| GraphML         | `graphml`                   | `Graph.Read_GraphML()`   | `Graph.write_graphml()`   |
| Gzipped GraphML | `graphmlz`                  | `Graph.Read_GraphMLz()`  | `Graph.write_graphmlz()`  |
| LEDA            | `leda`                      | 暂无                     | `Graph.write_leda()`      |
| 标记边列表/NCOL | `ncol`                      | `Graph.Read_Ncol()`      | `Graph.write_ncol()`      |
| Pajek 格式      | `pajek`，`net`              | `Graph.Read_Pajek()`     | `Graph.write_pajek()`     |
| 压制图          | `pickle`                    | `Graph.Read_Pickle()`    | `Graph.write_pickle()`    |

例如，可以下载著名的 [扎卡里空手道俱乐部问题](http://nexus.igraph.org/api/dataset?id=1&format=GraphML) 的 [案例图文件](https://igraph.org/python/tutorial/0.9.7/_downloads/181f2c166825b410a01506e5bd30d7a4/zachary.zip)，解压后将其加载至 `igraph`。因为它是 GraphML 文件，需要使用对应的方法：

```python
>>> karate = ig.Graph.Read_GraphML("zachary.graphml")
>>> ig.summary(karate)
IGRAPH UNW- 34 78 -- Zachary's karate club network
```

如果想要将其转换为其他格式，比如 Pajek 支持的格式，可以继续转化保存：

```python
>>> karate.write_pajek("zachary.net")
```

需要注意，不同的格式均有其限制，比如有的格式不支持存储参数。最佳的存储 `igraph` 图的格式可能是 GraphML、GML，或者如果你没有参数，NCOL 和边列表也可以（NCOL 支持节点名和边权重）。如果你只是想暂时保存图，之后还要在 `igraph` 中使用，可以使用 `pickle` 格式保证获得相同的图表。

还有两个帮助方法：`load()` 是一个通用的读取方法，能够尝试从文件类型扩展中自动识别对应类型并读取；`Graph.save()` 能以文件类型扩展中默认指定格式保存图片，当然也可以用 `format` 参数填入简称覆盖默认保存类型。

```python
>>> karate = load("zachary.graphml")
>>> karate.save("zachary.net")
>>> karate.save("zachary.my_extension", format="gml")
```



# 结语

本篇仅仅从表面介绍了 `igraph` 包的一些功能，作者将继续完善完整的操作手册。可以查看 [API 手册](https://igraph.org/python/doc/api/index.html) 以获取完整的 `igraph` 类、函数、方法使用信息。 `Graph` 类是一个好的开始，如果遇到难懂的部分，可以在 [讨论组](https://igraph.discourse.group/) 中寻求帮助。
