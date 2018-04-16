---
layout : post
title : PHP检测gif图片是否为动态图片
category : PHP
tags : php gif
date: 2014-10-15
---
当我们使用php自带的`getimagesize()`函数来判断一个gif图片是否为动态（动画）时，发现无法判别是否为动态的效果。本文介绍了php是如何判断gif图片是否为动画的方法，具体步骤如下：

经过GOOGLE搜索可以查到，gif动态图的文件中包含:`chr(0×21).chr(0xff).chr(0×0b).'NETSCAPE2.0'`。所以，我们就可以使用`fopen()`和字符串系列函数来检测文件中是否包含这些字符串就可以判断该gif图是否动态的。

<!--more-->

编码实现如下：

	<?php
	//检测一个图片是否为gif动态图   
	function check_gif ($path) {
    	$str = file_get_contents($path);		//获取全部文件内容
    	if (stripos($str, chr(0x21).chr(0xff).chr(0x0b).'NETSCAPE2.0')) {
     	   echo '是gif动态图';
    	} else {
    	   echo '不是gif动态图';
   		}
	}
	check_gif('./test.gif');

上述代码就已经可以检测一个gif图是否为动画的了，但是还是可以在优化的。

因为实际上 `chr(0×21).chr(0xff).chr(0×0b).'NETSCAPE2.0'` 只在文件头部出现，可以 `echo `来看看 ，但不是最头部，是在头部的某一个位置，所以，**严格来说需要读取一部分文件**，但不用全部，这样可以加快速度和节省内存。

经测试，读取1024字节足够了，因为此时读取的数据流中正好包含了 `chr(0×21).chr(0xff).chr(0×0b).'NETSCAPE2.0'`.

代码优化后：

	<?php    
	function check_gif ($path) {
    	$fh = fopen($path, 'rb');
    	$str = fread($fh, 1024);	//读取1024个字节
    	fclose($fh);
    	if (stripos($str, chr(0x21).chr(0xff).chr(0x0b).'NETSCAPE2.0')) {
    	    echo '是gif动态图';
    	} else {
    	    echo '不是gif动态图';
    	}
	}
	check_gif('./test.gif');

这样就可以很好的判断一个gif图是否为动画了，而且很高效。

（end）

	