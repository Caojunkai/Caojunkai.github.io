---
layout : post
title : Memcached缓存技术原理与安装
category : Memcached
tags : Memcached
date: 2014-07-12
---
Memcached 是以LiveJournal旗下Danga Interactive公司的Brad Fitzpatric为首开发的一款内存缓存软件。Memcached是一种缓存技术，它可以把数据放入内存，从而可以通过内存访问提高速度，memcached技术的关键是提速。

###为什么要使用Memcached

随着互联网的发展，传统的关系型数据库已经出现了瓶颈，无法满足我们的要求，例如：

1）**对数据库的高并发读写**

如果对关系型数据库进行高并发读写操作，它是不能承受的。

2）**对海量数据的处理**

对于大型的SNS网站，每天都有数百万的数据产生（例如微博），如果使用传统关系数据库进行查询数据，将会慢的难以忍受。

使用Memcached就可以很好的解决上述问题。

<!--more-->

###Memcached使用原理

Memcached是高性能的分布式内存缓存服务器，通过缓存数据库查询结果到内存，以后就可以直接访问内存，减少对数据库的访问次数，进而提高动态网站应用的速度和可扩展性。

当客户端与memcached建立连接后，接下来就是存储对象了，每个存储的对象都有一个唯一的标识符key与value对应相关联，通过key可以对对象进行存取操作，而对象实际上是保存在内存中的，所以速度比较快。

下面是memcached模型图：

![pic](http://ww2.sinaimg.cn/mw690/bd5a4d63tw1eiah17n5awj20dg0dcmy0.jpg)


**注意**： 为了提高性能，Memcached把数据存储在内存中。由于数据仅存在内存中，因次重启memcached或者主机都会导致数据丢失。另外，内存容量达到指定值后，就会使用LRU（least recently used）算法自动删除不使用的缓存。memcached本身是为了缓存而设计的服务器，因次并没有过多考虑数据的持久化问题。

###安装Memcached

这里只是介绍下在windows下是如何安装的。

步骤：

1、下载memcached安装软件

2、安装

进入cmd控制台，切换到memcached.exe文件夹下，执行命令：

	memcached.exe -d install

3、启动memcached

	memcached.exe -d start

4、停止服务

	memcached.exe -d stop

5、查看是否安装成功

	netstat -an

	TCP    0.0.0.0:11211          0.0.0.0:0              LISTENING

查看11211端口是否被监听，或者查看是否被memcached应用所监听：

	netstat -anb

	TCP    [::]:11211             [::]:0                 LISTENIN [memcached.exe]

可以看到，11211端口正被memcached应用程序所监听。


(完)