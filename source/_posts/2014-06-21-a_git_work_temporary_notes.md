---
layout : post
title : "Git工作区和暂存区"
category : Git
tags : Git
date: 2014-06-21
---
首先，我们要清楚Git和其他版本控制系统如SVN的一个不同之处就是有暂存区的概念。下面来看几个概念：

###工作区

工作区就是我们在电脑里看到的目录，比如我的learngit就是一个工作区。

###版本库

在工作区中都有一个.git隐藏文件夹，这个就是所谓的Git版本库。

###暂存区

Git的版本库里有很多的文件，其中比较重要的就是stage(或者index)文件，这个就是**暂存区**。还有Git为我们自动创建的第一个分支master，以及指向master的一个指针HEAD。

<!--more-->

![pic](http://ww1.sinaimg.cn/mw690/bd5a4d63gw1ehlvgkz640j20cq06i74g.jpg)

前面我们知道，当我们要提交修改的文件时是分为两步的：

* 第一步：`git add`把文件添加进去，实际上是把文件修改添加到暂存区。
* 第二步：`git commit`把文件提交给版本库，实际上是把暂存区的所有内容提交到当前的分支。


因为我们创建Git版本库时，Git自动为我们创建了唯一一个master分支，所以，现在，commit就是往master分支上提交更改。

可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

一旦提交后，如果又没有对工作区做任何修改，那么工作区就是“干净”的：

	$ git status
	# On branch master
	nothing to commit (working directory clean)

###小结

暂存区是Git非常重要的概念，弄明白了暂存区，就弄明白了Git的很多操作到底干了什么。
