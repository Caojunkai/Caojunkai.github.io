---
layout : post
title : Shell脚本的执行方式区别
category : Shell
tags : Linux Shell
date: 2014-08-22
---
不同的Shell脚本执行方式会造成不一样的后果，尤其对bash的环境影响很大。shell脚本的执行方式除了（绝对路径、相对路径、bash、sh）执行外，还可以利用source来执行，那么他们之间有什么不同呢？

###利用直接执行的方式来执行脚本

当我们利用直接方式（绝对路径、相对路径）或者使用bash命令（或者sh）来执行script时，该script都会使用一个新的bash环境来执行脚本内命令，也就是说，使用该方式执行时，其实script实在其子进程的bash内执行的。那么，我们首先应该知道：**当子进程完成时，子进程内的各个变量或者操作将会结束而不会传回到其父进程中**，这一点很重要，我们得要先明白。

<!--more-->

下面举例说明：

这个脚本是让用户自己输入名字，并在bash中打印出来：

	#!/bin/bash

	PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
	export PATH
	read -p 'enther yout name ' name
	echo -e "your name is $name"


我们来执行下：

	hanson@linux-host:~/Linux_Demo/shell$ bash sh01.sh 

然后打印下name变量：
	
	hanson@linux-host:~/Linux_Demo/shell$ echo $name
		<== name变量怎么不存在了呢？
		
	
从结果中，我们可以看出name变量并不存在，明明已经输入了值了呢，怎么回事？

下面将程序绘制成相关性图来说明，当我们直接来执行时，系统会给予一个新的bash让我们来执行shell中的命令，因此，name变量其实是在子进程中内执行的。当shell执行完毕后，子进程内的所有数据都会被删除。所以，在父进程中输出name变量时，就看不出任何数据了。

![pic](http://ww1.sinaimg.cn/mw690/bd5a4d63jw1ejlb5ikov0j20kn07mdfz.jpg)


###利用source来执行脚本：在父进程中执行

如果使用source来执行脚本，那就不一样了。来看实例：

	hanson@linux-host:~/Linux_Demo/shell$ source  sh01.sh

	//print name

	hanson@linux-host:~/Linux_Demo/shell$ echo $name 
	hanson <== name变量有数值哦


竟然name生效了。因为source执行shell脚本是在父进程中执行的，可以用下图来说明，因此各项操作都会在原本的bash中生效。

![pic](http://ww4.sinaimg.cn/mw690/bd5a4d63jw1ejlb56iuxcj20k805a747.jpg)

这也是为啥不用注销系统而要让某些写入`~/.bashrc`内的变量生效时，需要使用`source ~/.bashrc`而不能使用`bash ~/.bashrc`的原因了。

(end)
