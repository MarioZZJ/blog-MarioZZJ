---
title: 玩转服务器 | Web 应用部署上线全流程（Ubuntu + Nginx + MySQL + Flask）
comment: true
abbrlink: 7f568f00
date: 2022-02-22 19:30:29
updated: 2022-02-22 19:30:29
tags: 
  - 云服务器应用
  - Web 开发
  - Nginx
categories: 玩转服务器
keywords: 
  - 云服务器
  - Web 开发
  - Nginx
  - UFW
  - Flask
  - 域名解析
  - SSL
desc: 完成 Web 应用开发后，如何将应用发布到服务器，让更多人访问到呢？以 Ubuntu + Nginx + MySQL + Flask 为例，展示应用部署全流程。
cover: https://download.mariozzj.cn/img/picgo/202202221929984.png
katex: false
aside: true
---

你可能听过 LAMP / LNMP，这是搭建动态 Web 应用的常见环境组合，实质上是 Linux（系统）+ Apache / Nginx（Web 服务器） + MySQL（数据库）+ PHP（编程语言）环境的统称，这套组合较为流行，能够组合解决 Web 应用部署问题。

当我们在 PC 上完成 Web 应用的开发，想要利用服务器发布到互联网上供更多人访问时，需要先完成服务器软件环境的准备，大体上就可以按照 LNMP 的思路进行：


# 服务器系统安装：Ubuntu

