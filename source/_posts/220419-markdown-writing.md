---
title: 让 Markdown 成为写作的起点
comments: true
copyright: true
abbrlink: fd830caf
date: 2022-04-19 13:06:14
updated: 2022-04-19 13:06:14
tags: 
    - Markdown
categories: 工具干货
keywords:
    - Markdown
description: 轻量级标记语言，简约易学，专注内容，无限潜力。
top_img: https://download.mariozzj.cn/img/picgo/202204201006mdhead.png
cover: https://download.mariozzj.cn/img/picgo/202204191318445.png
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
katex: true
aside: true
---

# Markdown 是世上最好的写作语言

Markdown 是一种轻量级标记语言，由 John Gruber 于 2004 年初创，目前已成为世界上最流行的标记语言之一。它轻量小巧，简单易学，同时又具有无限潜能，近两年已成为我的主力写作语言。

说 Markdown 是“世界上最好的语言”，当然是在玩 php 的梗，**适合自己的写作方法才是最好的**。随着我认知的深入，我接触到越来越多的奇淫技巧，发现它在提升我写作效率的同时也具有许多意想不到的应用场景。

略知但不愿意使用 Markdown 来写作的人大概可以分两拨，一类是能够熟练使用 Word 等富文本编辑器，或者是 Latex 大神，认为 Markdown 的应用场景很局限，没法把文档玩出花，根本不够用；另一类是天然地对“编程语言”不感冒，觉得自己肯定学不会，Markdown 是一种语言，那肯定很难学吧？

事实上这两类人群或许都对 Markdown 存在认知误区。对于前者，在写作前我们应该明确写作的目的，**如果我们非常重视产出文本的格式，那我们自然就不会用 Markdown 来写**，而是用更强力的富文本编辑器或写作语言来完成写作；对于后者，其实写 Markdown 根本不是“编程”，因为文档本身不涉及程序编译，只有渲染等过程需要（当然，渲染都是用现成工具，不需要学会），而且 **Markdown 的语法极其简洁**，如果你不需要格式定义，那这个文档里面除了你的内容文字什么都没有。

## 最好的语言：必须易学

