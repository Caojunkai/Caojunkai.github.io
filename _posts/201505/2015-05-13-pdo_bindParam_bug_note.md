---
layout : post
title : 关于在PDO中使用bindParam绑定参数的陷阱
category : php
tags : php
---
今天，在看鸟哥[@laruence](http://weibo.com/laruence)的个人博客时看到了有关在PHP中使用PDO的bindParam绑定方法结合循环语句来使用时会产生一个`BUG`或者小陷阱，这里做个笔记备忘 :)

来自PHP官方的BUG报告在这里：[#63281](https://bugs.php.net/bug.php?id=63281)

<!--more-->

究其原因, 也就是bindParam、bindColum和bindValue的不同之处, bindColum和bindParam要求第二个参数是一个**引用变量(reference)**，这才是关键点。

具体介绍可以戳这里：[Laruence：PDOStatement::bindParam 的一个陷阱](http://www.laruence.com/2012/10/16/2831.html)

