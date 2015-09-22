---
layout : post
title : 分支管理策略
category : Git
tags : Git
---
通常，合并分支时，如果可能，Git会用`“Fast forward”`模式，但这种模式下，删除分支后，会丢掉分支信息。如果要强制禁用`“Fast forward”`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。为了保证版本演进的清晰，希望采用这种做法.

首先，我们创建一个dev分支，并且切换到该分支：

	$ git checkout -b dev
	Switched to a new branch 'dev'

修改one.md文件，并提交修改：

	$ echo 'add branch manage' >> one.md
	$ git add ./one.md
	$ git commit -m 'dev branch'

<!--more-->

现在，切换到master分支：

	$ git checkout master

准备合并dev分支，请注意`--no-ff`参数，表示禁用“Fast forward”：

	$ git merge --no-ff -m 'merge with no-ff' dev
	Merge made by the 'recursive' strategy.
 	one.md | 1 +
 	1 file changed, 1 insertion(+)

因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去。

合并后，我们用git log看看分支历史：

	$ git log --graph --pretty=oneline --abbrev-commit
	*   98aa0eb merge with no-ff
	|\
	| * 0b6f37c dev branch
	|/
	*   9eb72e8 update
	|\
	| * f4b1974 I am dev
	* | 6fb4e38 I am master
	|/

可以看到，不使用“Fast forward”模式，merge后就像这样：

![picture](http://ww3.sinaimg.cn/mw690/bd5a4d63gw1ehswmf2jhhj20dc07574c.jpg)

###分支策略

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；

你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

![pic](http://ww1.sinaimg.cn/mw690/bd5a4d63gw1ehswp18k3xj20du03haab.jpg)

---
###小结

**Git分支十分强大，在团队开发中应该充分应用。**

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。