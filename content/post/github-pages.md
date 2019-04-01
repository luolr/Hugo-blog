+++
categories = ["github"]
date = "2018-08-30"
description = "如何使用github pages搭建一个自己的个人博客"
tags = ["github"]
title = "使用GitHub Pages搭建静态网站"
author = "罗椋仁"
+++

作为一个程序员，个人博客肯定是必不可少的，虽然大家都善用百度、谷歌，感觉搭个博客还不是小case。但我还是想水上一篇，毕竟老司机虽多，新人也不少嘛，顺带帮自己总结下经验。
<!--more-->

### 前言

目前网上有很多工具可以帮助我们快速搭建一个静态Web站点，常见的就有Hexo、Hugo、Jekyll和WordPress等，你可能听过甚至用过其中的一两个。在这些工具之中，目前比较火热的是Hexo和Hugo，至于Jekyll和WordPress作为前辈，已经渐渐淡出了人们的视线。

至于Hexo和Hugo如何选择，我觉得都是可以的。Hexo的文档较为详细，主题和插件也比较丰富，适合喜欢折腾的朋友。而Hugo作为后起之秀，则更为轻量，网站编译的速度突出一个Fast，一个中等规模的静态网站几分之一秒内就可以生成出来。两者都有各自的特点，我个人更喜欢Hugo，所以下文我以Hugo为例。

* [Hugo • 简单、易用、高效、快速部署](http://www.gohugo.org)
* [Hexo • 快速、简洁且高效的博客框架](https://hexo.io/zh-cn)
* [Jekyll • 简单的博客、静态网站工具](https://www.jekyll.com.cn)
* [WordPress • It's pretty old school](https://codex.wordpress.org/zh-cn:Main_Page)

### 准备工作

这些工具的安装都相当的简单，基本上几个命令就能搞定，以Hugo为例：

```
1. 安装 Homebrew
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

2. 安装Hugo
$ brew install hugo

3. 生成站点
$ hugo new site siteName

4. 安装皮肤
$ cd siteName/themes
$ git clone https://github.com/spf13/hyde.git

5. 运行Hugo
$ cd ..
$ hugo server --theme=hyde --buildDrafts
```

然后，你就可以前往`localhost:1313`查看你的个人博客了。当然，Hugo也和其他生成工具一样，支持更换主题，可以根据自己的喜好安装不同的主题。

### GitHub Pages

Github Pages是一个静态站点托管系统，可以提供一个便捷的静态网站托管服务。它还为每个GitHub账号提供了一个免费的域名，假如你github的用户名是"test"，那么你就可以使用test.github.io的域名。

首先，我们要有一个项目来存放代码。我们新建一个项目，如果你想通过test.github.io就可以直接访问到的话，可以直接将其作为项目名称。不过，每个github账号只能有一个这样的域名，所以一般用于个人博客。
![new repository](/img/github_1.png)

有项目之后，我们可以开始着手部署了。我们在Hugo项目的根目录下执行以下命令，顺利的话会生成一个 `public` 目录，网站的所有静态代码都在里面，将目录里所有文件拷至刚创建的GitHub项目中，然后 `push` 你的代码到远程的master分支。

```
$ hugo --theme=hyde --baseUrl="http://test.github.io/"
```

想要使用	`GiHub Pages` 的服务，我们需要在项目中手动开启，在项目Settings中找到 `GitHub Pages` 版块，选择需要展示的分支，默认是master分支，稍等一会就会得到一个网址，那就说明你的网站已经生成成功啦。
![new repository](/img/github_2.png)

在实际使用中，GitHub Pages不只可以用来制作个人博客，像项目文档这样的静态资源也都可以使用这一服务。比如说，你和小伙伴正在写一个新的项目，作为一个优秀的项目，光写代码肯定不行啊，还需要一个完善的文档，一个小小的 `README` 恐怕是很难满足你。

此时你可能遇到一个问题，这个文档我是否需要另起一个项目来维护呢，有没有更好的选择呢？当然是有的，GitHub Pages 支持你选择 master 以外的分支来作为你的网站源代码，你可以新建一个分支 `gh-pages` 用来专门存放你的网站静态资源。那么你可能会想，如何将一个项目的子目录作为一个新的分支的代码呢？

这里我推荐一个工具：`Git Subtree`。

### Git Subtree

git subtree 可以实现一个仓库作为其他仓库的子仓库。

![new repository](/img/github_3.png)

git subtree的主要命令有：

``` basic
git subtree add   --prefix=<prefix> <commit>
git subtree add   --prefix=<prefix> <repository> <ref>
git subtree pull  --prefix=<prefix> <repository> <ref>
git subtree push  --prefix=<prefix> <repository> <ref>
git subtree merge --prefix=<prefix> <commit>
git subtree split --prefix=<prefix> [OPTIONS] [<commit>]
```

这里主要用到的是 push 这条命令，结合我们之前Hugo项目，我们的静态资源都生成在了`public` 文件夹下，此刻我们现在需要将该目录推至远端的gh-pages分支，那么可以使用如下命令：

```
$ git subtree push --prefix=public origin gh-pages
```

这样，我们就可以在愉快的在`GitHub Pages`中选取`gh-pages`分支啦。

#### 参考资料

+ [git subtree教程 - SegmentFault](https://segmentfault.com/a/1190000012002151)