Markdown 的语法相当简单，内容还是原原本本的内容，如果需要**格式定义**仅仅需要利用一系列**标点符号**即可，且种类有限：如 `#` 用于定义标题，`*` 用于控制粗体、斜体、列表，`>` 用于插入引用内容，`[]`、`()`、`!` 等用于插入图片和超链接，`$` 用于插入 $\LaTeX$ 公式……可以参考 [Markdown 教程](https://markdown.com.cn/) 进行简要学习。基础内容大约几分钟就可以学会，不需要任何前置知识。

![Markdown 语法：内容+符号](https://download.mariozzj.cn/img/picgo/202204191444092.png)

## 最好的语言：必须够用

Markdown 介于富文本和纯文本之间，它是纯文本的形式，却能够在渲染后呈现接近富文本的样式。Markdown 支持的基本格式已经基本足够用于**做笔记、形成知识库、发布博文**等，例如 Github Repo以及 Issue、Pull Request 等就支持 Markdown 样式，简书、知乎等平台也支持 Markdown 写文档、回答。而且得益于 Markdown 文档二进制文件特性，利用 Git 等可以进行**版本管理**，回溯每一版本的内容。

实际上，Markdown 本身只包含两部分信息：内容和标记格式。但标记格式其实是一类内容，类似于 HTML 中的 `class`，但是这类内容最终如何呈现，取决于渲染器中的选项。因此，如果 Markdown 语法能满足最终需要定义的格式类型需求，完全可以**通过调整模板/配置文件来实现更加细节的格式输出**（如颜色、字号、间距、对齐等在 Markdown 中无法定义的细节）。不同于 Word、LaTeX 是基于页面的写作，Markdown 是基于网页的写作，所以大多数的渲染器都支持 HTML 嵌入，所以也可以通过**插入 HTML 的方式来实现部分细节排版**（不推荐大量使用，这有悖于使用 Markdown 写作的初衷）。所以可以说，Markdown 能实现的排版格式在不同程度上都是够用的，后文还会继续介绍一些方法。

![Markdown 流](https://download.mariozzj.cn/img/picgo/202204191702600.jpg)

## 最好的语言：专注内容

在未接触到 Markdown 前，我一般习惯用 Microsoft Word 等富文本编辑器用于大部分的文本编辑场景。但是用久了会发现，我在写作的过程中，**很大一部分时间并不是在做知识的输入输出，而是在不断地调整格式**。尽管在我大部分的工作流中，一般会在最后一步调整格式并校对，但是仍然避免不了写作中停止我的思考，并且开始玩编辑器控件；另外，在部分场景下，我最终的输出结果并不一定是页面形式（打印等），Word 等编辑器基于页面的文档编辑逻辑会分走我的部分精力。

> 这个表格怎么错页了？
> 
> 这个图片怎么这么大？
> 
> 这个字体好难看啊！
> 
> 这里怎么换行了？？

实际上，大部分的形式问题在我们产出完整的内容之前无关紧要，有时这些形式打断了我们的输出逻辑，对整体成文质量可能是致命的。而 Markdown 因其**纯文本的编辑方式**，在一定程度上可以**减少我们对格式的关注，专注于内容本身**。而相比于简单的纯文本，Markdown 的基础语法也能够保证我们的行文有基本逻辑（可以用标题控制大纲、有序列表展示条目等）。

## 小结

到这里，我觉得可以用 [Markdown Guide](https://www.markdownguide.org/) 中的部分描述总结 Markdown 语言的优势。

**Markdown can be used for everything./ Markdown is everywhere.** 用途广泛，网站、知识库、笔记、技术文档等都可见 Markdown 的身影。

**Markdown is portable. /Markdown is future proof.** 二进制纯文本文件，不需要特定的编辑器或平台也可以打开，便于版本控制。

**Markdown is platform independent.**

另外，Markdown 简单易学，也能让我们回归写作初衷，提升效率。

# 工欲善其事，必先利其器

虽说 Markdown 用普通的文本编辑器就能打开，但是还是有很多编辑器表现优秀，可以提升我们的输入效率，值得推荐。

## Typora

[Typora](https://typora.io/) 应该是知名度最高的 Markdown 编辑器（没有之一），在很长一段的测试期内一直免费使用，广受好评，虽然最近推出正式版后开始收费，但实属可以理解，也可以继续免费用测试版本。

![Typora](https://download.mariozzj.cn/img/picgo/202204191908918.jpg)

Typora 的亮点是所见即所得以及**快捷键支持**，如果能够熟记 Typora 的快捷键操作，我们甚至不需要会 Markdown 语法也可以写 Markdown 文档。例如 <u>`Ctrl` + 数字</u> 为设定标题，<u>`Ctrl` + `b`</u> 为加粗，<u>`Ctrl` + `Shift` + `M`</u> 为插入行间公式等。**所见即所得**是 Typora 会根据选定主题实时渲染 Markdown 文档，可以一边预览一边输入，且预览和输入在同一窗口。

![Typora 所见即所得](https://download.mariozzj.cn/img/picgo/typora.gif)

另外，Typora 还集成了对 Pandoc 的支持，可以直接导出多种格式（包括我们常用的 PDF、docx 等），方便我们将文档发送给他人查看。后文还将具体介绍 Pandoc。

![Typora 丰富的格式导出](https://download.mariozzj.cn/img/picgo/202204191911851.png)

## Visual Studio Code

[VSCode](https://code.visualstudio.com/) 是微软推出的开源轻量级代码编辑器。VSCode 可以**编辑所有的二进制文本文件**，当然也包括 Markdown。这里推荐 VSCode 是因为 VSCode 可以同时编辑所有文本文件（包括文档、代码等）、多窗口支持等特性。VSCode 本身不支持 Markdown 渲染，但是 VSCode 强大就强大在它的**扩展能力**，可以通过插件实现多种功能，如 Markdown 渲染就可以用 [Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) 插件。

![Markdown All in One: 左栏编辑，右栏预览](https://download.mariozzj.cn/img/picgo/202204191444092.png)

## 墨滴MDNice

最后推荐一款在线 Markdown 编辑器——[墨滴](https://editor.mdnice.com/)，这个编辑器同样是左栏编辑，右栏预览，但是其亮点是**生成可复制到微信公众平台/知乎等内容平台的 HTML**，对于坚持写博客的我来说每次都**免去**了不同平台分发内容时的**排版**过程，节省了很多时间。另外它不用安装软件，使用浏览器即可使用，也可以在线导出 pdf。美中不足之处就是排版版式过少，可能需要更多的前端贡献。

![墨滴 在线编辑预览 Markdown](https://download.mariozzj.cn/img/picgo/202204191931464.png)

## 图床

在所有需要写入 Markdown 文档的内容中，图片应该是较难处理的。因为 Markdown 是纯文本，所以插入图片只能通过语法中的引用 `![图片标题](图片链接)` 填入地址。一般情况下，使用 Typora 等编辑器插入图片，会将图片放在本地计算机的某个地方并引用，但是如果我们想让文档分享给他人，就需要将图片一并上传并保证原本的相对文件结构，这样或许会比较麻烦。

现在比较流行的一种做法是将图片全部都放在云上，Markdown 内填入网络地址，渲染时文档预览者实时获取预览图片。这种专门用于存放图片的方式叫做图床或对象存储，优势在于**图片上传方便**，永久保存，**加载图片时仅占用图床服务资源**，并不与博客内容其他资源冲突造成整体加载缓慢的情况。

这里分享我的解决方案：[腾讯云对象存储](https://cloud.tencent.com/product/cos) + [PicGo](https://picgo.github.io/PicGo-Doc/zh/)。其中前者提供图床服务，后者用于图片上传。腾讯云对象存储一般是按用量收费，作为个人用户，我们的用量其实相对较少，每个月的消费可以忽略不计，而且腾讯云的服务相对稳定，可以和绑定在腾讯云的其他资源（CDN、域名）形成配合。选择 PicGo 是因为 Typora 提供了接口支持，可以实现图片复制一键上传重命名；VSCode 也有对应插件提供服务。

# 运用 Pandoc 将文档转换为多种格式

Markdown 的缺点之一就是用的人还不够多，在分享协作方面可能存在困难，电脑里安装了 Markdown 阅读器的人一定比安装了 Word 或者 PDF 阅读器的人少非常多，这也是强大的 LaTeX 的缺点之一。但是 Pandoc 解决了这个问题，可以将我们编辑好的 Markdown 文档进行渲染、转换为其他人可以查看的常用格式。

[Pandoc](https://pandoc.org/) 由 John MacFarlane 开发，是一个强大的全局文档转换器，提供了 标记语言到几乎所有 [常见文档格式的转换](https://pandoc.org/diagram.svgz)（如 HTML、Word、PowerPoint、LaTeX、PDF 等）。这样我们就可以将写完的 Markdown 文档分享给他人阅读。Pandoc 导出时一般需要对样式/模板进行设定，如果确有稳定的文档编辑需求，可以设定好模板便于导出。

## 用 Markdown 写论文

Markdown 可以导出为 docx 格式，并轻松处理引用问题。我的本科毕业论文就是使用 Markdown 完成的，按照导师和学校要求的格式导出为 docx。只需要通过下面一行命令：

```Powershell
pandoc --citeproc --number-sections --csl ./assets/china-national-standard-gb-t-7714-2015-numeric.csl --bibliography ./assets/better_bibtex.bib -M reference-section-title="参考文献" -M link-citations=true --reference-doc ./assets/whu_bsc_dissertation_template_pandoc.docx ./main.md -o ./output/main.docx
```

这里涉及到的部分参数含义如下：
* `--citeproc`：处理文献引用。
* `--number-sections`：导出时对标题进行编号。

这里涉及到五个文件：
* `main.md`：即撰写的 Markdown 文件。
* `main.docx`：即导出的 Docx 文件。
* `whu_bsc_dissertation_template_pandoc.docx`：按照我校毕业论文标准修改的 Pandoc 模板。
* `china-national-standard-gb-t-7714-2015-numeric.csl`：引用文献参考格式文件，使用 Zotero 的可以在 [Zotero 样式仓库](https://www.zotero.org/styles) 下载。
* `better_bibtex.bib`：我参考的文献元信息，Zotero 导出。

模板文件中，对各类内容预先进行了样式定义（见 [Pandoc 手册](https://pandoc.org/MANUAL.html) 中关于 reference-doc 的相关说明），如果没有模板文件可以通过 `pandoc -o custom-reference.docx --print-default-data-file reference.docx` 命令生成默认样式文件后修改。需注意所有的修改要在 Word 的样式库中进行，[仅仅修改模板文档中的内容无效](https://github.com/jgm/pandoc/issues/8009)。

在源 Markdown 文件中，在 Front Matter 中定义好参考文献的存放位置：
```Markdown
---
bibliography: [/assets/better_bibtex.bib]
---
```

随后就可以用 Pandoc 的引用模式插入引文，按照文献元信息中的 CiteKey 按如下形式引用即可（标注单一文献或同时标注多文献均可）
```Markdown
正文正文正文正文 @citekey1 正文正文正文 [ @citekey1; @citekey1 ]
```

随后就可以用最开始的那一行命令生成 docx 文件了。

![Markdown 转换为 docx](https://download.mariozzj.cn/img/picgo/202204192045834.png)

## 用 Markdown 做幻灯片

Markdown 也可以按照定义的格式转换为 Powerpoint 格式，在母版中进行调整，原理与转换为 docx 几乎相同，详见 Pandoc 手册。

```Powershell
pandoc --reference-doc ./custom-reference.pptx ./test.md -o ./output/test.pptx
```

![Markdown 转换为 pptx](https://download.mariozzj.cn/img/picgo/202204192101645.png)

还有一种转换为幻灯片的方法——借助 [reveal.js](https://revealjs.com/) 转换为网页幻灯片。在主机安装 `reveal.js` 后，可以利用以下命令生成网页幻灯片的 HTML：

```Powershell
pandoc ./test.md -o ./slides.html -t revealjs -s
```

![Markdown 转换为 SlidesGo](https://download.mariozzj.cn/img/picgo/md-slidesgo.gif)

Reveal.js 自带控制控件，只需要浏览器就可以打开，因此几乎可以在任何一台电脑上都可以展示。可以查看 [Reveal.js 参数列表](https://revealjs.com/config/) 配置更多参数，进一步修改格式。

----

虽然目前也涌现出越来越多的标记语言，如 RMarkdown、
CommonMark 等，增加了更多特性，但是 Markdown 自诞生之日起就因为其简约易学、专注内容、潜力无限的特性被不少人所接受。现在也可以看到许多富文本编辑器都能引入 Markdown 支持，一定程度上提升了写作效率，希望未来能够看到更多兼容 Markdown 的场景。