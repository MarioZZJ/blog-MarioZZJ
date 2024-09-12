---
title: Jupyter——轻量级数据处理分析利器
comment: true
abbrlink: 16cc73c
tags:
  - Jupyter
  - Python
categories: 工具干货
keywords: Jupyter
desc: 通过本文，了解数据分析常用工具 Jupyter Notebook/Lab 的使用！
cover: 'https://download.mariozzj.cn/img/picgo/202110091016118.jpeg'
katex: true
aside: true
date: 2021-10-09 10:12:48
updated: 2021-10-09 00:00:00
---

之前初学Python时，老师推荐的是Jetbrains出品的PyCharm作为IDE，对初学者而言感觉PyCharm非常实用，但是随着学习的深入也逐渐能感觉到使用PyCharm的一些缺陷：

1. 不为项目创建虚拟环境的话，开启项目需要花费较多时间导入包；而创建虚拟环境又需要花时间安装包，且徒占内存。
2. 脚本文件是一次性全部运行的。虽然可以使用debug功能打断点，但是如果发现一小部分出错还是需要重新全部运行。在模型训练等过程中这种情况可能是致命的。
3. 仅在科学模式下能实时预览matplotlib等可视化库绘制的图片，比较麻烦。
4. 代码注释仅有`#`和`'''`两种形式，且不能富文本编辑，难以辅助代码扩展和可理解。



因此，需要一款能够解决上述问题的IDE来加快数据科学学习和应用过程——Jupyter Notebook。



# Jupyter Notebook



## Jupyter Notebook简介

