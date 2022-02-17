---
title: 玩转服务器 | 购置自己的第一台服务器
comments: true
copyright: true
abbrlink: 75521d8e
date: 2022-02-16 13:41:53
updated: 2022-02-17 13:41:53
tags:
    - 云服务器应用
categories: 玩转服务器
keywords:
    - 云服务器
description: 在购买自己的第一台服务器前，需要了解的一点点知识
top_img: https://download.mariozzj.cn/img/picgo/202202171348754.png
cover: https://download.mariozzj.cn/img/picgo/202202171348754.png
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
katex:
aside:
---

# 什么是买服务器？

事实上，我们不是『买』服务器，而是『租赁』服务器。我们在服务商处购买的服务器大多是按时间支付的。云服务器可以理解为在云端的一台电脑主机，服务商会提供给你这个电脑的 **IP 地址**，以便你能够找到他，同时服务商也会在他的**控制台**面板给你提供一些信息和操作选项，让你后续能够操作这台电脑主机。



# 选定服务商

就国内而言，知名的服务商有 [阿里云](https://www.aliyun.com/)、[腾讯云](https://cloud.tencent.com/)、[华为云](https://www.huaweicloud.com/) 等，服务器定价类似。也有一些小型服务商，价格则偏向实惠。我的建议是为了稳定的服务支持、更全面的配套体验、更丰富的控制台功能，**尽量选择主流的服务商**。主流服务商一般都有**新用户福利**，可以免费获取 7 天至一个月的云服务器或轻量应用服务器，新手可以都白嫖个遍，体验一下哪一家的服务和生态协同更适合自己，再决定购买。

我体验过阿里云、腾讯云、华为云，我觉得都够用，但综合体验下来最终选择腾讯云。如果感兴趣可以点击 [邀请链接](https://cloud.tencent.com/act/cps/redirect?redirect=10488&cps_key=8a3d303f847d392194cd29c1311632f4&from=activity) 支持一波~

如果目前还是学生身份，建议在服务商处进行学生认证，认证后购买学生专属服务器可以享受较低价格。如腾讯云推出的 [云+校园](https://cloud.tencent.com/act/campus?utm_source=qcloud&utm_medium=navigation&utm_campaign=campus)。而且结合服务商的促销活动也可以对服务器进行配置升级等，可以按需选择。



# 购置服务器

## 配置选择

出于学习或简单应用搭建目的，购置普通的云服务器或者轻量应用服务器即可。配置方面，一般 1 核 CPU 2G 内存的配置足够个人使用，可以根据需求按需购买。下面贴出我的主力服务器的配置供参考：

| 产品类型 | 腾讯云轻量应用服务器 TencentCloud Lighthouse        |
| -------- | --------------------------------------------------- |
| CPU      | 2 核（2x Intel(R) Xeon(R) Gold 6148 CPU @ 2.40GHz） |
| 内存     | 4GB                                                 |
| 硬盘     | 80GB SSD                                            |
| 带宽     | 8Mbps                                               |
| 地域     | 北京三区                                            |



## 系统镜像安装

一般在购置服务器时即需要选定系统镜像，一般建议选用 Linux 系统，理由不赘述。Linux 系统具有很多发行版本，如 CentOS、Ubuntu、Debian 等都是日下使用较为广泛的系统，这里建议选用带有用户界面的 Ubuntu 系统作为系统镜像。

购置服务器后，在服务商控制台处也可以对服务器进行系统重装。如果使用服务器的目的比较纯粹，也可以直接安装应用镜像，或者 Windows 等可以直接远程桌面操作。

![系统镜像示例](https://download.mariozzj.cn/img/picgo/202202151614878.png)



## 重置凭据

下单购买服务器，待服务商对服务器实例完成配置，实例正常运行时，说明我们的服务器已经启用了。一般情况下，我们会在自己的常用设备上远程控制服务器，而不是通过浏览器借由服务商访问。一般来说，远程连接服务器需要主机、用户名、密码等凭据：**主机**即控制台页面提供给我们的公网 IP 地址，服务商安装系统时已默认在系统中创建管理员账户，而密码不会提供，此时需要在控制台处重置密码为我们指定的密码：

![服务商控制台页面](https://download.mariozzj.cn/img/picgo/202202151643941.png)

Linux 系统默认管理员用户名为 root，而 Ubuntu 的管理员用户为 ubuntu。以上，根据公网 IP、用户名、密码，我们便可以远程连接服务器了。



# SSH 远程连接服务器

SSH 访问是通过命令行方式访问并操作服务器。

连接之前，须确认本地计算机与实例之间的网络连通正常，以及实例的防火墙已放行 22 端口。

打开本地计算机终端（如 Windows 的 Powershell，MacOS 的 Terminal），执行以下命令：

```shell
ssh <username>@<IP address or domain name>
```

`@` 前后分别填入用户名和 IP 地址，按回车连接上后，如果是首次连接，会提示是否接受服务器的 ssh 密钥，输入 `yes` 即可；随后系统会提示输入远程服务器密码，输入刚才重置时输入的密码，回车即可。

![终端远程连接](https://download.mariozzj.cn/img/picgo/202202161620288.png)

登录成功后，即可使用一系列 Linux 命令对服务器进行操作。

至此，已实现我们这台服务器的购置和远程连接。

