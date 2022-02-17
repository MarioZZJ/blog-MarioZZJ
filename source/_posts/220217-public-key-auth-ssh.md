---
title: 玩转服务器 | 配置 SSH 免密登录
comments: true
copyright: true
abbrlink: 5c25bf34
tags:
  - 云服务器应用
  - SSH
categories: 玩转服务器
keywords:
  - 云服务器
  - SSH
description: 完成配置，无需密码，一键远程连接
top_img: 'https://download.mariozzj.cn/img/picgo/202202171403151.png'
cover: 'https://download.mariozzj.cn/img/picgo/202202171403151.png'
date: 2022-02-17 13:42:50
updated: 2022-02-17 13:42:50
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
katex:
aside:
---

SSH 即安全外壳协议（Secure Shell），可在不安全的网络环境下提供安全的数据传输。

在首次 SSH 连接服务器时，会弹出提示：

![认证信息](https://download.mariozzj.cn/img/picgo/202202162249170.png)

> The authenticity of host '<主机>' can't be established.
>
> RSA key fingerprint is <指纹>.
>
> Are you sure you want to continue connecting (yes/no/[fingerprint])?  

这是因为我们第一次连接未知的主机，无法确认这个主机的真实性。如果输入 yes，则会将该主机记为可信任服务器，并将主机信息及其公钥指纹添加进 `~/.ssh/known_hosts` 中，后续的连接中则不会再询问。随后进入密码输入和验证阶段。

使用密码验证方式，随后的每一次连接，我们都需要输入密码，利用服务器公钥加密后以密文形式传输至服务器验证。而如果配置免密登录，则可以跳过输入密码，直接验证。配置免密登录的方法如下：

# 生成本地客户端公钥、私钥

打开本地客户端的终端，键入命令：

```shell
ssh-keygen
```

根据提示操作，一般全部回车默认即可。随后默认在 `~/.ssh/` （用户目录下的 `.ssh` 文件夹）下生成公钥、私钥：

```
~/.ssh
   | ---- id_rsa       # 私钥
   | ---- id_rsa.pub   # 公钥
```

将公钥文件中的公钥复制。

# 将公钥上传至服务器

远程连接服务端，进入用户目录下的 `.ssh` 文件夹，编辑（如无则创建） `authorized_keys` 文件：

```shell
cd ~/.ssh/
vim authorized_keys
```

将客户端的 `id_rsa.pub` 中内容写入，保存。再次连接时，可以发现不用输入密码即可连接。



# 原理对比

使用**密码登录**时，客户端使用已获取的服务端公钥对用户输入的密码进行加密，将密文传输至服务端解密、验证；而使用**免密登录**时，收到登录请求的服务端会随机生成一段编码，并使用客户端预先提供的客户端公钥进行加密，并传输至客户端，客户端用客户端私钥解密密文，将解密后的明文发送给服务端验证，免去了输入密码、传输密码的过程。原理如下图：

![密码登录 & 公钥登录原理](https://download.mariozzj.cn/img/picgo/202202162349489.png)

