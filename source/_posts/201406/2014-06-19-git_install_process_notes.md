---
layout : post
title : "安装分布式版本控制系统Git"
category : Git
tags : 分布式版本控制 Git
date: 2014-06-19
---
Git是什么？Git是世界上最先进的分布式版本控制系统。

Git有什么特点？简单说就是：高端大气上档次！

那么什么是版本控制系统呢？自己[百度](http://zh.wikipedia.org/wiki/%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6%E7%B3%BB%E7%BB%9F)去。

很多人都知道，李纳斯创建了开源的Linux，从那时起Linux不断发展，已经成为最大的服务器系统软件了。李纳斯虽然创建了Linux，但Linux的壮大是靠全世界热心的hacker参与的，这么多人在世界各地为Linux编写代码，那Linux的代码是如何管理的呢？

李纳斯花了两周时间自己用C写了一个分布式版本控制系统，这就是Git！一个月之内，Linux系统的源码已经由Git管理了！**大神**是怎么定义的呢？大家可以体会一下。

<!--more-->

要使用Git，毋庸置疑首先得要先安装Git了，现在Git已经在Unix、Linux、Mac和Windows上正常运行了。下面，一步步来安装Git：

##Linux上安装Git

首先，在命令行输入`git`查看系统是否已经安装好了。若你是用的是Debian或者Ubuntu，可以直接输入命令`sudo apt-get install git`就可以直接根据依赖关系自动安装好，很简单、方便。如果是Redhat系列的linux版本，则可以使用`yum`命令或者使用下载好的源代码来安装。

源代码安装：

	./config
	make
	make install

##Windows安装Git

说实话，笔者是非常讨厌Windows操作系统的，存在各种兼容性等其他问题，而我们潜移默化都在受Windows的影响，对于Linux就不存在这个问题了，Linux的开源思想的确对本人影响很大，这里不再赘余这些。

我么只需下载exe的Git安装程序，一直点‘下一步’默认安装就ok。在开始菜单里找到Git->Git bash，就说明安装成功。

##安装后配置

在命令行输入：

	$ git config -- global user.name "你的名字"
	$ git config -- global user.email "你的邮箱"

因为Git是分布式版本控制系统，所以，每个机器都必须自报家门：你的名字和Email地址。你也许会担心，如果有人故意冒充别人怎么办？这个不必担心，首先我们相信大家都是善良无知的群众，其次，真的有冒充的也是有办法可查的。

注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

