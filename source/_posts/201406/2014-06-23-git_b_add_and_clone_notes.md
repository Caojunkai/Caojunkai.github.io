---
layout : post
title : "添加远程仓库和从远程仓库克隆"
category : Git
tags : Git
date: 2014-06-23
---
现在，我们已经在本地创建了一个版本库，那么如何在Github创建了一个仓库，并且让这两个仓库进行远程同步呢？

##添加远程库

首先，登陆GitHub，然后，在右上角找到“Create a new repo”按钮，创建一个新的仓库：

<!--more-->

![图片](http://ww4.sinaimg.cn/mw690/bd5a4d63gw1ehocqkjinqj20rl0elack.jpg)

目前，在GitHub上的这个learngit仓库还是空的，GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。

现在，我们根据GitHub的提示，在本地的learngit仓库下运行命令：

	$ git remote add origin git@github.com:GithubName/learngit.git

请千万注意，把上面的GithubName替换成你自己的GitHub账户名.

添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。

下一步，就可以把本地库的所有内容推送到远程库上：

	$ git push -u origin master

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支master推送到远程。

由于远程库是空的，我们第一次推送master分支时，加上了`-u`参数，Git不但会把本地的master分支内容推送到远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地作了提交，就可以通过命令：

	$ git push origin master

把本地master分支的最新修改推送至GitHub，现在，就拥有了真正的分布式版本库！

##克隆远程库

现在，我们已经有了一个远程learngit仓库，假设我们没有本地的版本仓库，那么就需要从远程进行克隆了。

要克隆远程仓库，可以使用`git clone`命令来达到目的,最后可以加上一个自己命名的文件名，不写则使用默认的。

	$ git clone git@github.com:GithubName/learngit.git （可以自己加上文件名）
	Cloning into 'learngit'...
	remote: Counting objects: 3, done.
	remote: Total 3 (delta 0), reused 0 (delta 0)
	Receiving objects: 100% (3/3), done.

如果有多个人协作开发，那么每个人各自从远程克隆一份就可以了。

你也许还注意到，GitHub给出的地址不止一个，还可以用`https://github.com/GIthubName/learngit.git`这样的地址。实际上，Git支持多种协议，默认的git://使用ssh，但也可以使用https等其他协议。

**注意**

使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令。

---

###小结

要关联一个远程库，使用命令`git remote add origin git@github.com/GithubName/***.git`

关联后，使用命令`git push -u origin master`第一次推送**master**分支的所有内容；

此后，每次本地提交后，就可以使用命令`git push origin master`推送最新修改；

要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。

Git支持多种协议，包括https，**但通过ssh支持的原生git协议速度最快**。

分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步，真是太方便了！




