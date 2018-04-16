---
layout : post
title : 配置篇-玩转Tmux
category : Tmux
tags : tmux
date: 2014-11-30
---
上一篇，我们已经安装好了我们的威猛先生Tmux神器。既然被称为是神器，肯定是其被人称道的地方，那么现在就来动手亲自配置我们的Tmux，初体验她的酷以及可定制化。

废话不多说，下面开始吧。。。

**常用快捷键**

Tmux的任何指令都包含一个前缀，也就是说当你按了前缀（默认Ctrl+b）以后，系统才知道你的命令是发给Tmux来执行的（[你也可以参考这里](http://baike.baidu.com/view/9065064.htm?fr=aladdin)）

<!--more-->

>* C-b ? 显示快捷键帮助
>* C-b C-o 调换窗口位置，类似与vim 里的C-w
>* C-b 空格键 采用下一个内置布局
>* C-b ! 把当前窗口变为新窗口
>* C-b " 模向分隔窗口
>* C-b % 纵向分隔窗口
>* C-b q 显示分隔窗口的编号
>* C-b o 跳到下一个分隔窗口
>* C-b 上下键 上一个及下一个分隔窗口
>* C-b C-方向键 调整分隔窗口大小
>* C-b c 创建新窗口
>* C-b 0~9 选择几号窗口
>* C-b c 创建新窗口
>* C-b n 选择下一个窗口
>* C-b l 切换到最后使用的窗口
>* C-b p 选择前一个窗口
>* C-b w 以菜单方式显示及选择窗口
>* C-b t 显示时钟
>* C-b ; 切换到最后一个使用的面板
>* C-b x 关闭面板
>* C-b & 关闭窗口
>* C-b s 以菜单方式显示和选择会话
>* C-b d 退出tumx，并保存当前会话，这时，tmux仍在后台运行，可以通过tmux attach进入 到指定的会话

##开始定制Tmux

Tmux的配置文件是 `~/.tmux.conf`，这个文件可能不存在，你可以自己新建。开始配置前，首先，有没有觉得tmux的前缀按起来太不方便了，ctrl与b键隔得太远，很多人把它映射成C+a，也就 是在配置文件(`~/.tmux.conf`)中加入下面这条语句：

	#设置前缀为Ctrl + x
	set -g prefix C-x

于此同时，取消掉默认的前缀按键：

	#解除Ctrl+b 与前缀的对应关系
	unbind C-b	

配置完以后，重启tmux起效，或者先按C+b，然后输入：，进入命令行模式， 在命令行模式下输入：

	source-file ~/.tmux.conf

你也可以在配置文件中加入下面这句话，以后改了只需要按`前缀+r`了。
	
	#将r 设置为加载配置文件，并显示"reloaded!"信息
	bind r source-file ~/.tmux.conf \; display "Reloaded!"

很多人都喜欢改成Ctrl+a，不过我个人更喜欢Ctrl+x，如果你是vim用户，你一定懂的。把前缀映射改成Ctrl+x，再加入如下几条语句， 现在切换窗口就和vim一摸一样了，顿时觉得亲切了很多。

	#up
	bind-key k select-pane -U
	#down
	bind-key j select-pane -D
	#left
	ind-key h select-pane -L
	#right
	bind-key l select-pane -R

上面的最后一条语句会更改C-x l的功能，我挺喜欢这个功能的，因为我们很时候都是在两个窗 口或这两个面板中切换，所以我又加入如下语句

	#select last window
	bind-key C-l select-window -l

现在我的l键可不能随便按了，`Ctrl+x l`是切换面板，`Ctrl+x Ctrl+l`切换窗口，`Ctrl+l`清屏。

##会话

>* C-x s 以菜单的方式查看并选择会话
>* C-x :new-session 新建一个会话
>* C-x d 退出并保存会话
>* 终端运行 tmux attach 返回会话

##命名会话

	tmux new -s session
	tmux new -s session -d #在后台建立会话
	tmux ls #列出会话
	tmux attach -t session #进入某个会话

---

好了，今天先练习这么多，看的太多其实也记不住多少的，你说对吧。就像学vim，很多人都是因为听了很多大牛说vim是神器才去学的（vim本来就是神器），但是这些菜鸟想着就是一下子完全掌握vim，唉，你觉得可能吗。vim学习起来难度大、成本也高，但是当我们放下浮躁的心态，每天学一点点vim命令或者操作，然后坚持下去，你说你能学不会？答案肯定是：总有一天会完全掌握的，vim也就那么回事。

其实，这些话也是说给我自己听的，因为最近发现自己有点浮躁的苗头，拒绝浮躁，踏实自我~~~

**参考博客：**

[http://mingxinglai.com/cn/2012/09/tmux/](http://mingxinglai.com/cn/2012/09/tmux/)

