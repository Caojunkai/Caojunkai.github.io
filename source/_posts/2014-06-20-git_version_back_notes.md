---
layout : post
title : "Git实现版本回退功能"
category : Git
tags : Git
date: 2014-06-20
---
现在，我们重新修改下文件并添加和提交到版本库：

	$ echo '再次添加新内容' >> readme.md
	$ git add ./readme.md
	$ git commit -m '更新内容'


像这样，我们不断的对文件进行修改，并一次一次的提交到版本库。就像我们在玩游戏时，每过一关我们都会保存当前的状态，以防备哪次被打败了，我们就可以选择重新读取保存的状态，然后可以从最近的地方重新开始玩。Git也是一样的道理，每当你觉得文件修改到一定程度的时候，就可以保存一个**快照**，这个快照在Git中被称为**commit**。一旦你把文件改乱了，或者误删了文件，还可以从最近的一个commit恢复，然后继续工作，而不是把几个月的工作成果全部丢失。

<!--more-->

那么，我们人的脑子怎么可能记住一个有几千行的代码文件都修改过什么啊，所以Git自由其智能之处，我们可以使用`git log`来查看

	$ git log
	commit 45c00ae6775bce4833f8541cf978781966f78565
	Author: Hanson <yafengdoudou@gmail.com>
	Date:   Fri Jun 20 20:08:58 2014 +0800

    更新内容

	commit 3de50f6d891bf7c7432edc2a959fd2fbfa9286b0
	Author: Hanson <yafengdoudou@gmail.com>
	Date:   Fri Jun 20 19:51:30 2014 +0800

    添加hello git

	**此处省略**

	commit 6e5f32ab6d7319274800f1d751e63fdc67008562
	Author: Hanson <yafengdoudou@gmail.com>
	Date:   Thu Jun 19 15:00:45 2014 +0800

    new file

如果嫌输出的信息太多，我们可以使用`git log --pretty=oneline`来查看啦

	$ git log --pretty=oneline
	45c00ae6775bce4833f8541cf978781966f78565 更新内容
	3de50f6d891bf7c7432edc2a959fd2fbfa9286b0 添加hello
	8edeaca3329ec0b38a1a66b948dabd8cb6e7dfa8 update fil
	d7d25794ef327789dae190d4ea9f65775d54d741 提交测试文
	67a36db3844043c7f97910cb177c6631d2f1cd2b add php
	6e5f32ab6d7319274800f1d751e63fdc67008562 new file

需要友情提示的是，你看到的一大串类似“ 45c00ae...6f78565”的是commit id（版本号），和SVN不一样，Git的commit id不是1，2，3……递增的数字，而是一个SHA1计算出来的一个非常大的数字(40位)，用十六进制表示，而且你看到的commit id和我的肯定不一样，以你自己的为准。为什么commit id需要用这么一大串数字表示呢？因为Git是分布式的版本控制系统，后面我们还要研究多人在同一个版本库里工作，如果大家都用1，2，3……作为版本号，那肯定就冲突了。

现在，我们开始回退到上一个版本，首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交“ 45c00ae...6f78565”（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。

我们可以使用`git reset`回退到某个版本：
	
	$ git reset --hard HEAD^
	HEAD is now at 3de50f6 添加hello git

再次`git log --pretty=oneline`下：

	$ git log --pretty=oneline
	3de50f6d891bf7c7432edc2a959fd2fbfa9286b0 添加hello git
	8edeaca3329ec0b38a1a66b948dabd8cb6e7dfa8 update file
	d7d25794ef327789dae190d4ea9f65775d54d741 提交测试文件
	67a36db3844043c7f97910cb177c6631d2f1cd2b add php
	6e5f32ab6d7319274800f1d751e63fdc67008562 new file

我们可以直观的看到“更新内容”不见了，那要是我们后悔回退了，想再次返到最新的版本，该怎么办呢？我们只需要在窗口没关掉之前找到最新版本的commit id`45c00ae...`即可:
	
	$ git reset --hard 45c00ae
	HEAD is now at 45c00ae 更新内容

现在，就可以回到最新的版本了。**注意：这里只需输入commit id的前几位就ok。**

假如，我们已经关掉了窗口，又想到回到某个版本该怎么办呢？不用担心，Git是有后悔药给我们吃的啦。Git提供了一个命令`git reflog`用来记录你的每一次命令:

	$ git reflog
	45c00ae HEAD@{0}: reset: moving to 45c00a
	3de50f6 HEAD@{1}: reset: moving to HEAD^
	45c00ae HEAD@{2}: commit: 更新内容

此时，我们可以根据前面的commit id返回到想要的版本库，仿佛穿梭于历史之间，很酷吧。

**Git的版本回退速度非常快，因为Git在内部有个指向当前版本的HEAD指针，当你回退版本的时候，Git仅仅是把HEAD从指向“添加hello git”：**

---

**小结**：
	
HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。

回退前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。

要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。

	
