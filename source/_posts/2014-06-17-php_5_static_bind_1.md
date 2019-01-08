---
layout : post
title : "PHP延迟静态绑定机制"
category : PHP
tags : [PHP]
date: 2014-06-17
---

自 PHP 5.3.0 起，PHP 增加了一个叫做后期静态绑定的功能，用于在继承范围内引用静态调用的类。准确说，后期静态绑定工作原理是存储了在上一个“非转发调用”的类名。当进行静态方法调用时，该类名即为明确指定的那个（通常在 ::  运算符左侧部分）；当进行非静态方法调用时，即为该对象所属的类。

该功能从语言内部角度考虑被命名为“后期静态绑定”。“后期绑定”的意思是说，static:: 不再被解析为定义当前方法所在的类，而是在实际运行时计算的。也可以称之为“静态绑定”，因为它可以用于（但不限于）静态方法的调用。

使用 self:: 或者 \_\_CLASS\_\_ 对当前类的静态引用，取决于定义当前方法所在的类： 

	<?php
 	class  A  {
    	public static function  who () {echo  __CLASS__ ;}
    	public static function  test () {self :: who ();}
	}
	class  B  extends  A  {
    	public static function  who () {echo  __CLASS__ ;}
	}
 	B :: test ();
 	?> 

<!--more-->

以上例程会输出： A

**后期静态绑定**

	<?php
 	class  A  {
    	public static function  who () {echo  __CLASS__ ;}
    	public static function  test () {
        	static:: who ();  // 后期静态绑定从这里开始
     	}
	}
	class  B  extends  A  {
    	public static function  who () {echo  __CLASS__ ;}
	｝
 	B :: test ();
 	?> 

该例会输出： B


我们都知道静态方法还可以用作工厂方法，下面给大家看一个代码实例：
	
	<?php  
	Class Parent {  
	    //父类的一些方法...  
	} 
	Class One extends Parent {  
   		Static Public function _create () {  
        	return new One();  
   	}  
	}  
	Class Two extends Parent {  
  		Static Public function _create () {  
        	return new Two();  
    	}  
	}  
	//......   
	print_r(One::_create());  
	print_r(Two::_create()); 
	?>  

在上面的父类Parent当中包含了很多的功能可被扩展类所调用，但是，他的两个派生类在创建自己的对象时使用了大量的重复代码，这是我们所不能忍受的，因为大量的重复代码很烦人也说明了你不是一个合格的coder。

笔者一直都坚信：简单的就是最好的。那么我们能不能把_create（）方法放在父类当中被公共使用呢？
来看下面的实例：

	<?php   
	Class Parent {  
    	//父类的一些方法...  
    	Static Public function _create () {  
        	return new self();  
    	}  
	}     
	Class One extends Parent {}  
	Class Two extends Parent {}  
	//......  
	print_r(One::_create());  
	print_r(Two::_create()); 
	?>  

这样仿佛我们就解决问题了，但是，由结果我们可以看出，并不是我们想要的One和Two类的实例对象，而是父类的实例，怎么回事呢？

因为self指的不是调用上下文，而是解析上下文。因此，self被解析为定义\_create()方法的Parent类。而不是解析为调用\_create方法的One和Two类的实例对象。

但是，在PHP中引入了延迟静态绑定机制，其实就是使用static关键字。而static类似于self和parent，但他指的是被调用的类不是包含类。

下面，我们来使用static进行改进:

	<?php    
	Class Parent {  
    	//父类的一些方法
    	Static Public function _create () {  
			 //这里使用static延迟静态绑定关键字static  
        	return new static();   
    	}  
	}      
	Class One extends Parent {}  
	Class Two extends Parent {}   
	//......   
	print_r(One::_create());  
	print_r(Two::_create());  
	?>  

由打印结果可以看出，确实生成了One和Two类的实例化对象，是不是省去了很多的重复代码，static确实很好用. :)