> Jupyter Notebook是基于网页的用于交互计算的应用程序。其可被应用于全过程计算：开发、文档编写、运行代码和展示结果。
>
> ——[JupyterNotebook文档](https://jupyter-notebook.readthedocs.io/)

![Jupyter Notebook](https://download.mariozzj.cn/img/picgo/202110091020979.jpeg)

与PyCharm不同，Jupyter Notebook没有独立的GUI界面和桌面应用程序，是通过浏览器来使用的。Jupyter Notebook既可以安装在本机电脑上通过本地服务器访问，也可以安装在远程服务器上远程使用。

Jupyter Notebook解析的文件后缀名为`.ipynb`。该类文件是以json形式书写，但在Jupyter Notebook页面内主要解析为三种类型的块（cell）：代码块（code）、文档块（markdown）、纯文本（Raw NBConvert）。

* **代码块**包含代码片和输出片。Jupyter Notebook原名IPython Notebook，对Python语言提供支持，书写的Python代码支持代码高亮，支持注释、缩进、Tab补全等基础功能。输出部分，相比于Pycharm等接近cmd只支持文字输出，Jupyter在浏览器中可以渲染出DataFrame表格、可交互的PyeCharts图形、Matplotlib图片等基于html、svg、png等格式的输出。

  ![Code Cell](https://download.mariozzj.cn/img/picgo/202110091020560.jpeg)

* **文档块**原生支持Markdown语法，可以进行简单的富文本编辑和多媒体插入，同时也支持Latex语法的解析，可以输入数学说明。

  ![Markdown Cell](https://download.mariozzj.cn/img/picgo/202110091020806.jpeg)



用户可以自由控制插入代码块和文档块，形成交错易于解释说明，同时也可自由控制代码运行顺序，环境启动后运行的代码块中保留的变量不会被删除，可以跨代码块在其他块中使用。

您可以访问Jupyter官网，在线[试用](https://jupyter.org/try)JupyterNotebook。



## 安装与启动

[Jupyter官网](https://jupyter.org/install.html)也给出了安装教程，摘录如下：

如果您已经安装了`conda`，可以使用conda进行安装。在CMD/Terminal输入如下命令：

```shell
conda install -c conda-forge notebook
```

或者，如果您想使用`pip`安装，在安装好pip之后可以在CMD/Terminal输入如下命令：

```shell
pip install notebook
```



安装完成后，在CMD/Terminal中输入如下命令即可启动：

```shell
jupyter notebook
```

一般而言，系统会帮您在默认浏览器打开Jupyter Notebook主界面（文件系统），如果没有自动打开，您也可以参照命令行输出在浏览器自行进入。





# 简单使用



## 基础操作

### 文件列表页面

![Notebook 文件列表](https://download.mariozzj.cn/img/picgo/202110091021795.jpeg)

在目录页面，用户可以新建空脚本文件进行编辑，也可以新建终端以Shell操作目录（因此，可以进行包的安装）。Jupyter Notebook会对ipynb脚本文件进行渲染，但同时也能够以纯文本打开其他文件。在此页面选中文件可以选择终止运行、复制、重命名、打开、下载、删除等操作。



### 脚本页面

![Jupyter Notebook 脚本页面](https://download.mariozzj.cn/img/picgo/202110091021998.jpeg)

脚本页面，用户可以点击文件名进行文件的重命名，可以通过菜单栏和功能区进行一些操作。就基础操作而言，用户可以使用功能区的`+`按钮添加单元格，使用运行按钮执行代码块或渲染文档块，使用下拉栏调整块的类型。

当然，也可以通过单击代码块旁的运行键来执行代码块。



## 基础快捷键

Jupyter Notebook的快捷键较多，这里按单元格的两种模式分别归纳。单元格模式可分为命令模式和编辑模式，主要区别在单元格框线的颜色以及内容是否可编辑，如图。

![Jupyter Notebook 单元格模式](https://download.mariozzj.cn/img/picgo/202110091022481.jpeg)



### 命令模式

* `Enter`：转为编辑模式

* `Shift`+`Enter`：运行本单元格并选中下个单元格

* `Ctrl`+`Enter`：运行本单元格

* `Alt`+`Enter`：运行本单元格并在下方插入新单元格

* `Y`：单元格变为代码状态

* `M`：单元格变为文档状态

* `↑` / `K`：选中上方单元格

* `↓` / `J`：选中下方单元格

* `A`：在上方插入新单元格

* `B`：在下方插入新单元格

* `C` / `X` / `V` ：复制 / 剪切 / 粘贴单元格

* `D`：删除单元格

* 长按`Shift`可连续选中


  仅列举部分常用快捷键。如需获取**全部快捷键**，可以在命令模式下按`H`进入**快捷键帮助**查看所有的快捷键。



### 编辑模式

* `Esc`：进入命令模式
* `Shift`+`Enter`：运行本单元格并选中下个单元格
* `Ctrl`+`Enter`：运行本单元格
* `Alt`+`Enter`：运行本单元格并在下方插入新单元格
* `Tab`：缩进 / 代码补全
* `Shift`+`Tab`：查看工具提示
* `Ctrl`+`]`：缩进
* `Ctrl`+`[`：取消缩进
* `Ctrl`+`/`：注释当前行 / 选中内容
* `Ctrl`+`Z`：撤销
* `Ctrl`+`Y`：重做
* `Ctrl`+`A`：全选
* `Ctrl`+`S`：保存



### 文件操作

![Notebook 脚本下载](https://download.mariozzj.cn/img/picgo/202110091023354.jpeg)

可以在菜单栏的Files菜单中选择各类文件操作，如新建、打开、保存等。

您可以给脚本设置检查点，便于版本回溯和重置。

同时也可以将其下载或导出为其他格式，便于分享、打印、发布等操作。在之后的进阶操作中还会再提及。



# 进阶技巧

## 启动命令参数

在[安装与启动](##安装与启动)中已经介绍过在CMD/Terminal中的启动方法：

```shell
jupyter notebook
```

执行命令后，终端会显示一系列服务器信息：

```powershell
$ jupyter notebook
[I 08:58:24.417 NotebookApp] Serving notebooks from local directory: /Users/catherine
[I 08:58:24.417 NotebookApp] 0 active kernels
[I 08:58:24.417 NotebookApp] The Jupyter Notebook is running at: http://localhost:8888/
[I 08:58:24.417 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
```

出现以上信息表示服务开启成功，浏览器会自动打开窗口进入主界面。

### 端口

默认情况下一个Jupyter Notebook进程会占用一个端口，默认从8888开始，如果端口被占用则选用邻近端口如8889、8890等。我们可以在启动时指定端口：

```shell
jupyter notebook --port <port_number>
```

其中`<port_number>`为指定的端口号。



###  文件目录

一般而言打开Jupyter Notebook会进入默认的文件路径，有的时候我们想要打开不再默认路径下的脚本文件，就可以选择指定目录启动：

```shell
jupyter notebook <dir>
```

其中`<dir>`为需要打开的目录，可以是相对路径也可以是绝对路径。

例如，在Windows下，我们可以在文件资源管理器的地址栏输入cmd来快速启动命令行，随后仅需键入`jupyter notebook ./`即可在Jupyter Notebook中打开当前目录。

![启动操作](https://download.mariozzj.cn/img/picgo/202110091024858.gif)



或者，如果需要直接更改默认文件目录，只需要进行配置文件的修改。方法如下：

1. 首先，在命令行输入命令得到jupyter notebook的安装地址：

   ```bash
   $ jupyter notebook --generate-config
   
   Overwrite C:\Users\PM\.jupyter\jupyter_notebook_config.py with default config? [y/N]y
   Writing default config to: C:\Users\PM\.jupyter\jupyter_notebook_config.py
   ```

   可以看到返回中给出了jupyter notebook的安装地址，配置文件在此目录下。

2. 前往该目录，使用vim等文本编辑方法编辑目录下的`jupyter_notebook_config.py`文件，找到如下代码段：

   ```Python
   ## The directory to use for notebooks and kernels.
   #c.NotebookApp.notebook_dir = ''
   ```

   将第二行内容取消注释，并将引号内更改为您需要指定的工作空间地址即可。

   

   随后重新启动Jupyter Notebook即可在指定空间打开。



## 主题更换方法

### 基于Jupyterthemes

Jupyter Notebook默认主题为浅色主题，虽然文字显示清晰，但长时间使用可能对眼睛刺激较大。我们可以借用Jupyterthemes插件来为Notebook更换合适的主题。

首先在CMD/Terminal输入如下命令安装Jupyterthemes插件：

```shell
pip install jupyterthemes
```

安装完成后即可使用。该库在使用时仅取两首字母`jt`为命令。完整的命令格式如下：

```shell
jt  [-h] [-l] [-t THEME] [-f MONOFONT] [-fs MONOSIZE] [-nf NBFONT]
    [-nfs NBFONTSIZE] [-tf TCFONT] [-tfs TCFONTSIZE] [-dfs DFFONTSIZE]
    [-m MARGINS] [-cursw CURSORWIDTH] [-cursc CURSORCOLOR] [-vim]
    [-cellw CELLWIDTH] [-lineh LINEHEIGHT] [-altp] [-altmd] [-altout]
    [-P] [-T] [-N] [-r] [-dfonts]
```

例如，使用如下命令

```shell
jt -t monokai -ofs 10 -nfs 13 -tfs 13 -fs 12 -T -N -lineh 140
```

表明使用monokai主题，输出字体大小10，notebook字体大小13，文档块字体大小13，代码字体大小12，展示工具栏，展示文档名和logo，行高140。



完整的参数解释见[jupyter-theme](https://github.com/dunovank/jupyter-themes)官方仓库。

使用`jt -l`命令可以查看目前安装的主题：

```bash
$ jt -l

Available Themes:
   chesterish
   grade3
   gruvboxd
   gruvboxl
   monokai
   oceans16
   onedork
   solarizedd
   solarizedl
```

![主题展示](https://download.mariozzj.cn/img/picgo/202110091024596.jpeg)

上图展示了部分主题样式基本情况，可根据喜好自行更换主题。

使用`jt -r`命令则可以重置主题。



### 基于Stylish

因为Jupyter Notebook是在浏览器呈现，那么任意形式的更换样式表就可以实现对主题的修改。例如较为著名的Stylish插件的社区中就有很多开发者分享了自己定制的Jupyter Notebook主题，这些主题的呈现效果整体上优于jupyterthemes。

[Stylish官网](https://userstyles.org/)

[Stylish-Chrome插件下载](https://chrome.google.com/webstore/detail/stylish-custom-themes-for/fjnbnpbmkenffdnngjfgmeleoegfcffe)

而且，如果您熟练掌握CSS语法，也可以自行基于Stylish定制合适的Jupyter Notebook主题。

![Stylish 暗黑主题效果](https://download.mariozzj.cn/img/picgo/202110091024781.jpeg)



## 魔法命令

魔法命令在代码块中执行，但是不属于Python语言的代码。使用魔法命令可以在执行代码的同时执行附加效果。

### 执行Shell命令

执行Shell命令并不是魔法命令的一部分，但是功能类似。

在代码块中键入`!`+shell命令即可在当前目录执行。

典型的应用场景例如，当我们需要使用某个包但是Jupyter Notebook所用环境中并没有此包，那么就可以利用此特性，在Notebook内完成包的安装。

```python
!pip install selenium
```

```output
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting selenium
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/80/d6/4294f0b4bce4de0abf13e17190289f9d0613b0a44e5dd6a7f5ca98459853/selenium-3.141.0-py2.py3-none-any.whl (904 kB)
     |████████████████████████████████| 904 kB 1.2 MB/s eta 0:00:01
Requirement already satisfied: urllib3 in /root/anaconda3/lib/python3.8/site-packages (from selenium) (1.25.9)
Installing collected packages: selenium
Successfully installed selenium-3.141.0
```

或者，我们共享脚本给他人运行时，需要其下载一些文件，就可以使用wget命令来下载：

```python
!wget https://tianchi-competition.oss-cn-hangzhou.aliyuncs.com/531810/test_a.csv.zip
```

```output
--2021-01-08 21:51:25--  https://tianchi-competition.oss-cn-hangzhou.aliyuncs.com/531810/test_a.csv.zip
Resolving tianchi-competition.oss-cn-hangzhou.aliyuncs.com (tianchi-competition.oss-cn-hangzhou.aliyuncs.com)... 118.31.232.194
Connecting to tianchi-competition.oss-cn-hangzhou.aliyuncs.com (tianchi-competition.oss-cn-hangzhou.aliyuncs.com)|118.31.232.194|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 61986708 (59M) [application/zip]
Saving to: ‘test_a.csv.zip’

100%[======================================>] 61,986,708  14.6MB/s   in 4.0s   

2021-01-08 21:51:30 (14.9 MB/s) - ‘test_a.csv.zip’ saved [61986708/619867
```



### 常用魔法命令

#### %run

```magic command
%run 脚本文件地址
```

可以在接下来的代码块中运行指定的脚本文件。

#### %time系列

```magic command
%time {一行代码}
%%time 
{代码块剩余内容}

%timeit {一行代码}
%%timeit
{代码块剩余内容}
```

使用%time和%%time可以计算一行/一个单元格内的代码执行一次所需的时间。这在模型训练和大文件读取时是很实用的，有助于使用者把握时间和环境性能。

而%timeit和%%timeit会将指定代码内容执行很多次，最后计算出平均运行所需时间，一般用于算法的评估。

#### %whos

该命令可以查看本脚本中仍在占用内存的变量的类型和值等情况

```Python
j = 9
i = 10
s = "This is a String"

%whos
```

```output
Variable   Type         Data/Info
---------------------------------
i          int          10
j          int          9
np         module       <module 'numpy' from '/ro<...>kages/numpy/__init__.py'>
pd         module       <module 'pandas' from '/r<...>ages/pandas/__init__.py'>
s          str          This is a String
```

#### %del %reset

使用`%del`可以删除某个变量；使用`%reset`可以清除所有变量。

#### %load

使用`%load`可以加载脚本文件内容并填充到代码块中。值得一提的是%load后不仅可以接本地计算机上的绝对地址和相对地址，还可以接其他服务器的地址，例如使用%load获取某网页的原代码：

```python
%load http://crafts.mariozzj.cn/
```

#### %matplotlib inline

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
plt.plot(np.arange(20))
```

在使用matplotlib绘图之前写上这样一行魔法命令可以让绘制出的图片直接显示在输出区域。

## 导出

Jupyter Notebook的代码块和文档块可以以合适的形式导出，导出的格式包括HTML、LaTeX、Markdown、PDF等。

导出的Markdown中，普通文档块成为Markdown文档，而代码块成为Markdown的代码块，输出部分也成为代码块（输出的图片、表格等不在代码块内，但是仍然会有效保存）。

导出的PDF是经由LaTeX转化的，最大限度地保留了原始脚本的呈现效果，在代码块和输出区旁会显示In/Out+编号，调整适配到合适的纸张大小。需要注意的是在Jupyter Notebook中直接导出PDF，不支持显示中文，但是也有可以导出含中文PDF的方法。

1. 经由md导出PDF

   在Jupyter Notebook中以markdown格式导出文件后，使用Typora等markdown文本编辑器再对格式进行转换，可以生成PDF以供打印或导出。由于转换为PDF的过程是在编辑器中进行的，所以最终格式是由编辑器决定的，而且不会保留IN/OUT标注等信息。

2. 经由LaTeX导出PDF

   在Jupyter Notebook中可以以LaTeX格式导出，随后可以用LaTeX编辑器进行转换，或者采取以下步骤以使转换的PDF支持显示中文：

   1. 安装MiKTeX。[下载链接](https://miktex.org/download)

   2. 生成LaTeX文件，可以在CMD/Terminal中进入脚本文件所在目录，输入`jupyter nbconvert --to latex 文件名.ipynb`，等待后生成文件名.tex文件。

   3. 使用文本编辑器打开文件名.tex文件，在`\documentclass{article}`后或`\documentclass[11pt]{article}`后或`\documentclass[11pt]{ctexart}`后插入以下两行语句，保存。

      ```tex
      \usepackage{fontspec, xunicode, xltxtra}
      \usepackage{ctex}
      \setmainfont{Microsoft YaHei}
      ```

   ```
   
   ```

3. 再在CMD/Terminal中输入`xelatex 文件名.tex`即可完成转化，生成的文件名.pdf在同目录下。

   ![转换操作](https://download.mariozzj.cn/img/picgo/202110091025604.gif)



# 升级体验

Jupyter Notebook 已经展现出了它在数据分析与处理方面的便捷性，但是使用下来还是有一些体验可以优化，例如：

* 相比于传统 IDE，文件目录和脚本操作页面是分开的，如果需要切换文件可能比较麻烦
* Notebook 需要先单独在终端启动，才具有查看 `.ipynb` 文件的能力，不能直接双击打开

这里提供两个能够缓解上述使用痛点的工具，因为只是升级体验，这两个工具的基本使用和 Notebook 类似，就不再赘述。

## JupyterLab

Jupyter Lab 和 Jupyter Notebook 一样同属于 Jupyter Project，二者在使用上几乎一致，个人认为 Jupyter Lab 是在 Notebook 基础上进行了升级。

### 安装与启动

[Jupyter官网](https://jupyter.org/install.html) 也给出了安装教程，摘录如下：

如果您已经安装了`conda`，可以使用conda进行安装。在 CMD/Terminal 输入如下命令：

```shell
conda install -c conda-forge jupyterlab
```

或者，如果您想使用`pip`安装，在安装好pip之后可以在 CMD/Terminal 输入如下命令：

```shell
pip install jupyterlab
```



安装完成后，在 CMD/Terminal 中输入如下命令即可启动：

```shell
jupyter-lab
```

一般而言，系统会帮您在默认浏览器打开 Jupyter Lab 主界面（文件系统），如果没有自动打开，您也可以参照命令行输出在浏览器自行进入。



### 界面展示

Lab 最主要变化在于在页面左侧加入了文件目录，打开的标签页以内嵌标签页而不是浏览器标签页展示。

![Jupyterlab 主界面](https://download.mariozzj.cn/img/picgo/202110091104518.png)

对 `.ipynb` 文件的操作，Lab 和 Notebook 完全相同，因此切换成本很低！

同时，Jupyter Lab 支持渲染 csv 等结构化数据为**表格形式**，对纯文本文件也可以直接编辑。

![Jupyter Lab 表格渲染](https://download.mariozzj.cn/img/picgo/202110091106880.png)



## VS Code

VS Code 是一款强大轻量级 IDE，支持丰富的语言拓展，同时还有多种强力插件。

### 安装与启动

如果没有安装 VS Code，可以前往官方页面下载安装： [Download Visual Studio Code - Mac, Linux, Windows](https://code.visualstudio.com/Download)

如果已安装 VS Code，但是没有安装 Python，可以在扩展页面安装：

![VSC安装扩展-Python](https://download.mariozzj.cn/img/picgo/202110091151070.png)

VS Code 安装 Jupyter 扩展后，就可以支持渲染 `.ipynb` 文件了，在扩展页面安装即可：

![VSC安装扩展-Jupyter](https://download.mariozzj.cn/img/picgo/202110091154620.png)

### 界面展示

VS Code 对 `.ipynb` 脚本的页面操作也很类似，同时也支持文件树、版本控制等，相对而言功能更加强大。

VS Code 相对于 Notebook 的提升在于：**增加了文件目录**，同时能够在文件夹中**直接双击** `.ipynb` **文件打开预览**，不需要启动服务就可以渲染查看。

![VSCode 主界面](https://download.mariozzj.cn/img/picgo/202110091156617.png)

