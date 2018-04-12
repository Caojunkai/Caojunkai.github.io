---
layout : post
title : 版本控制系统配置别名
category : Git
tags : Git
---
有时候，会觉得Git的命令太长又不好记住，那么能不能做到简短呢？答案当然是肯定的啦！

比如说`git status`这个命令对有些人来说就很长，不容易记住，如果可以这样`git st`使用那就好了，我们强大的Git版本控制可是很人性化的哦，已经为你设计好可以为命令设计别名来达到你“偷懒”的目的。

我们只需要敲一行命令，告诉Git，以后st就表示status：

	$ git config --global alias.st status

ok，现在在命令行敲入`git st`查看效果吧。

<!--more-->

当然还有别的命令可以简写，很多人都用co表示checkout，ci表示commit，br表示branch：

	$ git config --global alias.co checkout
	$ git config --global alias.ci commit
	$ git config --global alias.br branch

`--global`参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用。

下面是配置别名的格式：

	$ git config --global alias.别名 命令

这样就ok了，你可以尽情的去配置你喜欢的别名，妈妈以后再也不用担心我的学习了:)

---

###小结

给Git配置好别名，就可以输入命令时偷个懒。记住，偷懒不一定是坏事哦！！