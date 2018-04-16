---
layout : post
title : Sed数据处理
category : Linux
tags : sed linux
date: 2014-08-17
---
在Linux下有两个非常强大的数据处理工具，一个是sed，另一个是awk。这里暂且介绍sed如何使用好了，作为备忘笔记，以便以后查看。

sed本身是一个管道命令，可以分析stdin的，而且sed可以将数据进行替换、删除、新增、选取特定行的功能。这里要明白的是，与awk将一行数据分为“多个字段”来处理相比，sed常常作用于一整行的处理。

首先，先了解下sed工具的用法：

	hanson@linux-host:~$ sed [-nefir] [动作] 文件
	参数：
	    -n：安静模式，加上-n后只有经过sed特殊处理过的数据才会显示在屏幕上，不加-n很可能会造成重复输出数据
	    -e：直接在命令行模式上进行动作编辑
	    -f：将sed动作写入一个文件，-f filename 则可以执行文件内的动作
	    -r：sed的动作支持的是扩展型正则表达式语法
	    -i：直接修改读取的文件内容，而不是输出到屏幕

<!--more-->

	动作：
	    n1,n2 [function]：选取n1,n2之间的行进行动作操作
	
	function参数：
	    a：新增，会出现在下一行
	    i：插入，会出现在上一行
	    c：替换，c后面接字符串，可以替换n1,n2之间的行
	    s：替换，搭配正则表达式
	    d：删除，通常d后面不解参数
	    p：打印，一般与sed -n一起使用


##新增与删除

将passwd文件的2,5行删除：

	hanson@linux-host:~$ nl passwd  | sed '2,5d' 

**注意：**sed后跟的动作，必须在单引号之间。

在第一行的上一行新增加一行数据：

	hanson@linux-host:~$ nl passwd  | sed '1i This is a new line.'

当添加多行时，可以使用"\"来进行新行的增加：

	hanson@linux-host:~$ nl passwd  | sed '1a first line...\
	> second line...'

注意：当使用"\"进行新行添加时，"\"后面务必紧跟enter健。

##替换与显示

将2,5行替换为“2,5lines”：
	
	hanson@linux-host:~$ nl passwd  | sed '2,5c 2,5lines'

想要打印出5,10之间的数据呢？

	hanson@linux-host:~$ nl passwd  | sed '5,10p'
	笨的方法是：
	hanson@linux-host:~$ nl passwd  | head -n 10 | tail -n 5


##数据的替换

sed以行为单位进行数据的替换时，与vim相当类似，格式如下：

	sed 's/要被替换的字符串/新的字符串/g'

下面一个实例，查询出本机的ip地址：

	查看eth0:
	hanson@linux-host:~$ ifconfig eth0
	eth0      Link encap:以太网  硬件地址 08:9e:01:31:a4:8f  
		  inet 地址:192.168.1.102  广播:192.168.1.255  掩码:255.255.255.0
		  inet6 地址: fe80::a9e:1ff:fe31:a48f/64 Scope:Link
		  UP BROADCAST RUNNING MULTICAST  MTU:1500  跃点数:1
		  ...
	
	开始查询：
	hanson@linux-host:~$ ifconfig eth0 | grep 'inet 地址' | sed 's/^.*地址://g' | sed 's/广播.*$//g'
	192.168.1.102


##直接修改文件内容

sed可以直接进行文件的修改，而不用使用管道命令或者重定向，但这是一个危险操作，一定要注意：

	
	hanson@linux-host:~$ sed -i '$a #this is a new line.' passwd 
	hanson@linux-host:~$ cat passwd | tail -n 1
	#this is a new line.

sed 的"-i"参数可以直接修改文件的内容，这个功能非常有帮助，甚至不需要vim去修改。

---

更多关于sed使用讲解，请参考[左耳朵耗子]的[sed简明教程](http://coolshell.cn/articles/9104.html)


(end)
