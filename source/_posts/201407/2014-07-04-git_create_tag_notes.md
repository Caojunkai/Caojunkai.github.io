---
layout : post
title : 创建标签和操作标签
category : Git
tags : Git
---
提交一个版本时，我们通常先在版本库中打一个标签，这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

###创建标签

使用`git tag name`命令来创建新标签：

	$ git tag v1.0

使用`git tag`来查看所有标签：

	$ git tag
	v1.0

<!--more-->

注意，标签不是按时间顺序列出，而是按字母排序的。

默认标签是打在最新提交的commit上的。有时候，如果忘了打标签，比如，现在已经是周五了，但应该在周一打的标签没有打，怎么办？

方法是找到历史提交的`commit id`，然后打上就可以了：

	$ git log --pretty=oneline --abbrev-commit
	ea4c0c6 merge bug-101
	98aa0eb merge with no-ff
	0b6f37c dev branch
	9eb72e8 update

假如要对“bug-101”加标签，那么他的commit id是“ea4c0c6”:

	$ git tag v2.0 ea4c0c6

可以用`git show tagname`查看标签信息：

	$ git show v2.0
	commit ea4c0c6f646b3f38d586fc803817a73915b3753e
	Merge: 98aa0eb ddc86c4
	Author: Hanson <YourMail@gmail.com>
	Date:   Sat Jun 28 17:05:31 2014 +0800

    	merge bug-101

还可以创建带有说明的标签，用-a指定标签名，-m指定说明文字：

	$ git tag -a v3.0 -m 'dev branch' 0b6f37c

还可以通过-s用私钥签名一个标签：

	$ git tag -s v4.0 -m "merege with dev" 0b6f7c

---

###小结

命令`git tag name`用于新建一个标签，默认为HEAD，也可以指定一个`commit id`；

`git tag -a tagname -m "blablabla..."`可以指定标签信息；

`git tag -s tagname -m "blablabla..."`可以用PGP签名标签；

命令`git tag`可以查看所有标签；
命令`git show tagname`来查看标签信息；


###操作标签

删除某个标签，使用`git tag -d tagname`:

	$ git tag -d v1.0

因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

如果要推送某个标签到远程，使用命令`git push origin tagname`：

	$ git push origin v2.0

或者，一次性推送全部尚未推送到远程的本地标签：

	$ git push origin --tags

如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除,然后再远程删除：

	$ git tag -d v2.0   //本地删除
	$ git push origin :refs/tags/v2.0		//远程删除

要看看是否真的从远程库删除了标签，可以登陆Github查看。

---

###小结

命令`git push origin tagname`可以推送一个本地标签；

命令`git push origin --tags`可以推送全部未推送过的本地标签；

命令`git tag -d tagname`可以删除一个本地标签；

命令`git push origin :refs/tags/tagname`可以删除一个远程标签。

