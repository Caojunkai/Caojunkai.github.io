---
layout : post
title : jQuery对象与DOM对象相互转换
category : jQuery
tags : jQuery DOM
date: 2014-09-11
---
对于刚学习jQuery的童鞋来说，可能会一时分不清哪些是jQuery对象，哪些是DOM对象。而且初学者很容易以jQuery和DOM对象混搭的形式来进行js操作，这要求我们能够很好的进行jQuery和DOM对象之间转换，这可是很有必要的奥，下面简单来说明下。

首先，我们先定义好变量的风格，如果是jQuery对象，变量前就加上`$`符号：

	var $variable = jQuery对象

如果是DOM对象，则不加`$`符号：

	var variable = DOM对象

<!--more-->

###什么是jQuery对象？

简单解释就是通过jQuery包装DOM对象后产生的对象。JQuery对象的索引保存的是dom对象，所以可以通过索引将经jQuery对象转化为dom对象（**实际上是获取保存在jQuery对象中的DOM对象**）。



简单事例：
	
	$("#aabb").html();

意思是获取id为aabb的元素内的html代码，其中html()是jQuery中的方法。

	document.getElementById("aabb").innerHTML();

这个是通过DOM实现的与上面相同的示例代码。

###jQuery对象转换为DOM对象

只有jQuery对象才能调用jQuery类库的各种函数，同样有些dom对象的属性和方法在jQuery上也是无法调用的，不过基本上jQuery类库提供的函数包含了所有的dom操作。

jQuery提供了两种方法讲一个jQUery对象转换为DOM对象，即[index]和get(index)。由上面我们已经知道jQuery对象是一个数组对象，JQuery对象的索引保存的是dom对象，所以可以通过索引将经jQuery对象转化为dom对象。

* 方法一：通过[index]的方法得到相应的DOM对象

		var $a = $("test");		//jQuery对象
		var  b = $a[0];			//DOM对象

* 方法二：通过get(index)函数来得到相应的DOM对象

		var $a = $("test");		//jQuery对象
		var  b = $a.get(0);		//DOM对象


###DOM对象转换为jQuery对象

对于一个DOM对象，只要我们使用$()把DOM对象包装起来就可以得到一个jQuery对象，从而就可以任意使用jQUery类库中声明好的函数了，就这么简单。

代码：
	
	var bb = document.getElementById("test");	//DOM对象
	var $a = $(bb);								//jQuery对象

***注意：**

$(elements)中的elements参数还可以是jQuery对象，虽然将一个jQuery对象在次转化没有意义，这是为了当不确定一个对象的类型是jQuery对象还是dom对象时，可以再次调用此函数进行转化，这样可以保证此对象一定是jQuery对象。

---

（end）