见 [玩转服务器 | 购置自己的第一台服务器 | MarioZZJ's blog](https://blog.mariozzj.cn/posts/75521d8e/#系统镜像安装)

后续教程也以 Ubuntu 系统为例



# Web 服务器安装：Nginx

这里我们安装 Nginx，后续教程也利用 Nginx 展开

```shell
$ sudo apt install nginx -y
```

安装后，执行 `nginx -v` 查看 Nginx 版本，如正确打印版本则安装成功。

然后设置防火墙，以使得 Nginx 服务可以通过防火墙，这里以 ufw 为例：

```shell
$ sudo ufw allow 'Nginx Full'
```

最后设置开机启动 Nginx：

```shell
$ sudo systemctl enable nginx
```



# 数据库服务安装：MySQL

如果 Web 项目需要用到数据库，在服务器上安装同版本数据库即可，下面以 MySQL 为例：

```shell
$ sudo apt install mysql-server -y
```

安装完成后，查看服务状态检验是否安装成功：

```shell
$ sudo systemctl status mysql

● mysql.service - MySQL Community Server
     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2022-02-19 12:31:43 CST; 3min 28s ago
   Main PID: 592500 (mysqld)
     Status: "Server is operational"
      Tasks: 38 (limit: 4603)
     Memory: 358.1M
     CGroup: /system.slice/mysql.service
             └─592500 /usr/sbin/mysqld
```

随后，我们配置防火墙，让 MySQL 服务通过防火墙：

```shell
$ sudo ufw allow mysql
```

完成后，我们运行 MySQL 的安全脚本，完成安装：

```shell
$ sudo mysql_secure_installation
```

这里主要是一些配置的设置，其中包括密码设置、远程访问许可等。如果允许了远程访问并打开了防火墙端口，后续可以在 PC 上直接使用数据库管理软件远程连接访问。

安装完成后，配置开机启动 MySQL 服务：

```
$ sudo systemctl enable mysql
```



# 编程语言环境安装：Python

服务器应当安装与原 Web 应用开发环境相同的编程语言环境。

## 安装语言环境

这里以 Python 为例完成安装，Ubuntu 安装时自带 Python，如需安装可执行

```shell
$ sudo apt install python
```

## 安装项目依赖

在本地项目根目录打开终端，键入命令，生成 `requirements.txt` 依赖文件：

```shell
$ pip freeze > ./requirements.txt
```

`requirements.txt` 的形式如下，揭示项目需要依赖的第三方库及其版本。

随后我们可以将整个项目复制到服务器我们指定的目录下，可以用 Git、XFtp、SCP 等方式，在服务器的项目目录下执行命令，可按照 `requirements.txt` 安装依赖的包：

```shell
$ pip install -r ./requirements.txt
```



# 服务启动：Flask

## 启动 Web 应用服务：调试方式

以 Python 常用 Web 框架 Flask 为例，在本地调试环境下，我们一般通过运行根目录下的 `app.py` 启动服务。在服务器这边，我们也可以使用命令启动：

```shell
$ python app.py
 * Serving Flask app 'app' (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

根据提示，服务在服务器上是成功启动了。但是要想通过像在本地调试一样，在互联网上发布 Web 应用并通过公网访问，还需要几步调整。



## 监听外部网络 IP

根据提示输出 ` Running on http://127.0.0.1:5000/` 可知，Flask 服务仅仅可以在本地服务器地址 127.0.0.1 访问。当我们开发项目调试时，访问项目的就是本机，本机能够正常访问；而目前这个状态下，只有服务器自己能访问，其他设备作为外网设备并不能访问服务。解决的方法是修改对应的 `host` 参数，将其指定为 `0.0.0.0`，这样系统就会监听任意 IP 地址而不仅仅是本机。

体现在 Flask 框架脚本代码中，修改 `run()` 方法，将

```python
app.run()
```

修改为

```python
app.run(host='0.0.0.0')
```



## 防火墙放行端口

目前我们还没有绑定域名，需要通过服务启动时指定的端口访问，而服务器防火墙不一定放行了指定的端口。以 ufw 为例，放行该项目的 `5000` 端口，执行下述命令即可：

```shell
$ sudo ufw delete allow 5000
Rule deleted
Rule deleted (v6)
```

放行端口，监听所有 IP 均设置好后，再次启动项目，我们就可以在浏览器中输入 `http://<ip地址>:<端口号>` 访问项目：

![公网访问项目](https://download.mariozzj.cn/img/picgo/202202210019746.png)



## Gunicorn 启动

最开始的提示信息中，有两行这样的信息：

> WARNING: This is a development server. 
>
> Do not use it in a production deployment.  Use a production WSGI server instead.

意思是这样的启动方式仅供调试使用，生产环境下，建议使用 WSGI 服务。这是因为 Flask 自带的 WSGI 性能一般，只用于调试（但是足够应付少量访问），我们可以使用 Gunicorn 方式启动服务。

首先安装 `gunicorn`：

```shell
$ pip install gunicorn
```

然后使用 gunicorn 命令启动，首先了解 gunicorn 命令常用参数：

```
-c CONFIG, --config=CONFIG
# 设定配置文件。
-b BIND, --bind=BIND
# 设定服务需要绑定的端口。建议使用HOST:PORT。
-w WORKERS, --workers=WORKERS
# 设置工作进程数。建议服务器每一个核心可以设置2-4个。
-k MODULE
# 选定异步工作方式使用的模块。
```

随后在项目根目录，执行类似下面的命令即可：

```
$ gunicorn -w 3 -b 0.0.0.0:5000 app:app
# 此处app:app中，第一个app为flask项目实例所在的包，第二个app为生成的flask项目实例
```



## 后台启动、开机自启

事实上，远程通过上述方式启动的服务进程，需要保持远程终端连接，否则当连接断开（长时间未交互、网络不通畅等情况），进程可能被终止，Web 应用无法提供持续性服务。

所以，如果我们想让我们的 Web 应用稳定地在服务器上运行，应当使用后台运行的方式。具体方法很多，如 `nohup`、`screen`、`tmux` 等命令。这里以 `nohup` 为例，在命令前添加 `nohup` 即可让命令后台执行。为保存运行日志，在命令尾端也添加输出重定向符号 `>`，将原本打印到屏幕的输出重定向至我们指定的文件；最后加 `&` 让我们可以继续与该终端交互：

```shell
$ nohup gunicorn -w 3 -b 0.0.0.0:5000 app:app > runtest.log &
[1] 387135
```

当我们确认 Web 应用在服务器上正常运行，想要之后稳定地提供服务，可以将命令设为开机启动，这样如果服务器出现需要重启的情况，系统重新启动后也会自动启动 Web 应用，不用繁复操作。具体方法是编辑 `/etc/rc.local`，在文件结尾追加需要开机时启动的命令即可

```
$ sudo vim /etc/rc.local
```

需要注意，`rc.local` 执行命令的用户是 `root`，目录不是项目根目录，所以贴入的脚本需要调整，例如，我贴入的是 `nohup gunicorn --chdir /home/mariozzj/github/COS-Viewer -w 6 -b 0.0.0.0:5000 app:app > /home/mariozzj/github/COS-Viewer/runtest.log &`（更改了执行地址），同时 `root` 账户安装了对应的依赖。

完成之后，每次重启服务器，我们都无需单独去启动 Web 项目，服务器会根据 `rc.local` 开机自动执行脚本。



# 绑定域名

完成上述步骤，只要拥有 ip 地址、端口号，网络上的其他人就能够使用我们 Web 应用提供的服务了。但是要求其他人记住无规则的 ip 地址和端口号较为困难，所以我们还可以将一个容易识记的域名绑定到我们的服务上来，让其他人仅输入域名即可访问我们的服务。

## 购买域名

我们可以前往我们购买云服务器的服务商处（也可选择其他服务商）购买域名，以腾讯云为例，进入 [DNSPOD](https://dnspod.cloud.tencent.com/)，搜索我们感兴趣的域名是否被他人注册，如果未被注册即可购买域名。

![域名认证提示信息](https://download.mariozzj.cn/img/picgo/202202212259040.png)

在我国使用域名需经实名审查，按要求提交实名信息后，等待服务商、管局通知后，待服务商启动域名解析服务，即可进入下一步域名解析。



## 域名解析

域名解析是将拥有的域名或其子域名指向特定的 IP 地址 / 域名地址 / 机构等，向 DNS 登记，这样其他人访问域名时，DNS 就会提供给客户端我们设定好的终点。

进入服务商的域名控制台，添加记录，填写子域名前缀，选择记录类型为 A（指向 IPV4 地址），记录值填入服务器的 IP 地址：

![域名解析记录添加](https://download.mariozzj.cn/img/picgo/202202220038684.png)

操作后，该地址即可指向服务器。

```shell
$ ping test.mariozzj.asia           
Pinging test.mariozzj.asia [88.88.88.88] with 32 bytes of data:
```



# 反向代理：Nginx

完成域名解析后，我们就实现了域名到服务器 IP 的映射。使用 `<域名>:<端口号>` 就可以访问我们的项目。利用反向代理，我们可以让其他人仅通过记忆域名，免填写端口号访问项目（端口为默认端口 80 / 443），这也有利于我们在一台服务器上部署多个项目的情况。

![反向代理](https://download.mariozzj.cn/img/picgo/202202221108764.png)

反向代理简单来说就是启用一个反向代理服务，负责监听一个外来端口，根据客户端提供的信息找出特定的服务端服务提供给客户端，客户端不知道这个服务端服务实际在哪个端口。这里我们以 Nginx 为例设置反向代理：

## 安装 Nginx

执行命令

```shell
$ sudo apt install nginx -y
```

安装后，检查是否安装成功：

```shell
$ sudo nginx -v
nginx version: nginx/1.18.0 (Ubuntu)
```



## 防火墙配置允许 Nginx

使用 ufw，执行命令允许 Nginx 服务通过防火墙：

```shell
$ sudo ufw allow 'Nginx Full'
```

> 可用应用服务为 `Nginx HTTP`、`Nginx HTTPS`、`Nginx Full`，分别对应 80、443、80&443端口服务

检查是否配置成功：

```shell
$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere
Nginx Full                 ALLOW       Anywhere
3306/tcp                   ALLOW       Anywhere
22 (v6)                    ALLOW       Anywhere (v6)
Nginx Full (v6)            ALLOW       Anywhere (v6)
3306/tcp (v6)              ALLOW       Anywhere (v6)
```



## 开机启动 Nginx

执行 start 命令可以在 Nginx 关闭的情况下启动：

```shell
$ sudo systemctl start nginx
```

如果要在开机时自动启动 Nginx，也可用 `systemctl` 配置：

```shell
$ sudo systemctl enable nginx
```

启动 Nginx 后，访问服务器 IP 地址，或对应域名，即可看到 Nginx 默认页面

![Nginx 默认页面](https://download.mariozzj.cn/img/picgo/202202221545509.png)



## 配置反向代理

在 `/etc/nginx/sites-available` 下存放着 Nginx 服务器块配置文件，我们可以新建一个配置文件用于当前 Web 应用的反向代理：

```shell
$ cd /etc/nginx/sites-available
$ sudo touch test.mariozzj.asia # 添加文件
$ sudo vim test.mariozzj.asia   # 编辑文件
```

文件输入以下内容：

```
server{
  listen 80;
  server_name  test.mariozzj.asia;
  index  index.php index.html index.htm;

  location / {
    proxy_pass  http://127.0.0.1:5000; # 转发规则，将来自域名的访问代理至5000端口的应用
    proxy_set_header Host $proxy_host; # 修改转发请求头，使应用可以收到真实的请求
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

保存文件后，我们还需要将这个配置文件软链接到 `/etc/nginx/sites-enabled` 以启用。执行命令：

```shell
$ sudo ln -s /etc/nginx/sites-available/test.mariozzj.asia /etc/nginx/sites-enabled/
```

创建完软链接，为使配置文件生效，我们可以重启服务器，也可以重启 Nginx 服务：

```shell
$ sudo systemctl restart nginx
```

配置生效后，我们就可以不指定端口直接访问我们的 Web 应用了：

![纯域名访问应用](https://download.mariozzj.cn/img/picgo/202202221626565.png)



# 合法化、安全化

## 域名备案

根据国家相关法规的相关要求，使用域名需要备案，如果域名是首次购买且映射到国内的服务器，则需要进行备案，否则随时可能被服务商暂停接入。备案一般只需备案二级域名，即我们购买的主域名，对其子域名无需单独备案。关于备案的相关流程可以查看 [网站备案 首次备案 - 备案资料填写流程 - 文档中心 - 腾讯云 (tencent.com)](https://cloud.tencent.com/document/product/243/37402)。

备案流程完成之后，可以根据回函提示，在网站页面底端添加备案相关信息：

![备案信息](https://download.mariozzj.cn/img/picgo/202202221644105.png)



## SSL 证书申请与部署

你可能留意到，之前访问应用时，浏览器会提示“不安全”，这是因为我们是以 http 方式访问，如果我们换用 https 方式访问，保障 Web 应用安全性的同时（体现在密文传输等），也能使得 Web 应用得到更广支持。

首先，我们可以前往域名服务商控制台，为域名申请证书（也可利用 Certbot 等服务获取部署证书）：

![申请证书](https://download.mariozzj.cn/img/picgo/202202221711069.png)

证书签发后，我们可以在证书列表下载证书，对应 Nginx 的证书文件夹有四个文件，将其中文件名为 `<域名>.key` 代表的私钥文件和文件名为 `<域名>_bundle.crt` 代表的证书文件放在 `/etc/nginx/certs` 目录下（如无 certs 目录可自行创建）

然后，编辑我们在上一节编辑过的配置文件，将其修改为：

```conf
server{
  listen 80;
  listen 443 ssl; # 监听 HTTPS 的 443 端口
  ssl on;
  ssl_certificate /etc/nginx/certs/test.mariozzj.asia_bundle.crt; # 证书地址
  ssl_certificate_key /etc/nginx/certs/test.mariozzj.asia.key;    # 私钥地址
  ssl_session_timeout 5m;
  ssl_protocols TLSv1.2 TLSv1.3;
  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
  ssl_prefer_server_ciphers on;

  server_name  test.mariozzj.asia;
  index  index.php index.html index.htm;

  location / {
    proxy_pass  http://127.0.0.1:5000; # 转发规则
    proxy_set_header Host $proxy_host; # 修改转发请求头，使应用可以收到真实的请求
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

然后重启服务，这样，我们就完成了证书的部署，以 https 方式提供我们的 web 服务了！

