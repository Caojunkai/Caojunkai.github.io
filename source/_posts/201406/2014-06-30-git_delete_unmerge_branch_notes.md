---
layout : post
title : Feature分支
category : Git
tags : Git
---
添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。

假设我们创建了一个feature分支来开发新功能：

	$ git checkout -b feature

当我们开发好新功能，并且已经提交完成：

	$ git add two.md
	$ git commit -m 'feature branch'

<!--more-->

此时，切换到master分支：

	$ git checkout master

如果一切顺利的话，feature分支和bug分支是类似的，合并，然后删除。

但是，就在此时接到通知，要删除这个新功能：

	$ git branch -d feature
	error: The branch 'feature' is not fully merged.
	If you are sure you want to delete it, run 'git branch -D feature'.

销毁失败。Git友情提醒，feature分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用命令`git branch -D feature`。

现在我们强行删除：

	$ git branch -D feature
	Deleted branch feature (was 80ab808).

终于删除成功！

---

###小结

当开发一个新功能时，最好新创建一feature分支。

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D name`强行删除。