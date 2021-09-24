---
title: 使用 Hexo 搭建属于自己的博客
comments: true
copyright: true
abbrlink: 569080df
tags:
  - Hexo.js
  - Github
categories:
  - 工具干货
keywords:
  - 博客建设
  - Hexo
description: 不用服务器，不用会代码，借助 Github Pages 和简单的配置即可建设自己的博客！
top_img: 'https://download.mariozzj.cn/img/picgo/20210923171646.png'
cover: 'https://download.mariozzj.cn/img/picgo/20210923171646.png'
katex: true
aside: true
date: 2021-09-23 12:20:23
updated: 
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
---

> 原文地址：https://blog.mariozzj.cn/posts/569080df/

# 前言

## 为什么要建博客

其实我很早就有了建博客的想法，当时刚学会开发简单的 Web 应用，在网上浏览时发现很多人建议建一个博客练手，便想着要去跟风去写。后来，随着个人知识面的拓展和深入，发现有太多的知识和经验值得沉淀和交流，且**很多复杂性的知识是“一言难尽”的，可能需要成百上千字的描述，辅之以图片视频等媒体**。

另外，根据“学习金字塔”理论（Edgar Dale，1946），**对知识进行演示、实践和传授能够显著提升学习保持率**，而沉淀为博客或视频正是这样一种形式，我认为一方面可以让无形的知识转为有形的博客，便于之后复习和追溯，另一方面通过沉淀转化可以提高我对知识的记忆和掌握水平。

## 建设原理

既然是内容平台，博客自然是重内容，轻形式，主要重心点放在内容上，仅保留少量的交流功能即可。

