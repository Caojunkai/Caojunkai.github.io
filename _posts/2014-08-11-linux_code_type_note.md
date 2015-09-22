---
layout : post
title : Linux下语系编码问题总结
category : Linux
tags : Linux
---
我们都知道，我们操作系统的文件都会有一个编码格式[比如：utf8、gb2312...],但是很多情况下，我们会需要将现有文件的语系编码进行转换，比如将utf8的文件转换为gb2312格式，那该怎么办？难道我们需要将全部文件都打开，然后转存诚所需要的编码格式嘛？那样我们肯定会吐血...

`iconv`命令就会帮助我们做到，下面介绍下如何使用他来方便我们。

首先，我们要准备一个utf8格式的文件呢，来`file 文件`查看下：


	hanson@linux-host:~/Linux_Demo$ file iconv.f
	iconv.f: HTML document, UTF-8 Unicode text, with very long lines


可以看出`iconv.f`是一个utf8格式的文件。
<!--more-->


这里介绍下如何使用`iconv`这个很有用的格式转换的命令:

	参数：
	--list ：列出所有可被iconv转换的语系数据格式

	-f ： 后接旧的语系格式
	-t ： 后接新的语系格式
	-o file ： 如果想要保留原本的文件，可以使用-o new file来创建新的编码文件

废话不多说，来实地操作下：

	hanson@linux-host:~/Linux_Demo$ iconv -f utf8 -t gb2312 iconv.f -o iconv.f.gb2312

	下面查看下格式：
	hanson@linux-host:~/Linux_Demo$ file iconv*
	iconv.f:        HTML document, UTF-8 Unicode text, with very long lines
	iconv.f.gb2312: HTML document, ISO-8859 text, with very long lines


可以看出，将utf8格式的文件已经成功转换成gb2312格式的文件，是不是很方便啦。



