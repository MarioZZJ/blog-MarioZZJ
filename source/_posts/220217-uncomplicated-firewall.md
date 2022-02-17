---
title: 玩转服务器 | 服务器的简单防火墙管理
comments: true
copyright: true
abbrlink: e0d78514
tags:
  - 云服务器应用
  - UFW
categories: 玩转服务器
keywords:
  - 云服务器
  - 防火墙
  - UFW
description: 简单实现服务器端口控制，管理服务器请求
top_img: 'https://download.mariozzj.cn/img/picgo/202202171407132.png'
cover: 'https://download.mariozzj.cn/img/picgo/202202171407132.png'
date: 2022-02-17 13:43:11
updated: 2022-02-17 13:43:11
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
katex:
aside:
---

# 防火墙、ufw

防火墙可以实现流量控制。服务器的防火墙一般不限制从服务器发出的流量，主要限制其他主机传入的请求。

# 服务商防火墙控制

一般而言，我们管理服务器的防火墙是对端口进行控制。在购买服务器的服务商控制台处，一般都有防火墙/安全组等配置页面，进入防火墙配置页面即可对端口进行控制：

![腾讯云 - 防火墙](https://download.mariozzj.cn/img/picgo/202202171147291.png)

以腾讯云为例，服务商默认为我们开启了基本使用所需端口（备注中说明其用途）。如果我们需要放行端口，点击『添加规则』即可：

![添加规则](https://download.mariozzj.cn/img/picgo/202202171153569.png)

默认情况下，若未经设置，其他的端口都是关闭的。假设我们部署在服务器上的某项目开启了 4000 端口的广播，如果想在外网访问该项目，则需要将防火墙的 4000 端口放行。随后即可通过 <ip地址>:<端口号> 访问。



# UFW 防火墙控制

在之前的情形中，防火墙由服务商控制，每次更改配置需要进入服务商的 web 端控制台。而其实服务器上也有可以单独控制防火墙的手段，下面介绍 ufw 的使用方法。

ufw，即简单防火墙（Uncomplicated FireWall），可通过命令行的方式控制防火墙，是 ubuntu 等系统的默认防火墙程序（其实本质上是 iptables 的简明实现）。首先，**为了实现 ufw 对服务器防火墙规则的单一控制，避免配置冲突，我们在服务商处将所有端口的 TCP、UDP 放行**：

![放行全部 TCP 和 UDP](https://download.mariozzj.cn/img/picgo/202202171203786.png)

随后我们远程连接上服务器，进行 ufw 的配置。ufw 的默认配置文件在 `/etc/default/ufw`。

```shell
$ grep -v '^#\|^$' /etc/default/ufw

IPV6=yes
DEFAULT_INPUT_POLICY="DROP"
DEFAULT_OUTPUT_POLICY="ACCEPT"
DEFAULT_FORWARD_POLICY="DROP"
DEFAULT_APPLICATION_POLICY="SKIP"
MANAGE_BUILTINS=no
IPT_SYSCTL=/etc/ufw/sysctl.conf
IPT_MODULES=""
```

默认的配置文件中，允许了所有的输出（ACCEPT），而对输入则丢弃（DROP），和服务商那边的逻辑类似。

使用 `ufw` 指令可以实现对 ufw 的控制：

```shell
ufw <command>
```

例如，输入 `ufw --help` 可以看到 `ufw` 指令的常见用法：

```shell
$ ufw --help

Usage: ufw COMMAND

Commands:
 enable                          enables the firewall
 disable                         disables the firewall
 default ARG                     set default policy
 logging LEVEL                   set logging to LEVEL
 allow ARGS                      add allow rule
 deny ARGS                       add deny rule
 reject ARGS                     add reject rule
 limit ARGS                      add limit rule
 delete RULE|NUM                 delete RULE
 insert NUM RULE                 insert RULE at NUM
 route RULE                      add route RULE
 route delete RULE|NUM           delete route RULE
 route insert NUM RULE           insert route RULE at NUM
 reload                          reload firewall
 reset                           reset firewall
 status                          show firewall status
 status numbered                 show firewall status as numbered list of RULES
 status verbose                  show verbose firewall status
 show ARG                        show firewall report
 version                         display version information

Application profile commands:
 app list                        list application profiles
 app info PROFILE                show information on PROFILE
 app update PROFILE              update PROFILE
 app default ARG                 set default application policy
```

## 查看 ufw 状态

指令：`ufw status`

```shell
$ sudo ufw status
Status: inactive
```

在默认情况下，ufw 并没有启用（inactive），否则我们 ssh 远程连接的 22 端口也会被屏蔽。在启用状态下，我们可以看到规则信息：端口号、策略、来源。

```shell
$ sudo ufw status
Status: active
To                         Action      From
--                         ------      ----
22                         ALLOW       192.168.0.0/24
9090                       ALLOW       Anywhere
9090 (v6)                  ALLOW       Anywhere (v6)
```



## 启用、禁用、重启 ufw

指令：

* 启用：`ufw enable`
* 禁用：`ufw disable`
* 重启：`ufw reload`

启用后，ufw 即会启用防火墙控制

```shell
$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
```

提示启动行为可能会影响现存 ssh 连接，因为 ssh 所用端口可能未开启。所以还需完善 ufw 规则



## 添加规则

介绍最基本的规则指令：

* 允许规则：`ufw allow <参数>` 。参数中可填端口、协议等
* 阻止规则：`ufw deny <参数>`

```shell
$ sudo ufw allow 22
Rule added
Rule added (v6)
```

提示信息指示对端口 22 添加了两条规则，分别是 ipv4、ipv6 的 22 端口放行。



```shell
$ sudo ufw allow 9000:9090/tcp
Rule added
Rule added (v6)
```

该操作对 9000 至 9090 的所有端口，放行 TCP 协议请求。



执行以上操作后，可查看 ufw 状态：

```shell
$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere
9000:9090/tcp              ALLOW       Anywhere
22 (v6)                    ALLOW       Anywhere (v6)
9000:9090/tcp (v6)         ALLOW       Anywhere (v6)
```



## 删除规则

指令：

* `ufw delete <规则/编号>`

在删除规则之前，可以通过 `ufw status numbered` 查看带编号的规则列表：

```shell
$ sudo ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 22                         ALLOW IN    Anywhere
[ 2] 9000:9090/tcp              ALLOW IN    Anywhere
[ 3] 22 (v6)                    ALLOW IN    Anywhere (v6)
[ 4] 9000:9090/tcp (v6)         ALLOW IN    Anywhere (v6)
```

可以使用指定编号的方式删除规则：

```shell
$ sudo ufw delete 2
Deleting:
 allow 9000:9090/tcp
Proceed with operation (y|n)? y
Rule deleted
```

也可以通过指定规则内容的方式删除规则：

```shell
$ sudo ufw delete allow 9000:9090/tcp
Rule deleted
Rule deleted (v6)
```

