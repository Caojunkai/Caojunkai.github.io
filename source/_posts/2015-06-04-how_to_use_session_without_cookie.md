---
layout : post
title : 禁用COOKIE后如何访问SESSION问题
category : php
tags : php
date: 2015-06-04
---
最近看到一个有关在PHP中使用COOKIE会话管理的面试题，不单单是因为这道题很有意思，还有就是自己以前确实没有想到过这个方面（_知识欠缺_:P），所以这里做个记录总结下，该题大概是这样描述的:
	
	如何不使用cookie向客户端发送一个cookie?

刚看到这道题时确实懵了，愣是没看懂，后面在网上搜索了下，猜测出题人的意图估计是想解决这样的一个场景：

	当客户端端禁用COOKIE时，如何在本地保存COOKIE以及在发送HTTP请求时带上COOKIE信息。

<!--more-->

这样看来，好像有点眉目了，下面结合SESSION和COOKIE详细解释下如何解决这个问题。

我们知道，当在服务器端启用session时候，数据默认是以文件的形式保存服务器上的，其中session\_name(默认是PHPSESSID)和session\_id是保存在COOKIE中的，并发送到Client端。
此时，用户访问其他页面发送HTTP请求时候依然将cookie中保存的session\_name和session\_id带回服务器端，而服务器这时候同样启用了session，就会将session_id对应的文件中保存的数据反序列化并保存到`$_SESSION`数组中。整个流程大概是这样子的。

有一天，客户端无意将COOKIE禁用了，这时候再去服务器端访问上面流程的程序会出现这样的情况。服务器同样会保存session数据到文件中（默认），但是设置的cookie头信息却无法在客户端保存，后面再去访问其他页面时候客户端也就没有带上cookie中的数据去发送请求，然后服务器也就无法获取cookie信息，造成的后果就是上次创建的session文件成为了垃圾数据，而每次请求都要重新创建session文件，然后一直循环~~ 无法跟踪用户登录状态，造成不好的用户体验。

那么，该如何解决这个问题呢？

其实在PHP官方手册中有提到过这个问题，只怪自己没有自己研究手册。我们只需要在`php.ini`中配置`session.use_trans_sid=1`或者在编译PHP时打开`–enable-trans-sid`选项。这样，每当客户端禁用了COOKIE，在访问其他页面时会在URL上自动加上`SESSIONID=这里是session_id的字符串`传递到服务器端，而服务器端会自动解析URL中传递过来的SESSIONID信息，从而把该用户信息读取到`$_SESSION`数组中使用，一切看起来都是这么的正常，但是这其中有很多细节如果不注意的话还是会掉坑里的。

首先，要注意的是`php.ini`中两个选项默认是这样的设置：

	session.use_cookies=1	（开启使用cookie保存session会话信息）
	session.use_only_cookies=1 (注意：只使用cookie 来保存session信息)

所以。如果想要在浏览器开启cookie时候使用cookie保存会话信息，当浏览器禁用cookie时候使用url传递会话信息的话，就应该如下设置才会达到你想要的效果:

	在php.ini文件中修改
	session.use_trans_sid=1  //启用url传递会话信息
	session.use_only_cookies=0  //关闭只是用cookie保存会话信息
	session.use_cookies=1   //默认使用cookie保存会话信息

或者在php程序中设置:

	ini_set('session.use_trans_sid', 1);
	int_set('session.use_only_cookies', 0);
	ini_set('session.use_cookies', 1);

如果不管浏览器是否开启cookie，都使用url的方式传递会话信息，可以进行如下设置（这个例子主要想说明一下设置`session.use_only_cookies`和`session.use_cookies`的区别）

	在php.ini文件中
	session.use_trans_sid=1
	session.use_only_cookies=0
	session.use_cookies=0

或者在php程序中设置：

	ini_set('session.use_trans_sid', 1);
	ini_set('session.use_only_cookies', 0);
	ini_set('session.use_cookies', 0);


当然，除了上述方法方便解决该问题，还有如下解决方案可以参考：

>1. 手动URL传值
>
>2. 通过隐藏表单传递
>
>3. 在数据库中保存session_id, 然后手动调用


###在使用`session.use_trans_sid`时，php官方给出了安全风险警告，我们需要注意：

**_Note: 基于 URL 的会话管理比基于 cookie 的会话管理有更多安全风险。例如用户有可能通过 email 将一个包含有效的会话 ID 的 URL 发给他的朋友，或者用户总是有可能在收藏夹中存有一个包含会话 ID 的 URL 来以同样的会话 ID 去访问站点。_**


（end）

 

