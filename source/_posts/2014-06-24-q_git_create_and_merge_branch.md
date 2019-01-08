---
layout : post
title : "创建与合并分支"
category : Git
tags : Git
date: 2014-06-24
---
分支就是科幻电影里面的平行宇宙，当你正在电脑前努力学习Git的时候，另一个你正在另一个平行宇宙里努力学习SVN。

如果两个平行宇宙互不干扰，那对现在的你也没啥影响。不过，在某个时间点，两个平行宇宙合并了，结果，你既学会了Git又学会了SVN,是不是很棒、很神奇啊！

一开始的时候，master分支是一条线，Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点：

<!--more-->


![picture](http://ww3.sinaimg.cn/mw690/bd5a4d63gw1ehpf84llcnj208d047wee.jpg)

每次提交，master分支都会向前移动一步，这样，随着你不断提交，master分支的线也越来越长.

###创建分支

创建dev分支，然后切换到该分支：

	$ git checkout -b dev
	Switched to a new branch 'dev'

`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

	$ git branch dev
	$ git checkout dev
	Switched to a new branch 'dev'

使用`git beanch`查看当前分支：

	$ git branch
	* dev
  	master

`git branch`命令会列出所有分支，当前分支前面会标一个`*`号.

下面我们在dev分支上添加文件添加内容，并进行提交：
	
	$ touch three.txt
	$ echo 'using dev branch' >> three.txt

然后提交：
	
	$ git add three.txt
	$ git commit -m 'dev branch'

现在我们切换回master分支，使用`git checkout master`命令：

	$ git checkout master
	Switched to branch 'master'

切换回master分支后，再查看一个three.txt文件，刚才添加的内容不见了！因为那个提交是在dev分支上，而master分支此刻的提交点并没有变：

![picture](http://ww4.sinaimg.cn/mw690/bd5a4d63gw1ehpfp10rioj20bn066t8p.jpg)

那怎么才能把dev分支的工作合并到master上呢？使用`git merge`命令：

	$ git merge dev
	Updating f8077c3..b1b45f5
	Fast-forward
 	three.txt | 2 +-
 	1 file changed, 1 insertion(+), 1 deletion(-)

`git merge`命令用于**合并指定分支到当前分支**。合并后，再查看three.txt的内容，就可以看到，和dev分支的最新提交是完全一样的。

注意到上面的Fast-forward信息，Git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快。

现在，我们该怎么删除刚创建的dev分支呢？使用`git branch -d `命令：

	$ git branch -d dev
	Deleted branch dev (was b1b45f5).

**注意：**

要删除某个分支时，必须切出要删除的分支，要不然会报错。

删除后，查看branch，就只剩下master分支了：

	$ git branch
	* master

因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在master分支上工作效果是一样的，但过程更安全

---

###小结

查看当前分支：`git branch`

创建分支：`git branch name`

切换分支：`git checkout name`

创建+切换分支：`git checkout -b name`

合并某分支到当前分支： `git merge name`

删除某分支: `git branch -d name`