这里我使用 [Hexo](https://hexo.io/) 框架搭建博客，该框架基于 [Node.js](https://nodejs.org/zh-cn/) ，使用者不需要掌握 node 原理，Hexo 会根据配置项中预设好的模板自动将内容转换为静态页面。

Hexo 提供了多种形式的主题：[Themes | Hexo](https://hexo.io/themes/)，这里我选用的是 [Butterfly](https://butterfly.js.org/)。该主题较为贴近我对博客美观性的要求，且原生支持一系列配置项和插件，能满足我的需要。

虽然我有自己的服务器，但是为了提高其可用性，我还是使用了可以白嫖的 [GitHub Pages](https://pages.github.com/) 服务，将 Hexo 生成的静态页面部署上去，从而可以让大家访问。对于评论、说说等功能插件还用到了 [Artitalk](https://artitalk.js.org/)、[Waline](https://waline.js.org/)、[Vercel](https://vercel.com/) 和 [LeanCloud 国际版](https://leancloud.app/)，有兴趣可自行了解，本文不赘述。



# 本地建设

## 基础环境安装

### 安装 Node.js 

使用我的方法建设博客，最核心的框架是 Hexo，其基于 Node.js 环境。如果没有 Node 环境需要自行安装，在 [Node.js 官方下载页面](https://nodejs.org/zh-cn/download/) 根据操作系统下载即可，具体操作可以参考 [windows安装npm教程--nodejs - 遥望那月 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jianguo221/p/11487532.html)。

这里为和我使用的一些插件兼容，我使用的是 `12.18.0` 版本。该版本自带 npm。安装完成后在终端运行如下命令：

```bash
$ node -v
$ npm  -v
```

如果正常响应对应版本号，则说明安装及环境变量配置成功。

### 安装 Hexo

使用 npm（node 的包管理器，可以类比 python 的 pip）可以直接安装 Hexo。在终端输入如下命令：

```bash
$ npm install hexo
```

即可安装。终端运行 `hexo -v` 检查是否安装正常。

安装后就可以开始配置了，首先，选择一个本地文件夹作为博客的目录，终端切换到该目录下，运行如下命令初始化该目录：

```bash
$ hexo init .
$ npm install
```

成功运行后，即可使用 hexo 的命令对该目录进行操作了。默认情况下，hexo 使用 landscape 主题，运行

```bash
$ hexo server
```

即可从本地运行博客服务器。浏览器打开终端提示的页面（默认为 `https://localhost:4000/`），即可查看。

![Hexo-landscape demo](https://download.mariozzj.cn/img/picgo/20210923150624.png)

默认页面为内置的 `hello-world.md` 文档。至此我们已经完成了 Hexo 的安装，如要了解更多配置项可参考[Configuration | Hexo](https://hexo.io/docs/configuration)，要了解更多 Hexo 命令可参考 [Commands | Hexo](https://hexo.io/docs/commands)。



## 主题配置：以 Butterfly 为例

大部分人可能都会嫌默认主题 landscape 丑，所以可以进入 Hexo 的主题页面：[Themes | Hexo](https://hexo.io/themes/) 挑选符合自己需求的主题进行进一步配置。这里我选用了 Butterfly。作者 [jerryc127 (Jerry Wong)](https://github.com/jerryc127) 在主题页面给出了详尽的文档，参见： [Butterfly - A Simple and Card UI Design theme for Hexo](https://butterfly.js.org/)。

实际上，大部分的操作都只需要理解 `_config.yml` 里面的配置项，不需要掌握任何的代码原理。按照作者文档给出的步骤一步步按需进行修改即可，对于非必要参数也可以选择不配置。

作者建议大多数更改都在主题配置文档 `_config_butterfly.yml` 中进行，但是有一些插件只会识别 `_config.yml` 中的配置，建议自行做好备份。

* [Butterfly 安裝文檔(一) 快速開始 | Butterfly](https://butterfly.js.org/posts/21cfbf15/)
* [Butterfly 安裝文檔(二) 主題頁面 | Butterfly](https://butterfly.js.org/posts/dc584b87/)
* [Butterfly 安裝文檔(三) 主題配置-1 | Butterfly](https://butterfly.js.org/posts/4aa8abbe/)
* [Butterfly 安裝文檔(四) 主題配置-2 | Butterfly](https://butterfly.js.org/posts/ceeb73f/)

完成这几篇文档里面的配置之后，就可以正常使用博客了！建议牢记以下 hexo 命令：

* `hexo clean` —— 清空之前已生成的全部静态文件
* `hexo generate` —— 根据配置生成新的静态文件，`.md` 文档会在这一步自动解析生成静态页面。
* `hexo server` —— 在本地服务器启动 hexo 服务。

关于 Butterfly 的进阶教程查看：[Butterfly 安裝文檔(六) 進階教程 | Butterfly](https://butterfly.js.org/posts/4073eda/)

如在 Butterfly 配置项中存在疑问，可查看：[Butterfly 安裝文檔(五) 主題問答 | Butterfly](https://butterfly.js.org/posts/98d20436/)



## 推荐插件

### 评论系统：Waline

在 Butterfly 文档 [Butterfly 安裝文檔(四) 主題配置-2 | Butterfly](https://butterfly.js.org/posts/ceeb73f/) 中作者介绍了很多评论系统，这里我使用过一部分，最后决定使用的是 Waline。我这里也为使用过的系统写一些评价：

* Disquejs：要翻墙
* livere（来必力）：韩国的，这个一开始吸引到我是因为他可以用国内常见社交平台登录，这样有人评论我也可以推理一下是谁，但是后面发现他会往我的页面里面插乱七八糟的广告（是的也有你们想象的那种），有时登录体验也很差，就放弃了。
* Gitalk：这个用的人蛮多，原理是基于 Github 的 Issue，但是考虑到我的读者不一定都有 Github 账号，且有时候 Github 会被墙，就放弃了。
* Valine：衍生为 Waline 了
* Waline：虽然不支持第三方登录，但是注册只要随便填信息就行，还不用验证，也可以不留信息评论，这样也很方便交流。另外，结合 Leancloud 我可以对评论设置邮箱推送，这样有了新评论我也能第一时间看到，很方便，在 Leancloud 也可以维护掉不友好的评论，完全满足我的需求。

这里 Butterfly 作者文档写的不是很详细，我补充说明一些。简单来说，首先，在主题配置文件中寻找 `comments` 配置项，修改为：

```yaml
comments:
  # Up to two comments system, the first will be shown as default
  # Choose: Disqus/Disqusjs/Livere/Gitalk/Valine/Waline/Utterances/Facebook Comments/Twikoo
  use:
    - Waline # 这里启用 Waline，注意大小写
  text: true # 双评论系统展示评论系统名
  # lazyload: The comment system will be load when comment element enters the browser's viewport.
  # If you set it to true, the comment count will be invalid
  lazyload: true # 懒加载，用户滚动到评论处时才加载评论
  count: true # Display comment count in top_img
  card_post_count: false # Display comment count in Home Page
```

然后按照 Waline 的说明文档：[快速上手 | Waline](https://waline.js.org/guide/get-started.html)，完成 LeanCloud 设置 和 Vercel 部署两步，复制下来 Vercel 提供的 URL。最后在主题配置文件末尾增加以下配置项：

```YAML
waline:
  serverURL:  # 这里填写 Vercel 提供的 URL
  avatar: monsterid # gravatar style https://zh-tw.gravatar.com/site/implement/images/#default-image
  avatarCDN: # Gravatar CDN baseURL
  bg: # waline background
  visitor: false
  option:
```

即可启用。另外特别推荐 Waline 的评论通知功能，参照 [评论通知 | Waline](https://waline.js.org/guide/server/notification.html#邮件通知) ，在 Vercel 完成参数（`SMTP_SERVICE`、`SMTP_USER`、`SMTP_PASS`、`SMTP_NAME`、`SMTP_URL`、`AUTHOR_EMAIL`）的配置就可以了。这里我使用 Outlook365 的邮箱配置成功。这个页面还提供了微信通知和 QQ 通知，但是我测试了下提供服务的三方不是很稳定，且会影响到邮件的正常发送，所以我没有启用。



### 说说系统：Artitalk

这个插件在 Butterfly 进阶教程文档：[Butterfly 安裝文檔(六) 進階教程 | Butterfly](https://butterfly.js.org/posts/4073eda/#Artitalk) 有提及。

说说系统更像是一个只属于自己的留言板，我可以在上面发表一些想法，但是设置好权限之后只有自己能发，别人不能回复。

![Artitalk 在页面中的详情](https://download.mariozzj.cn/img/picgo/20210923154050.png)



### 豆瓣爬虫：hexo-douban

这个插件在 Butterfly 进阶教程文档中有提及：[Butterfly 安裝文檔(六) 進階教程 | Butterfly](https://butterfly.js.org/posts/4073eda/#電影) 

这个插件可以在每次生成静态页面之前，运行一个豆瓣爬虫，爬取你指定的豆瓣账户的电影、书籍、游戏记录，并生成静态页面。这个插件已经很久没有维护，所以需要降级到 node `v12.18.0` 版本才可以正常使用。

每次运行 `hexo douban` 即可运行爬虫并生成静态页面。

![豆瓣列表在页面中的详情](https://download.mariozzj.cn/img/picgo/20210923154308.png)



# 在线发布

## 配置 Github 仓库

在 Github 上创建好仓库，并在本地设置好博客目录，准备发布。

在 Github 仓库的设置页面选择 Pages 选项卡即可对页面进行配置。一般来说，如果仓库名称为 {用户名}.github.io 会自动完成配置，对应的页面部署域名为 `https://{用户名}.github.io`。这里我用了自己的域名，我在服务商那边将域名解析到我的 Github 页面（`https://mariozzj.github.io`），再在这里设置，系统就会自动在目录处生成一个 `CNAME` 文件，之后可以使用原域名直接访问。

![Github Pages 设置页面](https://download.mariozzj.cn/img/picgo/20210923161436.png)



## 方法一：使用 Hexo 部署

Hexo 官方文档给出了一种一步部署的方法：[One-Command Deployment | Hexo](https://hexo.io/docs/one-command-deployment) 

首先需要安装 `hexo-deployer-git` ，在终端运行如下命令：

```bash
$ npm install hexo-deployer-git --save
```

再在 `_config.yml` 中配置好自己的 git 仓库相关设置：

```yaml
deploy:
  type: git
  repo: <repository url> # Git 仓库链接
  branch: [branch] # 分支
  message: [message] # git commit 信息。如	Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}
```

随后，每次完成页面时，运行 `hexo clean` 之后直接运行

```bash
$ hexo deploy
```

即可完成一系列上传操作。



## 方法二：使用 Github Action 部署（推荐）

我更推荐这一种，因为我对博客的修改实际上是对原文档和配置文件的修改。如果使用 `hexo deploy`，那么在 Github 上看到的 commit 记录都是对静态页面 `HTML` 的修改，实际没有参考价值（当然，也可以对源单独设一个分支，但是感觉比较麻烦）

Github Action 配置后，可以根据行动（如`push`）自动执行持续集成、持续部署操作（CI/CD），Github 会在云端服务器执行配置好的脚本。这里就是 Github Action 替我们执行 `hexo clean`、`hexo generate`、`git commit`、`git push`等操作，我们只需把代码推送到源文件分支即可。



### 配置 Github Secrets

不是任何一台云服务器都可以直接向我们的仓库推送代码，需要“登录验证”。这里我们需要生成 Access Token，让 Github Action “登录”并推送到仓库以部署网页。

登录 Github，进入个人账户的 [Settings - Developer Settings - Personal Access Tokens](https://github.com/settings/tokens)，点击 `Generate new token` 生成新的 token，注意权限配置添加读写相关权限，将生成的 token 复制下来。

![Access Token 生成页面](https://download.mariozzj.cn/img/picgo/20210923172557.png)

随后，进入项目仓库的 [Settings - Secrets - Actions](https://github.com/#username/#reponame/settings/secrets/actions)，点击 `new repository secret` 添加以下两个参数：

* `GH_TOKEN`：填写上一步生成的 token
* `GIT_EMAIL`：填写你的用户对应 git 使用的电子邮箱



### 配置工作流（workflow）

工作流是一次持续集成的过程，由多个构建任务（job）组成，一个构建任务由多个步骤（step）组成，每个步骤包含多个 action。

在博客所在目录下新建 `.github` 文件夹，在该文件夹下新建 `workflow` 文件夹，并在其中创建 `deployment.yml`，内容如下：

```yaml
name: Deployment ### workflow

on:
  push:
    branches: [hexo] # 当推送到 hexo 分支时触发 workflow

jobs:
  hexo-deployment: ### jobs
    runs-on: ubuntu-latest
    env:
      TZ: Asia/Shanghai

    steps: 
    - name: Checkout source  
      uses: actions/checkout@v2
      with:
        submodules: true

    - name: Setup Node.js 
      uses: actions/setup-node@v1
      with:
        node-version: '12.x'

    - name: Install dependencies & Generate static files ### steps
      run: | ### actions，这里是安装了 hexo 和主题、插件
        node -v
        npm un hexo-renderer-marked --save
        npm un hexo-renderer-kramed --sav
        npm i -g hexo-cli
        npm i hexo-renderer-markdown-it --save
        npm i hexo-theme-butterfly
        npm install @neilsustc/markdown-it-katex hexo-wordcount hexo-butterfly-douban hexo-renderer-pug hexo-renderer-stylus hexo-generator-search hexo-butterfly-artitalk hexo-abbrlink --save
        npm i
        hexo clean
        hexo douban
        hexo g
    - name: Deploy to Github Pages ### 这一步是部署，填写相关信息
      env:
        GIT_NAME: MarioZZJ 
        GIT_EMAIL: ${{ secrets.GIT_EMAIL }}
        REPO: github.com/MarioZZJ/blog-MarioZZJ
        GH_TOKEN: ${{ secrets.GH_TOKEN }}
      run: |
        cd ./public && git init && git add .
        git config --global user.name $GIT_NAME
        git config --global user.email $GIT_EMAIL
        git commit -m "Site deployed by GitHub Actions"
        git config --global init.defaultBranch master
        git push --force --quiet "https://$GH_TOKEN@$REPO" master:master
```

将包含该配置文件的项目推送到远程分支，就完成了 action 的配置。这样，每次 push 到指定分支时触发该工作流，可以在 Github 项目仓库的 Action 选项卡查看具体的配置过程。

![仓库 Action 页面](https://download.mariozzj.cn/img/picgo/20210923180233.png)

![Action 执行详情](https://download.mariozzj.cn/img/picgo/20210923180255.png)



# 想要白嫖

无需代码开发，这可能是建一个自定义博客较为简单的方法了，其中最为关键的一步 **【主题配置】** ，我没有写下来详细过程，而是直接引用了原作者的教程文档，这一步其实会耗费最多的时间。如果连这一步都懒得去配置，~~那可真是无药可救了~~， **我可以给你一个配置好的模板仓库** ！只要你觉得我这个 [博客](https://blog.mariozzj.cn) 看着还可以的话，可以把我配置好的参数直接拿去用！这里只需要修改部分插件的密钥即可，Action 也配置好了只需自行更改 Github Secrets。当然， **本地别忘了安装 node 、hexo 和我用到的插件** （在 package.json 中）

仓库地址：[MarioStudio/blog-MarioZZJ: Personal blog of MarioZZJ. Source in hexo branch, pages in master branch. Deployed by Github Actions.](https://github.com/MarioStudio/blog-MarioZZJ) 

点击 `use this template` 即可在你的账号里面创建一个一样的仓库。

如果觉得喜欢的话，不妨给我的仓库点个 Star！感谢支持
