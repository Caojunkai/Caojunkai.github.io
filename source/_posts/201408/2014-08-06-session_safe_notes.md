---
layout : post
title : SESSION安全性问题
category : PHP
tags : SESSION
date: 2014-08-06
---
一直以来很多开发者都认为php内置的session管理机制是具有一定的安全性，可以对一般的session攻击起到防御。事实上，这是一种误解，php团队只实现了一种方便有效的机制。具体的安全措施，应该有应用程序的开发团队来实施。


现在，我们来看下一个比较常规的针对session的攻击：


*	用户访问`http://www.example.org`，并且登录。

*	`example.org`的服务器设置指示客户端设置相关`cookie - PHPSESSID=12345`

*	攻击者这时访问`http://www.example.org/`,并且在请求中携带了对应的`cookie - PHPSESSID=12345`


<!--more-->

这样情况下，因为`example.orge`的服务器通过PHPSESSID来辨认对应的用户的，所以服务器错把攻击者当成了合法的用户。

整个过程的描述，请看下面的示例图：

![pic](http://ww2.sinaimg.cn/mw690/bd5a4d63jw1ej2tiwbwbjj20b405kgls.jpg)

当然这种攻击的方式，前提条件是攻击者必须通过某种手段固定，劫持或者猜测出某个合法用户的PHPSESSID。虽然这看起来难度很高，但是也不是不可能的事情。

###安全性的加强

有很多技术可以用来加强Session的安全性，主要思想就是要使验证的过程对于合法用户来说，越简单越好，然后对于攻击者来说，步骤要越复杂越好。当然，这似乎是比较难于平衡的，要根据你应用程序的具体设计来做决策。


最简单的居于HTTP/1.1请求包括请求行以及一些Host的头部：



	GET / HTTP/1.1

	Host: example.org





如果客户端通过PHPSESSID传递相关的session标识符，可以将PHPSESSID放在cookie头部中进行传递:

	GET / HTTP/1.1

	Host: example.org

	Cookie: PHPSESSID=12345





同样地，客户端也可以将session标识符放在请求的url中进行传递。

	GET /?PHPSESSID=12345

	HTTP/1.1Host: example.org





当然，session标识符也可以包含在POST数据中，但是这对用户体验有影响，所以这种方式很少采用。



因为来自TCP/IP信息也不一定可以完全信任的，所以，对于web开发者来说，利用TCP/IP中的信息来加强安全性也是不太合适的。 不过，攻击者也必须提供一个合法用户的唯一的标识符，才能假扮成合法用户进入系统。因此，看起来唯一能够有效的保护系统的措施，就是尽量地隐藏session标识符或者使之难于猜测出来。最好就是两者都能实施。



PHP会自动生成一个随机的session ID，基本来说是不可能被猜测出来的，所以这方面的安全还是有一定保障的。但是，要防止攻击者获取一个合法的session ID是相当困难的，这基本上不是开发者所能控制的。



事实上，许多情况下都有可能导致session ID的泄露。 比如说，如果通过GET数据来传递session ID的话，就有可能暴露这个敏感的身份信息。因为，有的用户可能会将带有session ID的链接缓存，收藏或者发送在邮件内容中。Cookies是一种像相对来说安全一点的机制，但是用户是可以在客户端中禁止掉cookies的！在一些IE的版本中也有比较严重的安全漏洞，比较有名的就是会泄露cookies给一些有安全隐患的邪恶站点。



因此，作为一个开发者，可以肯定session ID是不能被猜测出来的，但是还是有可能被攻击者使用某些方法获取到。所以，必须采取一些额外的安全措施来防止此类情况在你的应用程序中发生。



实际上，一个标准的HTTP请求中除了Host等必须包含的头部，还包含了一些可选的头部.举一个例子，看下面的一个请求：

	GET / HTTP/1.1
	Host: example.org
	Cookie: PHPSESSID=12345
	User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X; en-US; rv:1.8.1.1) Gecko/20061204 Firefox/2.0.0.1
	Accept: text/html;q=0.9, */*;q=0.1
	Accept-Charset: ISO-8859-1, utf-8;q=0.66, *;q=0.66
	Accept-Language: en

我们可以看到，在以上的一个请求例子中包含了四个额外的头部，分别是User-Agent, Accept, Accept-Charset以及Accept-Language。因为这些头部不是必须的，所以完全依赖他们在你的应用程序中发挥作用是不太明智的。但是，**如果一个用户的浏览器确实发送了这些头部到服务器，那么可以肯定的是在接下来的同一个用户通过同一个浏览器发送的请求中，必然也会携带这些头部。**当然，这其中也会有极少数的特殊情况发生。假如以上例子是由一个当前的跟服务器建立了会话的用户发出的请求，考虑下面的一个请求：

	GET / HTTP/1.1
	Host: example.org
	Cookie: PHPSESSID=12345
	User-Agent: Mozilla/5.0 (compatible; IE 6.0 Microsoft Windows XP)


因为有相同的session id包含在请求的Cookie头部中，所以相同的php session将会被访问到。但是，请求里的User-Agent头部跟先前的请求中的信息是不同的，系统是否可以假定这两个请求是同一个用户发出的？



像这种情况下，发现浏览器的头部改变了，但是不能肯定这是否是一次来自攻击者的请求的话，比较好的措施就是弹出一个要求输入密码的输入框让用户输入，这样的话，对用户体验的影响不会很大，又能很有效地防止攻击。



当然，你可以在系统中加入核查User-Agent头部的代码，类似Listing 3中的代码：

**Listing 3:**

	<?php
	session_start(); 
    
	if (md5($_SERVER['HTTP_USER_AGENT']) != $_SESSION['HTTP_USER_AGENT']) 
	{   /* 弹出密码输入框 */   exit;
	} 
    
	?>




