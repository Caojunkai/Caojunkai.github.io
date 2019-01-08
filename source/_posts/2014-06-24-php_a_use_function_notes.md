---
layout : post
title : "关于PHP生成验证码字符串的一点思考"
category : PHP
tags : PHP内置方法
date: 2014-06-24
---
在PHP中创建验证码很方便，创建好验证字符串和使用GD库函数创建图像就ok了。这里不说明怎么去创建验证码，其中当我们来创建验证字符时，我们是怎么做到的呢？

下面比较下作者见到的两种方式来生成字符：

**第一种方式**

	<?php
	$str = 'abcdefghigkmnprstuvwxyABCDEFGHGKLMNPQRSTUVWXY3456789';	
	$n = 6;		//生成字符的个数
	$code = '';
	$length = strlen($str);
	for ($i=0; $i < $n; $i++) { 
		$code .= $str[mt_rand(0, $length-1)];
	}
	echo $code;
	?>

<!--more-->

**第二种方式**

	<?php
	$str = 'abcdefghigkmnprstuvwxyABCDEFGHGKLMNPQRSTUVWXY3456789';	
	$n = 6;		//生成字符的个数
	$str = str_shuffle($str);
	$code = substr($str, 0, $n);
	echo $code;
	?>


观察这两种方式，有没有觉得第二种方式很简洁、清晰呢，是的，在第一种方式中我们使用了for循环以及定义了额外临时变量。再看第二种方式，我们只是充分使用了PHP自带的`str_shuffle`（打乱字符串顺序）和`substr`（截取字符串）函数，就达到了我们想要的目的。

---

###作者的观点

学习某一种语言或者技术，我们不仅要学好其本身的语法与规范，还要充分利用好该语言或者技术自身的优点和捷径来达到我们的目的。比如，当我们费了很大力气终于完成某个功能，没准其内部已经提供好了方法或者接口，只等着我们来调用就ok了。
