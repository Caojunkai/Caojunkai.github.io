---
layout : post
title : 解决分支冲突问题
category : Git
tags : Git
date: 2014-06-26
---
我们已经知道了如何去创建分支以及合并分支，但实际工作中合并分支并不总是像我们想象的那么一帆风顺，总会发生这样那样的冲突，下面说明下如何去解决如此的问题。

首先，创建一个分支dev，并添加新内容：
	
	$ git checkout -b dev
	$ echo 'Hello Php' >> one.md

到此，我们在dev分支上进行添加与提交：

	$ git add ./one.md
	$ git commit -m 'I am dev'

<!--more-->

现在，我们切换分支到master：

	$ git checkout master

在master分支上添加新内容到one.md文件

	$ echo 'I am master' >> one.md

现在，在master分支上提交：

	$ git add one.md
	$ git commit -m 'I am master'

到这一步时，我们已经很清楚的知道master分支与dev分支都进行了一次提交，图示如下：

![picture](http://ww1.sinaimg.cn/mw690/bd5a4d63gw1ehrtiazeufj20br06x3yl.jpg)

这时，对这两个分支进行合并，会发生什么呢？来看下：

>git merge dev

>Auto-merging one.md

>CONFLICT (content): Merge conflict in one.md

>Automatic merge failed; fix conflicts and then commit the result.

这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突.

果然冲突了！Git告诉我们，one.md文件存在冲突，必须手动解决冲突后再提交。`git status`也可以告诉我们冲突的文件：

	$ git status
	# On branch master
	# You have unmerged paths.
	#   (fix conflicts and run "git commit")
	#
	# Unmerged paths:
	#   (use "git add <file>..." to mark resolution)
	#
	#       both modified:      one.md
	#
	no changes added to commit (use "git add" and/or "git commit -a")

直接查看one.md文件，看看有什么：

	$ cat one.md
	hello php
	hello php
	I am branch
	I am master
	<<<<<< HEAD
	Hello Python
	=======
	Hello Php
	>>>>>>> dev

Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容，我们修改如下后保存:

>Hello Python

再次提交：

	$ git add one.md
	$ git commit -m 'update'
	[master 9eb72e8] update

可以使用带参数的`git log`也可以看到分支的合并情况：

	$  git log --graph --pretty=oneline --abbrev-commit

![picture](http://ww2.sinaimg.cn/mw690/bd5a4d63gw1ehrt4j4i4qj205v04yaac.jpg)

现在，删除dev分支：

	$ git branch -d dev
	Deleted branch dev (was f4b1974)

---

###小结
	
当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

用`git log --graph`命令可以看到分支合并图。




