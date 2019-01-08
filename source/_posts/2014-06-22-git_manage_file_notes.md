---
layout : post
title : "版本库文件的管理"
category : Git
tags : Git
date: 2014-06-22
---
##撤销修改

现在，我们查看下`one.txt`文件：

	$ cat one.txt
	git is the best
	I love git
	error content

假如，当我们没提交前，又想撤销这次的修改，那该怎么办呢？这时我们可以直接手动删除修改的内容，恢复到原来的版本。此时，我们可以使用`git status`查看下：

<!--more-->

	$ git status
	# On branch master
	# Your branch is ahead of 'origin/master' by 1 commit.
	#   (use "git push" to publish your local commits)
	#
	# Changes not staged for commit:
	#   (use "git add <file>..." to update what will be committed)
	#   (use "git checkout -- <file>..." to discard changes in working directory)
	#
	#       modified:   one.txt
	#
	no changes added to commit (use "git add" and/or "git commit -a")



你可以发现，Git会告诉你，git checkout -- file可以丢弃工作区的修改：


	$ git checkout -- one.txt

命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：

1、 一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

2、一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次git commit或git add时的状态。

git checkout -- file命令中的“--”很重要，没有“--”，就变成了“创建一个新分支”的命令.

当我们使用`git add`添加到暂存区后，有想要撤消修改，咋办呢？

	$ git status
	# On branch master
	# Your branch is ahead of 'origin/master' by 1 commit.
	#   (use "git push" to publish your local commits)
	#
	# Changes to be committed:
	#   (use "git reset HEAD <file>..." to unstage)
	#
	#       modified:   one.txt
	#

Git同样告诉我们，用命令`git reset HEAD file`可以把暂存区的修改撤销掉（unstage），重新放回工作区.

git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。

那如何恢复工作区的修改呢？还记得`git checkout --file`吗，对，就是这个命令：

	$ git checkout -- one.txt

现在，假设你不但改错了东西，还从暂存区提交到了版本库，怎么办呢？还记得版本回退一节吗？可以回退到上一个版本。不过，这是有条件的，就是你还没有把自己的本地版本库推送到远程。

##小结

*   当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

*   当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD file`，就回到了场景1，第二步按场景1操作。

*   已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

	

