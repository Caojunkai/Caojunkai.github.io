---
layout : post
title : "Git创建版本库和查看工作区状态"
category : Git
tags : Git
date: 2014-06-19
---
什么是版本库呢？版本库又名仓库，英文名repository，可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”.

首先，选择一个合适的地方，创建一个空目录：

	$ mkdir learngit
	$ cd learngit

**注意：** 如果你使用Windows系统，为了避免遇到各种莫名其妙的问题，请确保目录名（包括父目录）不包含中文。

然后，通过`git init`命令把该目录变成Git可以管理的仓库：

	$ git init

<!--more-->


此时，Git就把仓库建好了，而且告诉你是一个空的仓库`empty Git repository`，可以发现当前目录下多了一个`.git`的目录，这个目录是Git来跟踪管理版本库的，不要随便修改里面的文件。

###把文件添加到版本库

*明确一下*，所有的版本控制系统，其实只能跟踪文本文件的改动，比如TXT文件，网页，所有的程序代码等等，Git也不例外。版本控制系统可以告诉你每次的改动，比如在第5行加了一个单词“Linux”，在第8行删了一个单词“Windows”。而图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化，只能把二进制文件每次改动串起来，也就是只知道图片从100KB改成了120KB，但到底改了啥，版本控制系统不知道，也没法知道。

废话不多说，在learngit目录下我们创建一个`readme.md`文件，并添加一些内容：
	
	$ git is a free software.

完成文件添加版本库，分两步一起来完成：
	
第一步：使用`git add`告诉git把文件添加到版本库

	$ git add ./readme.md

第二步，使用`git commit`告诉git把文件提交到仓库

	$ git commit -m 'add a new file'

`git commit`命令，-m后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。


###查看工作区状态

我们已经成功提交了一个readme.md文件，现在我们继续修改该文件，添加：
	
	Git is a distritbuted version control system.

Now,我们运行`git status` 来查看此时的仓库状态：
	
	$ git status

git status命令可以让我们时刻掌握仓库当前的状态，上面的命令告诉我们，readme.md被修改过了，但还没有准备提交的修改。

虽然Git告诉我们readme.md被修改了，但如果能看看具体修改了什么内容，自然是很好的。比如你周一上班时，已经记不清上次怎么修改的readme.md，所以，需要用`git diff`这个命令看看：

	$ git diff

知道了做了什么修改后，就可以提交新文件了：

	$ git add ./readme.md
	$ git commit -m '***'

此时，再次运行`git status`来查看状态，git会告诉我们当前没有需要提交的修改，工作目录是干净的`working directory clean`。


###总结一下：

1、初始化仓库：`git init`

2、添加文件到仓库：
	
	第一步：git add 	//可添加多个文件
	第二步：git commit

3、查看工作区状态：`git status`

4、查看修改内容： `git diff`

(完)