当然，你先必须在第一次请求时，初始化session的时候，用MD5算法加密user agent信息并且保存在session中，类似下面listing4中的代码：

**Listing 4:**

	<?php 
    
	session_start(); 
    
	$_SESSION['HTTP_USER_AGENT'] = md5($_SERVER['HTTP_USER_AGENT']); 
    
	?>


虽然不一定需要用MD5来加密这个User-Agent信息，但使用这种方式以后就不需要再过滤这个`$_SERVER['HTTP_USER_AGENT']`数据了。不然的话，在使用这个数据以前必须要进行数据过滤，**因为任何来自客户端的数据都是不可信任的，必须要注意这一点。**



在你检查这个User-Agent客户端头部信息以后，做为一个攻击者必须要完成两步才能劫持一个session:

*	获取一个合法的session id

*	包含一个相同的User-Agent头部在伪造的请求中

你可能会说，居然攻击者能获得有效的session id,那么以他的水平，伪造一个相同的User-Agent不是件难事。不错，但是我们可以说这至少给他添加了一些麻烦，在一定程度上也增加了session机制的安全性。



你应该也能想到了，既然我们可以检查User-Agent这个头部来加强安全性，那么不妨再利用其它的一些头部信息，把他们组合起来生成一个加密的token，并且让客户端在后续的请求中携带这个token！这样的话，攻击者基本上不可能猜测出这样一个token是怎么生成出来的。这好比你用信用卡在超市付款，一个你必须有信用卡(好比session id)，另外你也必须输入一个支付密码(好比token),这有这两者都符合的情况下，你才能成功进入账号付款。 看下面一段代码：

	<?php 
	session_start(); 
	$token = 'SHIFLETT' . $_SERVER['HTTP_USER_AGENT'];
	$_SESSION['token'] = md5($token . session_id()); 
	?>


**注意**：Accept这个头部不应该被用来生成token,因为有些浏览器会自动改变这个头部，当用户刷新浏览器的时候。

在你的验证机制中加入了这个非常难于猜测出来的token以后，安全性会得到很大的提升。假如这个token通过像session id一样的方式来进行传递，这种情况下，一个攻击者必须完成必要的3步来劫持用户的session:

*	获取一个合法的session ID

*	在请求中加入相同的User-Agent头部,用与生成token

*	在请求中携带被攻击者的token

这里面有个问题。如果session id以及token都是通过GET数据来传递的话，那么对于能获取session ID的攻击者，同样就能够获取到这个token。所以，比较安全靠谱的方式应该是利用两种不同的数据传递方式来分别传递session id以及token。例如，通过cookie来传递session id,然后通过GET数据来传递token。因此，假如攻击者通过某种手段获得了这个唯一的用户身份标识，也是不太可能同时轻松地获取到这个token，它相对来说依然是安全的。



还有很多的技术手段可以用来加强你的session机制的安全性。希望你在大致了解session的内部本质以后，可以设计出适合你的应用系统的验证机制，从而大大的提高系统的安全性。毕竟，你是最熟悉当下你开发的系统的开发者之一，可以根据实际情况来实施一些特有的，额外的安全措施。

---

###总结

以上只是大概地描述了session的工作机制，以及简单地阐述了一些安全措施。但要记住，以上的方法都是能够加强安全性，不是说能够完全保护你的系统，希望读者自己再去调研相关内容。在这个调研过程中，相信你会学到很有实际使用价值的方案。

**参考**：[原文链接](http://blogread.cn/it/article/6004?f=wb)

（完）
