---
layout : post
title : "PHP新增语法结构Traits"
category : PHP
tags : PHP traits
date: 2014-06-18
---
**面向对象**的优势在于类的复用，继承和多态都是对类进行复用，他们一个是类级别的复用，一个是方法级别的复用。其中提到继承必提组合，有什么区别呢。组合与继承都是提高代码可重用性的手段。通过总结，可以得出**继承是一种“是、像”的关系；而组合则是一种“需要”的关系**，利用这个“定律”可以很好的判断出是继承关系还是组合关系。

继承最大的优点就是扩展简单、代码简洁，但是缺点大于优点。而组合却很灵活，仅通过唯一接口与外界通信，耦合度低于继承，但是却增加了代码量。

如果既想要组合的灵活，又要继承的代码简洁，该怎么办呢？

<!--more-->

这是可以做到的，比如使用多重继承就可以实现。多重继承一个类会同时继承多个父类，组合两个父类的功能。但是多重继承过于灵活，可能会带来“*菱形问题*”,使模型变的复杂起来，因次大多数语言，都放弃了多重继承这一模型。

---

PHP5.4.0引入了新的语法结构Traits，实现了代码复用的方法。Traits 是一种为类似 PHP 的单继承语言而准备的代码复用机制，为了减少单继承语言的限制，使开发人员能够自由地在不同层次结构内独立的类中复用方法集，是出extends、implements外的另外一种扩展对象的方式。

下面总结下Traits的用法，先看下实例代码：
	
	<?php 
	trait traitName {
		function func_one () {/*code*/}
		function func_two () {/*code*/}
	}
	Class class_one {
		use traitName;
		/*code*/
	}
	Class class_two {
		use traitName;
		/*code*/
	}
	?> 

###优先级

优先顺序是来自当前类的成员覆盖了trait的方法，而trait则覆盖了被继承的方法。

	<?php
 	class  Base  {
    public function  sayHello () {
        echo  'Hello ' ;
    }
	}
	trait  SayWorld  {
    public function  sayHello () {
        parent :: sayHello ();
        echo  'World!' ;
    }
	}
	class  MyHelloWorld  extends  Base  {
    	use  SayWorld ;
	}
 	$o  = new  MyHelloWorld ();
 	$o -> sayHello ();
 	?>  
	输出：Hello World！

###多个trait

通过逗号分隔，在 use 声明列出多个 trait，可以都插入到一个类中。

	<?php 
	trait Hello {
		Public function sayHello () {
			echo 'Hello';
		}
	}
	trait World {
		Public function sayWorld () {
			echo 'World';
		}
	}
	Class Say {
		use Hello, World;

	}
	$a = new Say();
	$a->sayHello();
	$a->sayWorld();
	?>
	输出：HelloWorld


###冲突的解决

如果两个 trait 都插入了一个同名的方法，如果没有明确解决冲突将会产生一个致命错误。为了解决多个 trait 在同一个类中的命名冲突，需要使用 insteadof（*而不是的意思，可以这么理解*） 操作符来明确指定使用冲突方法中的哪一个。以上方式仅允许排除掉其它方法，as 操作符可以将其中一个冲突的方法以另一个名称来引入。

	<?php
 	trait  A  {
    	public function  smallTalk () {
        	echo  'a' ;
    	}
    	public function  bigTalk () {
        	echo  'A' ;
    	}
	}

	trait  B  {
    	public function  smallTalk () {
        	echo  'b' ;
    	}
    	public function  bigTalk () {
        	echo  'B' ;
    	}
	}

	class  Talker  {
 	   use  A ,  B  {
    	     B :: smallTalk  insteadof  A ;
    	     A :: bigTalk  insteadof  B ;
    	}
	}

	class  Aliased_Talker  {
    	use  A ,  B  {
      	    B :: smallTalk  insteadof  A ;
      	    A :: bigTalk  insteadof  B ;
     	    B :: bigTalk  as  talk ;
    	}
	}
 	?> 

###修改方法的访问控制

使用 as 语法还可以用来调整方法的访问控制。


	<?php 
 	trait  HelloWorld  {
    	public function  sayHello () {
        	echo  'Hello World!' ;
    	}
	}
 	// 修改 sayHello 的访问控制
 	class  MyClass1  {
    	use  HelloWorld  {  sayHello  as protected; }
	}
 	// 给方法一个改变了访问控制的别名
	// 原版 sayHello 的访问控制则没有发生变化
 	class  MyClass2  {
    	use  HelloWorld  {  sayHello  as private  myPrivateHello ; }
	}
	$a = new MyClass1();
	$a->sayHello();		//输出错误
	$b = new MyClass2();
	$b->sayHello();		//输出Hello World!
	$b->myPrivateHello();		//输出出错
	?>


###从 trait 来组成 trait

正如类能够使用 trait 一样，其它 trait 也能够使用 trait。在 trait 定义时通过使用一个或多个 trait，它能够组合其它 trait 中的部分或全部成员。

	<?php 
	trait Hello {
		function sayHello () {echo 'Hello';}
	}
	trait World {
		function sayWorld () {echo 'World';}
	}
	trait HelloWorld {
		use Hello, World;
	}

	Class Say {
		use HelloWorld;
	}

	$a = new Say();
	$a->sayHello();
	$a->sayWorld();
	?>

###Trait 的抽象成员

为了对使用的类施加强制要求，trait 支持抽象方法的使用。

	<?php 
	trait HelloWorld {
		function hello () {echo 'Hello';}
		abstract function world ();
	}
	Class Say {
		use HelloWorld;
		function world () {		//实现抽象方法
			echo 'World!';
		}
	}
	?>

###Trait 的静态成员

Trait可以被静态成员静态方法定义。

	<?php 
	trait HelloWorld {
		Static public function say () {echo 'Hello,World';}
	}
	Class Say {
		use HelloWorld;
	}
	Say::say();
	?>

###属性

Trait 同样可以定义属性,如果 trait 定义了一个属性，那类将不能定义同样名称的属性，否则会产生一个错误。

	<?php 
	trait A {
		Public $a = 'Hello World';
	}
	Class B {
		use A;
	}
	$b = new B();
	echo $b->a;
	?>

**总结：**

在笔者看来，Traits的用法在某些方面是跟类的继承很相似的，学习的时候可以对比着来使用，只是Traits使用起来更加的灵活方便，减少了语言单继承的限制，使得应用类的成员不需要继承。：)