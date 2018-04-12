---
layout : post
title : PHP生成扭曲验证码
category : PHP
tags : PHP
---
为了防止网站被机器人灌水，现在网站的各种验证码广泛存在，为安全性起到了一定的作用。

验证码的样式有很多样，这里笔者只是总结下扭曲验证码的制作过程及其原理介绍，也算是备忘的笔记吧。

###原理介绍

这里不再赘余生成验证码的一般步骤，只是说下当一般验证码已经制作好时，如何
进行扭曲加工的关键步骤。简单地说就是，对制作好却没有输出到浏览器的画布资源进行竖直单位像素的分割，然后利用正弦函数的波动特性进行扭曲。下面使用画图来进行更好的说明：

<!--more-->

![pic](http://ww4.sinaimg.cn/mw690/bd5a4d63jw1eibfukt1m7j20bg09qgm4.jpg)

利用php的循环语句，依次切割单位像素的画布复制给准备的目标资源，最后输出到浏览器就ok了。

废话不多说，直接上代码（关键步骤代码）：

	/*
		生成扭曲验证码[核心代码]
		sin(float $arg )  返回参数 arg 的正弦值。参数 arg 的单位为弧度。
		pi()或者M_PI取得圆周率PI值
		round(float $val , int $precision  = 0 )对浮点数进行四舍五入  

		$offset  设置最大波动像素
		$round    设置扭曲的周期

    */
    for ($i = 0; $i<$width; $i++) {
    	$offset = 2;		//最大波动几个像素，可更改
    	$round = 2;			//扭曲2两个周期，即4*PI，可更改

    	$pos_y = round(sin(($round * 2 * pi() / $width) * $i) * $offset, 1);		//pi()得出圆周率PI

    	imagecopy($dst, $image, $i, $pos_y, $i, 0, 1, $height);
    }


可通过调整$offset或者$round变量来进行设置。

（完）