---
title: 用Hexo+Vercel搭建属于自己的静态博客
toc: true
tags:
  - 经验分享
  - 博客搭建
categories:
  - 博客搭建
abbrlink: 6697
date: 2026-01-08 00:48:07
---


## 前言

继续用Hexo，Vercel，无需服务器，无需备案，最好要域名，来搭建属于自己的静态博客，上次我们啥也没搭，我们继续搭。

我很久不用qq空间了，很怀念qq空间的日志功能（虽然没用过几次），可以发长文。但是现在支持存放长文的地方太少了，而且自定义功能都不足，不够我捯饬。也想过搞个站，但一直苦于租服务器太贵，也一直没有实施。

直到了解了静态博客，无需服务器也可以搭建一个个人博客。作为一个计算机知识基本为零的小白，也是成功照猫画虎搭出了一个还算能看的个人博客。这里写一篇分享，给各位苦于没有地方存放自己作品的同僚们一个指引。

我自己也特别菜，可能真的大佬看我的文章会觉得挺搞笑的，我只能说确实挺搞笑的orz，若有问题欢迎提出修改建议。

## 这是什么

首先介绍一下标题提到的Hexo和Vercel是个什么东西。

![](https://img.57hmpg.top/file/1767792107566_20260107212132500.png)

Hexo是一个静态博客框架，它可以将markdown文件渲染为网页，十分快速且轻量。说的通俗一点，就是它给了你一个框架，你不需要从头到脚写一个完整的网站，只需要写你的内容，顺便稍微修缮一下这个框架，再给它一个命令，它就可以将你填的文件自己打包好，变为人人可以查看的网页。

经过多年发展，Hexo已经形成了一个较为成熟的社群，官网有上百种用户开发的主题可以选择。

除了Hexo之外，也有其他一些静态网站框架，比如astro和hugo。astro比较新且更新勤快，但是我个人感觉对会前端的用户更友好一些，感兴趣可以去试试，这里选用Hexo是因为稍微比较省事

问题来了，自己精心做的网站怎么让别人看到？



![](https://img.57hmpg.top/file/1767792710183_20260107213138610.png)

Vercel就是一个免费提供静态托管服务的平台，它将我们打包好的网站放到平台上供人们浏览，并且能够快速部署并更新我们的博客。



前面说了Hexo的优点——快速、轻量且免费，但也有些小小的局限性需要注意一下：

- 因为要经过github，所以不可避免的会有网络问题（悲）
- 没有可视化界面，几乎全程都是对着代码编辑器和终端打字，不过习惯一下也还好。也有qexo、Hexohub、Hexo pro这类提供了可视化管理界面的外部工具，个人试用过感觉有一定的局限性
- 用别人的主题还好说，但是要自己写主题的话，框架太老会比较困难



## 事前准备

虽然说是一篇零基础经验分享，但最好还是要做好如下准备：

- 【必须】拥有一个github账号，且可以基本稳定地访问github。如果没有魔法，可以使用watt toolkit。
- 【必须】拥有一个vercel账号，直接使用上面的github账号注册，这样两个号可以关联，vercel可以直接导入你号里的库
- 【必须】一个代码编辑器，本文推荐并使用vscode
- 【可选但强烈建议】自己的域名。虽然托管服务自带一个免费的域名，但是太长又难看，而且国内访问速度极慢，所以强烈建议申请一个属于自己的域名，而且开销跟组服务器相比已经很便宜了。在阿里云和腾讯云都能申请到廉价的.xyz和.top域名，且只申请域名不用备案（但是据说有些地区无法访问未备案的廉价地名），也可以自己寻找免费申请域名的途径。
- 【可选】用于编辑markdown的软件，比如typora。不过直接用vscode就行。
- 一颗敢于折腾的心（），从入门到上线，最快也需要一整天的时间，而且就算网站成功部署上线，后期的维护也会十分费心费力。

另外如果你一边看hexo官方文档一边看本文，会发现两者有很多有出入的地方，因为官方文档太老了，七年前就有人在建议重写。如果不信我，也可以去参考其他人写的教程。



## 环境搭建

Hexo需要事先安装两个程序：Node.js和Git

首先安装[Node.js — 在任何地方运行 JavaScript](https://nodejs.org/zh-cn)

![](https://img.57hmpg.top/file/1767793528379_20260107214517792.png)

运行安装程序，一路点next就行，不用管那些配置项，

然后win+r运行，输入“cmd”回车，打开终端，输入以下代码

```bash
node -v
```

这个命令用于查询版本号，如果弹出来一串数字，就说明安装成功了

-----

然后安装[Git - Git 版本控制系统](https://git-scm.cn/)

![](https://img.57hmpg.top/file/1767793705174_20260107214813460.png)

还是一样，一路next就安装结束了

在终端中验证一下是否安装

```bash
git -v
```

如果显示版本号，就ok了，然后再稍微配置一下。先前让你注册了一个github账号，记住用户名和邮箱，运行以下命令：

```bash
git config --global user.name "此处填写你的Github用户名"
git config --global user.email "此处填写你的Github绑定的邮箱"
```

双引号要保留，两个命令分开运行！

然后环境就算是配置成功了



## 创建Hexo项目

### 安装Hexo

大伙应该在前面Hexo的截图中看到了一串代码，还是在终端中，输入它

```bash
npm install -g hexo-cli
```

然后hexo就在你的电脑里创建好了，是不是很快

npm是node.js默认的包管理器，我这么说可能看不太明白什么意思，可以先理解为：别人在npm库上传了项目，你就可以通过npm install，将它的项目下载下来

如果npm下载速度太慢或者下载失败，可以试着将npm换为国内的淘宝镜像源

```bash
npm config set registry https://registry.npmmirror.com
```



### 创建项目

这里我们在Vercel直接创建项目，这样后期部署可以省很多事

原先可以直接在新建项目时选择hexo模板，但现在被撤下了，不过还是找到了一个入口

![](https://img.57hmpg.top/file/1767798798351_20260107231307293.png)

点击这里就可以用hexo模板创建项目了

![](https://img.57hmpg.top/file/1767798837917_20260107231352017.png)

这里在vercel创建工程的同时，会在你的github里创建一个包含项目文件的库。给库随便起个名，然后点击create

稍等片刻让他自己部署

![](https://img.57hmpg.top/file/1767798933648_20260107231523395.png)

现在就部署成功了

![](https://img.57hmpg.top/file/1767798958931_20260107231545393.png)

下滑点击continue to dashboard，就能看到你部署的网站了，domains下面的链接就是vercel分配给你的免费二级域名，点击就可以访问你的网站

![](https://img.57hmpg.top/file/1767799104092_20260107231816709.png)

但是如果你在没有魔法上网的情况下会发现，你点不开这个网站，因为vercel.app这个域名国内无法访问，而且这么长看着太不习惯了，所以强烈建议申请一个自己的域名。

但是现在博客在人家的平台上，我们要把它想办法拖到本地来编辑修改。点击顶栏的setting，进入git页

![](https://img.57hmpg.top/file/1767799473318_20260107232419037.png)

这个就是和你的hexo项目关联的库，点进去（应该已经登录了github账号），就是你刚才创建的hexo项目了

![](https://img.57hmpg.top/file/1767799555643_20260107232539705.png)

现在要把这个项目克隆到本地。找一个你想放置项目文件夹的地方，右键open git bush here，在终端输入如下命令

```bash
git clone https://github.com/{{用户名}}/{{仓库名称}}.git
```

其实复制库的链接，在后面加个.git就行，稍等一下项目文件夹就克隆好了

如果git无法连接，就试试加速器/设置git代理，后者请自行搜索设置方法

现在就可以进入我们的项目目录一探究竟了，这里我搬运一下[利用Github与Vercel多端部署Hexo博客 | 白衣INK](https://baiyi.ink/posts/d89a0dc3.html#三-Vercel额外部署)对各目录文件的详细解释

```
#假设是E盘：
    └── blog  # Hexo 博客根目录
        ├── _config.yml         # Hexo 全局配置文件，可设置网站标题、副标题、作者、URL、部署方式等信息
        ├── package.json        # 项目依赖和脚本配置文件，记录项目所需的 npm 包和可执行脚本，通过 npm install 安装依赖
        ├── package-lock.json   # 锁定安装时的包的版本号，保证其他人安装相同版本的依赖
        ├── scaffolds           # 模板文件夹
        │   ├── draft.md        # 草稿文章模板，使用 hexo new draft "文章标题" 时基于此创建草稿
        │   ├── page.md         # 页面模板，使用 hexo new page "页面名称" 时基于此创建页面
        │   └── post.md         # 文章模板，使用 hexo new post "文章标题" 时基于此创建文章
        ├── source              # 资源文件夹，存放博客文章、页面、图片等资源
        │   ├── _drafts         # 草稿文件夹，存放未发布的文章草稿
        │   │   └── draft-article.md  # 示例草稿文章
        │   ├── _posts          # 文章文件夹，存放已发布的文章
        │   │   └── published-article.md  # 示例已发布文章
        │   ├── images          # 图片文件夹，存放博客中使用的图片
        │   │   └── example.jpg # 示例图片
        │   └── index.md        # 首页 Markdown 文件，可自定义首页内容
        ├── themes              # 主题文件夹，存放 Hexo 主题
        │   └── next            # 以 Next 主题为例，不同主题名称不同
        │       ├── _config.yml # 主题配置文件，可配置主题外观、功能等
        │       ├── languages   # 语言文件文件夹，包含不同语言的文本配置
        │       ├── layout      # 布局文件夹，存放主题的模板文件（如 Pug、EJS 等）
        │       ├── scripts     # 脚本文件夹，存放主题的自定义脚本
        │       └── source      # 主题资源文件夹，存放主题的 CSS、JavaScript、图片等资源
        ├── node_modules        # 依赖包文件夹，存放项目通过 npm 安装的所有依赖包
        └── public              # 生成的静态文件文件夹，hexo generate 命令将 Markdown 文件转换为 HTML 等静态文件存于此
            ├── index.html      # 博客首页
            ├── archives        # 文章存档页面
            ├── categories      # 分类页面
            ├── tags            # 标签页面
            ├── css             # 样式文件文件夹
            └── js              # JavaScript 文件文件夹
```

先本地部署看一下成不成功

在项目文件夹右键打开bash，先运行npm安装一下依赖

```bash
npm i
```

还是在终端，执行如下命令

```bash
hexo g
```

此为缩写，全称为hexo generate，就是生成打包你的博客的静态网页文件，放在新建的public文件夹里

如果爆了很多warning，不用管，全当是氛围灯，不是error就行（

然后执行

```bash
hexo s
```

全称为hexo server，就是在本地服务器运行你的网站，通常用于部署前测试一下效果

成功弹出一行地址

![](https://img.57hmpg.top/file/1767797119753_20260107224509606.png)

这个就是你的网站在本地服务器的地址，按住ctrl点一下这个链接，就能看到你建立的网站了

![](https://img.57hmpg.top/file/1767797211788_20260107224629067.png)

已经迫不及待力（喜）

看够了就在终端按ctrl+c关闭服务器

## 博客的初步尝试

### vscode的用前准备

这里是给第一次使用vscode的萌新看的，使用过vscode的可以直接跳过了

接下来就要用到代码编辑器了，接下来写文章和配置主题都要用到它，这里我们使用vscode

打开vscode后界面都是英文，我们现在给调成中文。点击侧边栏的extension（或者ctrl+alt+x）

![](https://img.57hmpg.top/file/1767797630916_20260107225336007.png)

搜索chinese，最上面那个应该就是官方的简体中文安装包，install它，然后启用，参照官方的设置方法，然后vscode就变成中文了

![](https://img.57hmpg.top/file/1767797695636_20260107225447937.png)

然后用vscode来打开你的项目目录，这里有几种办法

一个是打开一个空的vscode，将项目文件夹直接拖进去

或者打开项目文件夹，在顶部的路径栏直接输入cmd

![](https://img.57hmpg.top/file/1767797911234_20260107225819177.png)

就可以直接在这个文件夹打开终端，输入如下指令

```bash
code . 
```

注意code和点之间有一个空格！

按下回车，就会自动在vscode打开项目文件夹

当然右键打开git bash输入同样指令也可以。

### 博客配置

首先对网站的信息进行初步的配置。打开_config.yml，参考[配置 | Hexo](https://hexo.io/zh-cn/docs/configuration)来进行更改。这里列出一些最好要改的部分（没讲的部分可看文档自行了解）

网站基本信息：

```yaml
# Site
title: Hexo #网站标题
subtitle: #网站副标题
description:  #网站描述
keywords:  #关键词
author: John Doe #作者名
language: #网站语言，中文为zh-CN
timezone: #时区，其实这个和上面那行可以都不管
```

下面的URL部分我们不用管，Directory是文件存放路径，我们也不要动

```yaml
# Writing
new_post_name: :title.md # 使用hexo new新建文章时，默认的文章标题
default_layout: post #新建文章使用的默认布局，读取scaffolds里的md文件
titlecase: false # 英文标题用的，我们不管
external_link: true # 是否在新选项卡打开外部链接
filename_case: 0 #同样是英文标题用，不管
render_drafts: false #显示草稿
post_asset_folder: false #如果为true，就会在创建文章的同时生成一个同标题的文件夹，可以直接读取里面的图片等素材
relative_link: false 
future: true 
highlight:
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:
```

剩下的就可以先不用管，包括deployment，我们用不上，可以等到后文配置主题再进行设置



### 写文章

我们先写篇文章来助助兴

除了上面两个方式之外，其实在vscode里也能打开终端，在vscode打开项目文件夹后，点击左下角的这一块

![](https://img.57hmpg.top/file/1767798290974_20260107230439467.png)

在弹出的底边栏，选项卡切换到终端，就可以在vscode中直接使用终端，不用在cmd和vscode窗口中来回切换了，我们接下来的大部分时间都要对着这个vscode

在终端中输入以下命令来创建文章

```bash
hexo new "你的文章标题"
```

回车一下你的文章就创建好了，文章一般会放在source/_posts文件夹里，打开创建的md文件，会看到开头有这么几句

```bash
---
title: 测试
date: 2026-01-07 23:35:27
tags:
---

```

这是这篇文章的front-matter，放在文件开头，用于配置这篇文章的基本信息。具体请参照[Front-matter | Hexo](https://hexo.io/zh-cn/docs/front-matter)

hexo的博客文章使用markdown语言书写，具体的写法请自行上网搜索，这里不再赘述，很简单的（

文章写好后记得进行保存。

## 部署和更新博客

因为我们是直接在vercel上创建的项目，再通过github克隆到本地，所以部署是相当方便的。

当文章写好后，先运行

```bash
hexo cl
```

清除本地的缓存文件，然后

```bash
hexo g
```

生成静态文件，再

```bash
hexo s
```

在本地服务器运行一下看看有没有问题，如果有问题需要修改，注意改完后**hexo cl和hexo g都要再运行一遍！**

如果我们是本地初始化项目，就还要再来个hexo d，还要再搞一堆配置，不过我们不需要，把远程仓库同步一下就行。

然后再稍微做一下准备工作

### 创建SSH密钥

还是win+R打开cmd。输入以下命令

```bash
ssh-keygen -t rsa -C "你的 GitHub 邮箱"
```

再进入C:\Users\用户名\\.ssh目录，能看到里面有一些无扩展名的文件（记得勾选显示隐藏文件）

![](https://img.57hmpg.top/file/1767802740287_20260108001853439.png)

用记事本打开id_rsa.pub文件，全选并复制里面全部内容

然后打开github，进入setting，打开如下界面

![](https://img.57hmpg.top/file/1767802979161_20260108002244984.png)

点击new ssh key，取名随意，将刚才复制的内容全部粘贴到下面的大框里，再点击add ssh key就ok了

完成添加后，在终端运行

```bash
ssh -T git@github.com //后面是你的邮箱
```

如果出现如下文字

```bash
Hi username! You’ve successfully authenticated, but GitHub does not provide shell access.
```

就说明ok了，接下来开始正式部署

### 进行push

```bash
git status
git add .
git status
git commit -m '注释'
git branch -m master
git push -f origin master
```

还是在项目文件夹打开终端，先输入

```bash
git status
```

运行后显示如下

![](https://img.57hmpg.top/file/1767803330735_20260108002845360.png)

这里会显示该项目中已更改的文件，红色的字体即为已更改但还未加入暂存区的文件

暂存区是啥？就是它会暂时保存好你要push的文件，不过你得自己手动加进去

再运行

```bash
git add .
```

（注意，add和点之间有空格）

这个命令会将更改过的所有文件加入到暂存区中，也可以将指定文件加入，这个就自行去搜索吧

如果此时弹警告，就最好将你更改的文件关掉

再来一次

```bash
git status
```

就会发现字变绿，说明已经加入到了暂存区

![](https://img.57hmpg.top/file/1767803653505_20260108003400372.png)

然后运行

```bash
git commit -m '注释'
```

这个命令就是将暂存区的内容提交到本地仓库。注意后面的注释一定要写，一是要养成写版本日志的良好习惯，二是不写可能会出问题

运行成功后，再运行

```bash
git branch -m master
```

切换到主分支（？），然后

```bash
git push -f origin master
```

就是将更改推送，上传到远程仓库了

打开github看一下，会发现已经push成功了

![](https://img.57hmpg.top/file/1767804023703_20260108004016539.png)

进入vercel查看，发现部署成功

![](https://img.57hmpg.top/file/1767804092910_20260108004124219.png)

打开网站，我们的测试文章也已经有了

![](https://img.57hmpg.top/file/1767804195868_20260108004301265.png)

以后每次写好文章，都要按照这一部分的步骤重来一遍（ssh密钥不用），不过以后push那里只要运行

```bash
git push
```

就行了

但现在看，这个博客未免太过毛坯房了，还有域名，该怎么办呢？？？？

能完成部署就已经ok了，剩下的主题配置和一些其他小知识就留到下一篇再说了。

## 常见问题

### hexo g生成失败

说明你的配置文件或者文章有问题，对照终端显示的错误信息去更改

### push报错无法连接SSH密钥

网络问题，多试几次

### vercel部署报错代码127

在项目目录里删除package-lock.json和node_modules文件夹，运行npm i，重新安装依赖，再重新push



## 参考文献

[Hexo](https://hexo.io/zh-cn/)

[Vercel](https://vercel.com/)

[使用 Hexo+GitHub+Vercel 搭建个人免费博客的最佳实践 | 春秋金经](https://www.chunqiujinjing.com/2023/06/18/hexo-hello-world/#部署流程)

[使用 Vercel 部署 Hexo 博客 - 白糖的记事本](https://blog.sugarmgp.cn/2024/10/20/blog-setup/#上传到-Github)

[利用Github与Vercel多端部署Hexo博客 | 白衣INK](https://baiyi.ink/posts/d89a0dc3.html#三-Vercel额外部署)

[如何使用 vercel + hexo 搭建博客 - 知乎](https://zhuanlan.zhihu.com/p/342790013)
