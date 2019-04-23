---
layout : post
title : 如何正常转换中文json格式字符串
category : php
tags : php
date: 2015-07-09
---
如何对json格式的字符串进行转换，在php手册中提供了两个函数：

> json_encode : 对变量进行 JSON 编码

> json_decode : 对 JSON 格式的字符串进行编码

<!--more-->

下面看个实例：

	$arr = ['a', 'b', 'c'];
	echo json_encode($arr);

输出为：

>["a","b","c"]

但是，当我们在数组中放入中文的时候，就会出现问题：

	$arr = [
    	'世界',
    	'你好',
	];
	echo json_encode($arr);

输出的结果为：

>["\u4e16\u754c","\u4f60\u597d"]

这个结果显然不是我们想要的，但是为什么出现这个呢？
因为当我们的值中包含中文时，php对他进行json编码时底层会对中文进行unicode编码，导致结果不可读.

_**那该怎么解决呢？**_

###方法一
可以利用 urlencode 和 urldecode 方法绕过这个转码为 unicode 的过程，先将中文字段进行urlencode，然后json_encode，最后再用urldecode处理结果，便可以正常显示中文。具体代码如下：

	$arr = [
    	'世界',
    	'你好',
	];
	echo urldecode(json_encode(array_map('urlencode', $arr)));

输出结果为：

>["世界","你好"]

###方法二
自PHP5.4版本，官方就已经给Json新增了一个选项: `JSON_UNESCAPED_UNICODE`。加上这个选项后，就不会自动把中文编码了。具体代码如下：

	$arr = [
    	'世界',
    	'你好',
	];
	echo json_encode($arr, JSON_UNESCAPED_UNICODE);

输出为：
	
>["世界","你好"]

看吧，这才是我们想要的结果 :)

###**注意**
需要我们注意的是，由于 `json_encode` 和 `json_decode`只支持`utf-8`编码的字符，`gbk`的字符想要用json函数的话就得转换一下喽。

[end]