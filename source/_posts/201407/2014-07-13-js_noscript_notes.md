---
layout : post
title : noscript元素
category : JavaScript
tags : js
date: 2014-07-13
---
今天，我把自己的浏览器脚本给禁用了，为了想看下他们是如何处理的，然后登录了一些网站：

百度帐号都无法登录，163邮箱无法登录也没给出任何的友好显示，淘宝、京东也是一团糟，新浪还好些，新浪邮箱给出了HTML文档提示：

![pic](http://ww1.sinaimg.cn/mw690/bd5a4d63tw1eib12a25aij20co049jrr.jpg)

潦潦看了一下，爆出的毛病都是些不友好的界面，大家都知道“一切input都是邪恶的，js验证只是给君子使用的”.
<!--more-->

早期浏览器都面临一个特殊的问题，就是当浏览器不支持js或者js脚本被禁用时如何让页面平稳的退化。对这个问题的最终解决办法是创建一个<noscript\>元素，用以在不支持js或禁用js脚本的浏览器中使用。这个元素能够包含出现在文档<body>中的任何html元素\-\-\-<script\>元素除外，<noscript\>元素只有在以下条件下才会显示出来：

*	浏览器不支持脚本
*	浏览器支持脚本，但脚本被禁用

符合以上任何一个条件，浏览器就会显示noscript中的内容，除此之外的任何情况下，都不会呈现noscript中的内容，下面看一个例子：

	<!DOCTYPE HTML>
	<html>
    	<head>
        <meta http-equiv="content-type" content="text/html; charset=utf-8">
        <title>Noscript</title>
        <script type="text/javascript" defer="defer" src="./js/noscript.js"></script>
    	</head>
    	<body>
        <noscript>
            <h1>本页面已禁用js脚本</h1>
        </noscript>
    	</body>
	</html>


这个页面会在脚本无效的情况下向用户显示一条信息，而在启用了脚本的浏览器中，用户永远也不会看到他，尽管他是页面的一部分。




