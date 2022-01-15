---
title: 玩转服务器 | 搭建 24h 在线的《饥荒》联机服务器
comments: true
copyright: true
abbrlink: 8d6b8da8
date: 2022-01-14 22:08:06
updated: 2022-01-14 22:08:06
tags:
  - 游戏《饥荒》
  - 云服务器应用
  - 联机游戏
categories: 玩转服务器
keywords: 
  - 饥荒
  - 联机游戏
description: 用闲置云服务器和朋友们快乐联机《饥荒》！
top_img: https://download.mariozzj.cn/img/picgo/202201151235435.png
cover: https://download.mariozzj.cn/img/picgo/202201151236429.jpeg
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
katex: false
aside: true
---

# 前言

从这篇开始会写一个系列，叫“玩转服务器”，分享一下怎么把闲置云服务器的价值利用起来。

这几天想和朋友玩《饥荒》联机，一般的联机方式是房主（Host）创建房间，我们再加入。但是我们发现这种方式不太方便，因为《饥荒》作为一款周期不算短的游戏，是可能要把游戏进程存档下去继续玩的，而游戏的存档默认放在房主电脑的本地，也就意味着每次打这个世界都需要房主开服，非常麻烦。

因为近期我的云服务器也闲置，参考了网上一系列的教程后摸索出了目前（2022.1）能够稳定建立《饥荒》联机服务器的方法（我的服务器近期也重装了，所以应该在其他机子上面不会有兼容性和依赖性问题），在此做记录。



我的服务器配置如下：

* 型号：Tencent Cloud CVM（腾讯云轻量应用服务器）
* 实例规格：
  * 2核 CPU（2x Intel(R) Xeon(R) Gold 6148 CPU @ 2.40GHz）
  * 4G 内存
  * 8M 带宽
  * 80G 硬盘
* 系统：Ubuntu 20.04.3 LTS（Linux 系统）



在我的 Windows 机上的交互需要用到的工具：

* Windows Terminal + Powershell
* XFtp



# 环境准备

## 依赖库安装

使用服务器管理员账户安装：

```shell
dpkg --add-architecture i386
sudo apt-get update
sudo apt-get install libstdc++6:i386 libgcc1:i386 libcurl4-gnutls-dev:i386 SDL
```

## 创建用户，安装 SteamCMD 和游戏

```shell
sudo adduser steam
```

输入账户基本信息后即可创建名为 `steam` 的用户。这里不将该用户设为管理员，单独创建用户的目的是为了隔离数据，同时保证服务器其他功能不受影响。随后输入命令和密码切换到该用户：

```shell
su - steam
```

现在我们在名为 `steam` 的用户目录下（`/home/steam`），可下载 SteamCMD 并解压安装：

```shell
mkdir ~/steamcmd
cd ~/steamcmd
wget http://media.steampowered.com/installer/steamcmd_linux.tar.gz
tar -xvzf steamcmd_linux.tar.gz
```

随后我们可以启动 SteamCMD：

```shell
./steamcmd.sh
```

进行必要的启动后，我们会进入 Steam 命令行模式，终端前缀不再是 `$` 而是 `steam >`。首先我们创建游戏目录，再匿名登录 steam 更新游戏，最后退出 Steam 命令行即可。

```steamcmd
force_install_dir ../dstserver
login anonymous
app_update 343050 validate
quit
```

## 开放端口

如果服务器有防火墙、安全组等，一定要开放指定的端口。默认的端口大概在 10000 - 12000 之间，具体需要开放的端口可以查看存档文件夹下的这几个文件：

* `~/.klei/DoNotStarveTogether/Cluster_1/cluster.ini` 中的 `master_port`。
* `~/.klei/DoNotStarveTogether/Cluster_1/Master/server.ini` 中的 `server_port`。
* `~/.klei/DoNotStarveTogether/Cluster_1/Caves/server.ini` 中的 `server_port`、`master_server_port`、`authentication_port`。

最好 TCP、UDP 全部打开。

# 创建世界

## 在本机完成世界的创建

在主力机上打开《饥荒联机版》，在选单界面点击【创建游戏】对联机需要创建的世界进行配置，服务器模式选择【公共】，并配置好密码。

