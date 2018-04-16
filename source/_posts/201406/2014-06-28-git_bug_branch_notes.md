---
layout : post
title : 管理Bug分支
category : Git
tags : Git
date: 2014-06-28
---
在项目开发中，bug就像家常便饭一样，随时都会出现。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

当你接到一个修复一个代号101的bug的任务时，很自然地，你想创建一个分支bug-101来修复它，但是，等等，当前正在dev上进行的工作还没有提交：

	$ git status
	# On branch dev
	# Changes not staged for commit:
	#   (use "git add <file>..." to update what will be committed)
	#   (use "git checkout -- <file>..." to discard changes in working directory)
	#
	#       modified:   one.md
	#
	no changes added to commit (use "git add" and/or "git commit -a")

<!--more-->

并不是你不想提交，而是工作只进行到一半，还没法提交，那咋办？

Git给我们提供了一个`stash`功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

	$ git stash
	Saved working directory and index state WIP on dev: 98aa0eb merge with no-ff
	HEAD is now at 98aa0eb merge with no-ff

现在，使用`git status`查看下：

	$ git status
	# On branch dev
	nothing to commit, working directory clean

发现工作区已经是干净的了。我们可以放心的去创建分支来修复bug。

首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支：

	$ git checkout master
	$ git checkout -b bug-101
	Switched to a new branch 'bug-101'

现在我们来修复bug，并提交：

	$ git add ./one.md
	$ git commit -m '修复bug101'
	[bug-101 ddc86c4] 修复bug101
 	1 file changed, 1 insertion(+), 1 deletion(-)

修复完成后，切换到master分支，并完成合并，最后删除bug-101分支：

	$ git checkout master
	$ git merge --no-ff -m 'merge bug-101' bug-101
	Merge made by the 'recursive' strategy.
 	one.md | 2 +-
 	1 file changed, 1 insertion(+), 1 deletion(-)
	$ git branch -d bug-101

太好了，现在已经修复完成了，该回到dev分支工作了。

	$ git checkout dev
	$ git status
	# On branch dev
	nothing to commit (working directory clean)

工作区是干净的，刚才的工作现场存到哪去了？用`git stash list`命令看看:

	$ git stash list
	stash@{0}: WIP on dev: 98aa0eb merge with no-ff

工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：

一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除

另一种方式是用`git stash pop`，恢复的同时把stash内容也删了：

	$ git stash pop
	# On branch dev
	# Changes not staged for commit:
	#   (use "git add <file>..." to update what will be committed)
	#   (use "git checkout -- <file>..." to discard changes in working directory)
	#
	#       modified:   one.md
	#
	no changes added to commit (use "git add" and/or "git commit -a")
	Dropped refs/stash@{0} (06be1e02d3a4d183b0585339a3ffc232b031abf1)

可以发现，最后一行已经告诉我们把stash内容删除了。

现在使用`git stash list`，可以看到stash是空的。

也可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：

	$ git stash apply stash@{0}

---

###小结

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场。

	






