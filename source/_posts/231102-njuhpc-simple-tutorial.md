---
title: NJUHPC 运行 Python 程序简明流程
comment: true
abbrlink: 6ab96d9d
date: 2023-11-02 08:24:53
updated:
tags: 
    - Python
    - bsub
    - HPC
categories: 工程实践
keywords:
description:
top_img: https://download.mariozzj.cn/img/picgo/202311021615799.png
cover: https://download.mariozzj.cn/img/picgo/202311021615799.png
katex: false
aside: true
---
[NJU HPC](https://hpc.nju.edu.cn/) 指南京大学人工微结构科学与技术协同创新中心高性能计算中心，中心提供多种软硬件资源以供高性能运算服务，本文简单介绍在 NJU HPC 上运行 Python 程序的流程，更多详细使用方法可参见 [使用手册](https://doc.nju.edu.cn/books/efe93)。

# 账号注册与登录

## 申请账号

NJU HPC 的用户由用户组方式管理，如课题组已申请用户组，新用户可以在 [注册页面](https://scc.nju.edu.cn/user/register) 注册个人用户，其中用户组名称填写课题组的用户组。
提交表单后，用户组组长可以对新用户进行审核，审核通过后，系统会向注册时填写的邮箱发送新用户的账号、密码。
该密码即为后文中提到的登录密码。
随后，新用户即可在 `scc.nju.edu.cn` 登录。在后台页面可以对账户信息、密码等进行修改。

![SCC 后台页面](https://download.mariozzj.cn/img/picgo20231102090035.png)

在后台页面的【财务管理】功能中可以查看提交作业及其费用情况，一般每日 2:00 更新前一周期作业结果。

## 绑定二度认证

关于二度认证的详细信息可参见 [eScience服务双重认证简明指南](https://doc.nju.edu.cn/books/18d80/page/escience-hap)，可供使用的二度认证客户端可参考 [远程登录文档](https://doc.nju.edu.cn/books/efe93/page/b1a59)，下文以 Microsoft Authenticator 为例。

获得账号用户可用账号密码登录 `access.nju.edu.cn` 连接资产。
首次登录时会要求绑定二度认证，打开 Microsoft Authenticator，点击右上角的 `+`，选择 `其他账户`，扫描二维码，即可完成绑定。

![使用 Microsoft Authenticator 绑定二度认证](https://download.mariozzj.cn/img/picgo20231102091332.png)

绑定后，点击列表中的 `access.nju.edu.cn`，即可查看二度认证的即时六位验证码，在浏览器中输入当前六位验证码即可完成绑定。

# SSH 登录资产

HPC登陆节点为 Linux 系统，推荐用户与资产使用命令行进行交互，因此用户最好掌握基础的 Linux 命令行操作。
这里以 Windows Powershell 为例，介绍如何使用 SSH 登录资产。

打开 Powershell，输入 `ssh <user>@access.nju.edu.cn`，其中 `<user>` 替换为自己的用户名，然后回车。
系统此时会要求输入密码，请注意，**ssh 连接登录节点的密码为登陆密码与实时二度认证验证码的结合**，使用空格分隔。
如登录密码为 `pswd888.nju`，当前二度认证验证码为 `876543`，则要求输入密码时输入 `pswd888.nju 876543`，然后回车即可。

![SSH 交互](https://download.mariozzj.cn/img/picgo/202311021429079.png)

在 HPC，登录节点和计算节点共享部分文件系统，因此可以使用 `scp` 命令传输文件到登录节点供使用，也可以 [使用 XFtp 等工具传输文件](https://doc.nju.edu.cn/link/2#bkmrk-sftp登录（文件传输）)。
如果需要从非内网/教育网终端传输文件，则可以使用 [南大云盘](https://box.nju.edu.cn/)，将文件传输至云盘后，登录节点可以挂载云盘，从而访问云盘中的文件。

详细的集成云盘教程可见 [集成云盘教程](https://doc.nju.edu.cn/books/efe93/page/352fe)。

# 模块环境管理

集群（包括登录节点和计算节点）上安装了大量的软件，用户可以通过 `module` 命令加载需要的软件环境。
常用的模块管理命令见 [文档#环境变量](https://doc.nju.edu.cn/books/efe93/page/e1e32)。
这里，我们使用 `module avail` 命令查看可用的模块：

```shell
$ module avail
------------------------------------ /fs00/software/modulefiles ------------------------------------
abaqus/6.14-5                    gcc/7.4.0                   make/4.2.1
anaconda/2                       gcc/7.5.0                   make/4.3
anaconda/2-4.3.1                 gcc/8.2.0                   mathematica/11.0
anaconda/2-5.0.1                 gcc/8.3.0                   mathematica/12.1.0
anaconda/3                       gcc/8.5.0                   matlab/r2020a
anaconda/3-4.3.1                 gcc/9.2.0                   nccl/2.16.2-cuda11.0
anaconda/3-5.0.1                 gcc/9.5.0                   nccl/2.16.2-cuda11.8
aocc/2.0.0                       gcc/10.2.0                  nccl/2.16.2-cuda12.0
aocc/2.1.0                       gcc/10.4.0                  nccl/10.1-v2.4.8
aocc/2.3.0                       gcc/11.2.0                  nccl/10.2-v2.5.6
aocl/2.0                         gcc/11.3.0                  netcdf/c-4.7.0
aocl/2.2                         gcc/12.1.0                  openfoam/v1806-ips2017u6
aws-cli/2.9.6                    git/2.38.1                  openmpi/1.10.0-gcc-5.2.0
:q
```

结果可以看出，同一个软件可能在集群安装了多个版本模块，这里我们选择 `anaconda/3-5.0.1` 模块，接下来使用 `module load` 命令加载该模块：

```shell
$ module load anaconda/3-5.0.1
$ which conda # 查看 conda 命令的路径
/fs00/software/anaconda/3-5.0.1/bin/conda
```

可以看到 `conda` 命令的路径已经被添加到环境变量中，接下来我们可以使用 `conda` 命令创建虚拟环境，安装 Python 包等。

# 创建 conda 创建虚拟环境并安装依赖

**推荐使用 conda 创建虚拟环境用于运行 Python 程序**，因为一方面在虚拟环境中我们普通用户权限也可以方便安装 Python 包，另一方面可以根据不同项目需求创建不同的虚拟环境，避免不同项目间的依赖冲突。
我们使用 `conda create` 命令创建一个名为 `test` 的虚拟环境：

```shell
$ conda create -n test python=3.8
```

创建完成后，使用 `source activate` 命令即可激活该虚拟环境：

```shell
$ source activate test
```

激活后，命令行提示符前会出现 `(test)` 字样，表示当前虚拟环境已激活。激活环境后，我们就可以在环境中安装 Python 包了。由于 [南大 anaconda 源公告停止服务](https://nju-mirror-help.njuer.org/anaconda.html)，而 [登录节点访问公网需要认证](https://doc.nju.edu.cn/books/efe93/page/a1d0c)，我们可以在环境中使用 `pip` 安装依赖，同时使用 `-i` 参数指定可访问的南大内网源，以安装安装包较大的 `torch` 为例：

```shell
$ pip install -i https://mirrors.nju.edu.cn/pypi/web/simple torch==2.0
```

在内网下的安装包下载速度可达千兆。安装完成后，运行的 Python 程序就可以引入 `torch` 包了。

# 提交作业

集群使用作业调度系统管理所有计算作业，该系统接受用户的作业请求，并将作业合理的分配到合适的节点上运行。
用户在登录节点使用 `bsub` 命令提交作业，该命令存在多个参数，这里列出几个重要参数：
* `-J <job_name>`：指定作业名称，以供后续查看作业状态时使用；
* `-m <node>`：指定作业运行的节点，在 [计算资源](https://doc.nju.edu.cn/books/efe93/page/d9640) 页面可查看完整的运行队列，在登录节点也可使用 `bqueues`；
* `-o <output_file>`：标准输出文件
* `-e <error_file>`：错误输出文件
* `-n <num_cpu>`：指定作业运行需要的 CPU 核心数，如 `-n 4` 表示需要 4 个 CPU 核心；
* `-gpu <opt_str>`：指定作业运行的 GPU 参数，参数以 `:` 分隔选项，默认值为 `num=1:mode=shared:mps=no`，包含以下选项：
    * `num=number`：每台主机需要 GPU 的数量，当指定 GPU 数量时，CPU 核心数会根据队列中的 GPU 资源自动分配；
    * `mode=shared|exclusive_process`：GPU运行模式，`shared`对应 Nvidia/AMD DEFAULT compute mode、`exclusive_process`对应 Nvidia EXCLUSIVE_PROCESS
    * `mps=yes|no`：开启或关闭Nvidia Multi-Process Service (MPS)。关闭MPS，多进程通过时间分片的方式共享GPU；开启MPS，多进程共享一个CUDA Context并发执行，增加了GPU利用率。

其他参数可查阅 [IBM 手册](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=bsub-options) 或 [提交作业教程](https://doc.nju.edu.cn/books/efe93/page/4f4ad)。例如，我们需要在 `734090ib` 队列提交一个 GPU 计算任务——运行 `train.py`，占用 1 张显卡，则输入以下命令：

```shell
$ bsub -J train_model -m 734090ib -gpu num=1:mode=shared:mps=no python train.py
Job <********> is submitted to queue <734090ib>.
```

提交作业后，系统会返回一个作业 ID，以供后续查看作业状态时使用。

除了用命令行方式提交作业，也可使用脚本方式提交作业，形如 `bsub < jobfile.sh`，其中 `jobfile.sh` 为脚本文件，在脚本文件中可以写多行命令，同时 `bsub` 命令的各个参数可以在脚本文件中定义，如以上命令等价于以下脚本：

```shell
#!/bin/bash
#BSUB -J train_model
#BSUB -m 734090ib
#BSUB -gpu num=1:mode=shared:mps=no

python train.py
```

最后需要注意，作业最终是提交到计算节点上运行，因此需要在执行 `python` 命令前需要先激活虚拟环境，加载 anaconda，因此完整的脚本应该为：

```shell
#!/bin/bash
#BSUB -J train_model
#BSUB -m 734090ib
#BSUB -gpu num=1:mode=shared:mps=no

module load anaconda/3-5.0.1
source activate test
python train.py
```

# 作业管理

作业提交后会得到一个作业 ID，可以使用 `bjobs` 命令查看作业状态：

```shell
$ bjobs ********
JOBID    USER        STAT  QUEUE       JOB_NAME   SUBMIT_TIME  EXEC_HOST
******** ********** RUN   734090ib    train_model  Nov  2 15:51 8*m006
```

可以看到，该作业已经在 `734090ib` 队列中运行，运行的节点为 `m006`，同时可以看到该作业的状态为 `RUN`，表示正在运行。
在作业运行的过程中，我们可以使用 `ssh` 命令连接计算节点查看状态，这里 `ssh m006` 即可。

如果我们需要终止提交的作业，则可以使用 `bkill` 命令终止作业：

```shell
$ bkill *******
Job <********> is being terminated
```

以上就是一次利用 NJUHPC 运行 Python 脚本的简明流程。更多详细用法，可以查阅 [超级计算指引文档](https://doc.nju.edu.cn/books/efe93)。
