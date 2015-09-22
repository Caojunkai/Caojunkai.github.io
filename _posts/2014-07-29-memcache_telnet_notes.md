---
layout : post
title : Telnet操作memcached服务
category : memcached
tags : telnet memcached
---
我们知道，操作memcached有两种方式，分别是通过telnet和php程序来控制，其中php程序控制是主要方面，主要包括三种类型，如下图：

![pic](http://ww2.sinaimg.cn/mw690/bd5a4d63jw1eiu65cgobnj208c07omxk.jpg)

<!--more-->

首先，我们要先在cmd控制台中开启memcached服务，命令如下：

	memcached.exe -d start

或者

	memcached.exe -p 端口号
	端口号可自行设置，只要不被占用即可


开启服务后，需要登录telnet连接到memcached服务，命令如下;
	
	telnet 127.0.0.1 11211

**注意：**

如果登录telnet'时报错检测不到应用程序，请在“控制面板--->程序--->打开或关闭windows程序中”勾选telnet客户端并点击确定。

下面总结下常用语法结构：

###增加

基本语法：
	
	add key名  0  存放时间  数据大小（字符）

例如：

	add key1 0 30 5

###获取元素

基本语法：

	get key名

例如：

	get key1

###修改

基本语法：

	set key名 0  存放时间  数据大小

或者

	replace key名 0  存放时间 数据大小

###删除

基本语法：

	delete key名

例如：

	delete key1

---

###小结

这里简单总结了通过telnet来操作memcached的基本方法，其实主要还是通过程序（比如php）来控制memcached服务的，这里了解就好。