![本机创建游戏](https://download.mariozzj.cn/img/picgo/202201151238562.png)

建议在世界创建时即添加好模组，便于后续云服务器端模组的导入。

创建世界进入游戏后，即可退出世界，回到游戏选单界面。

## 本地存档导出

![【数据】](https://download.mariozzj.cn/img/picgo/202201151239734.png)

在选单界面点击【数据】，此时文件资源管理器会打开《饥荒》存档文件夹，该文件夹下每个世界都有一个文件夹用于存放存档。找到刚才创建世界对应的存档文件夹，这个文件夹稍后将上传到云服务器。

## 获取 Token

![【账号】](https://download.mariozzj.cn/img/picgo/202201151240163.png)

回到选单界面，点击【账号】即可进入 Klei 账户界面，在首页，复制自己的 Klei 用户 id 并记录下来。

![获取用户id](https://download.mariozzj.cn/img/picgo/202201151242887.png)

点击【游戏】选项卡，选择《饥荒联机版》的游戏服务器，输入集群服务器名即可创建，建议同时配置服务器密码。随后在该页面会看到该服务器对应的 Token，将其复制并记录下来。

![获取服务器Token](https://download.mariozzj.cn/img/picgo/202201151245561.png)



# 配置服务器端世界

## 服务器端生成默认配置文件

使用之前创建的 `steam` 账户登录服务器，终端下执行下列命令生成默认配置文件

```shell
cd ~/dstserver/bin
./dontstarve_dedicated_server_nullrenderer
```

生成完成后按 `Ctrl+C` 强制退出即可。

## 设置 Token 和管理员

上一步应该创建了饥荒存档文件夹，此时在存档文件夹里面配置 Token。使用 Vim 打开配置文件

```shell
vim ~/.klei/DoNotStarveTogether/cluster_token.txt
```

按 `i` 进入编辑模式，输入刚才的 Token。再按 `Esc` 进入命令模式，输入 `:wq` 保存并退出。使用同样的方法，编辑刚才默认配置世界下的文件：

```shell
vim ~/.klei/DoNotStarveTogether/Cluster_1/cluster_token.txt
```

随后，我们可以将我们配置为该饥荒世界的管理员。同样在世界存档文件夹下添加 `adminlist.txt`：

```shell
vim ~/.klei/DoNotStarveTogether/Cluster_1/adminlist.txt
```

输入之前我们复制的 Klei 用户 id，保存退出即可。

## 配置模组

虽然我们的存档里有模组，但是我们还需要在游戏里面修改配置文件，这样在云服务器端运行时才会加载。加载配置文件名为 `dedicated_server_mods_setup.lua`。默认在 `~/dstserver/mods` 里面，如果没有，可以使用 `find` 命令查找一下。编辑该文件：

```shell
vim ~/dstserver/mods/dedicated_server_mods_setup.lua
```

在该文件中，按如下格式每行填入一个刚才导入的模组的 ID。

```lua
ServerModSetup("对应的模组ID")
ServerModSetup("对应的模组ID")
....
```

模组的 ID 可以根据该模组的创意工坊链接获得，也可以直接从之前的本地存档文件夹中，`Master` 或 `Cave` 文件夹下的 `modoverrides.lua` 中找到。文件中的 `"workshop-xxxxx"` 的 `xxxxx` 就是模组 ID。

## 上传存档

使用 XFtp ，创建的 `steam` 用户登录服务器，进入 `~/.klei` 文件夹，然后将刚才创建世界的存档文件夹中的内容整个复制到服务器的 `~/.klei/Cluster_1` 文件夹下，确保存档文件覆盖了默认配置文件。



# 运行世界

## 配置一键运行脚本

建议在用户目录下创建运行脚本，随后可以利用该脚本一键运行。

创建 `rundst.sh` 文件：

```shell
vim ~/rundst.sh
```

填入 [脚本内容](https://gist.githubusercontent.com/MarioZZJ/f4b6d55fe6fdd494cf425f819cc51f09/raw/a6811bdd443cc988f91fb7f864892603d9b935bb/rundst.sh)：

```sh
#!/bin/bash

steamcmd_dir="$HOME/steamcmd"
install_dir="$HOME/dstserver"
cluster_name="Cluster_1"
dontstarve_dir="$HOME/.klei/DoNotStarveTogether"

function fail()
{
        echo Error: "$@" >&2
        exit 1
}

function check_for_file()
{
    if [ ! -e "$1" ]; then
            fail "Missing file: $1"
    fi
}

cd "$steamcmd_dir" || fail "Missing $steamcmd_dir directory!" # TODO

check_for_file "steamcmd.sh"
check_for_file "$dontstarve_dir/$cluster_name/cluster.ini"
check_for_file "$dontstarve_dir/$cluster_name/cluster_token.txt"
check_for_file "$dontstarve_dir/$cluster_name/Master/server.ini"
check_for_file "$dontstarve_dir/$cluster_name/Caves/server.ini"

./steamcmd.sh +force_install_dir "$install_dir" +login anonymous +app_update 343050 +quit

check_for_file "$install_dir/bin"

cd "$install_dir/bin" || fail 

run_shared=(./dontstarve_dedicated_server_nullrenderer)
run_shared+=(-console)
run_shared+=(-cluster "$cluster_name")
run_shared+=(-monitor_parent_process $$)
run_shared+=(-shard)

"${run_shared[@]}" Caves  | sed 's/^/Caves:  /' &
"${run_shared[@]}" Master | sed 's/^/Master: /'
```

保存关闭后，赋予脚本执行权限：

```shell
chmod u+x ~/rundst.sh
```



## 使用 tmux 运行联机脚本

上一步的脚本执行后即可一键运行服务器。考虑到我们的远程终端可能会在未来退出，为保持 session 以使得脚本可以在我们退出后后台持续运行，可以使用 tmux 后台运行脚本。

Ubuntu 20.04 自带了 tmux，无需安装，我们可以直接创建一个会话：

```shell
tmux new -s dontstarve
```

进入 tmux 界面后，运行命令：

```shell
~/rundst.sh
```

![tmux 下脚本稳定运行界面](https://download.mariozzj.cn/img/picgo/202201151247136.png)

待稳定运行后，即可暂挂该会话，回到正常终端。操作是依次按下 `Ctrl+B` 、`D`。



**脚本稳定运行创建世界后，即可喊上小伙伴，在游戏选单界面选择【加入游戏】，搜索房间名加入了。**

![查找房间](https://download.mariozzj.cn/img/picgo/202201151246311.png)

之后如果要回到该会话，可以使用 `tmux attach -t dontstarve` 进行操作。



![快乐游戏](https://download.mariozzj.cn/img/picgo/202201151248430.png)
