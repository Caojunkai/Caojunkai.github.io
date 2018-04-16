---
layout : post
title : Ajax使用jsonp实现跨域请求
category : Ajax
tags : Ajax Jsonp
date: 2014-09-03
---
Ajax使用XMLHttpRequest对象在无刷新页面的情况下对服务器端进行数据请求，她允许客户端javascript通过HTTP连接到Web服务器。不过，由于收到浏览器的限制，Ajax不允许实现跨域通信，如果尝试从不同的域请求数据，就会出现错误！

###同源策略

同源策略，它是由Netscape提出的一个著名的安全策略。现在所有支持JavaScript 的浏览器都会使用这个策略。所谓同源是指域名、协议、端口相同。当一个浏览器的两个tab页中分别打开来百度和谷歌的页面，当一个百度浏览器执行一个脚本的时候会检查这个脚本是属于哪个页面的，即检查是否同源，只有和百度同源的脚本才会被执行。

同源策略阻止从一个域上加载的脚本获取或操作另一个域上的文档。也就是说，受到请求的URL的域必须与当前 Web 页面的域相同。这意味着浏览器隔离来自不同源的内容，以防止它们之间的操作。

<!--more-->

###什么是jsonp

资料来自百度：

> JSONP是JSON with Padding的略称。它是一个非官方的协议，它允许在服务器端集成Script tags返回至客户端，通过javascript callback的形式实现跨域访问（这仅仅是JSONP简单的实现形式）。


###jsonp有什么用


由于同源策略的限制，XmlHttpRequest只允许请求当前源（域名、协议、端口）的资源，为了实现跨域请求，可以通过script的**src**标签实现跨域请求，然后在服务端输出JSON数据并执行回调函数，从而解决了跨域的数据请求。

因为，script标签的src属性并不被同源策略所约束，所以可以获取任何服务器上脚本并执行。


###jsonp的实现模式

	<!doctype html>
	<html>
    	<head>
        <meta http-equiv="content-type" content="text/html; charset=utf-8">
        <title>jsonp测试</title>
        <script type="text/javascript" charset="utf-8">
            function callback (data) {
                alert(data.message);
            }
         </script>
         <script type="text/javascript" charset="utf-8" src="jsonp.js"></script>
    	</head>
    	<body>
    	</body>
	</html>

程序jsonp.js中的代码：

	//调用callback函数，并以json数据作为参数传入，完成回调
	callback({message : 'success'});

这其实就是JSONP的简单实现模式：创建一个回调函数，然后在远程服务上调用这个函数并且将JSON 数据形式作为参数传递，完成回调。

###动态创建script标签

我们可以通过javascript动态的创建script标签，这样我们就可以灵活调用远程服务了。

客户端代码：

    <script type="text/javascript" charset="utf-8">
            function callback (data) {
                for (var i in data) {
                    window.alert(i + ":" + data[i]);    //循环输出
                }
            }

            function create_script () {
                var script = document.createElement("script");
                script.type = 'text/javascript';
                script.src = 'http://www.###.com/calljsonp.php?callback=callback';
                document.getElementsByTagName('head')[0].appendChild(script);
            }
     </script>

服务器端php代码：(calljsonp.php)

	<?php
    //服务器端返回json数据
    $arr = array('a' => '1', 'b' => '2', 'c' => '3');
    $json = json_encode($srr);
    //执行回调函数
    $callback = $_GET['callback'];
    echo $callback . "($json)";
	?>

这是一个google实现ajax搜索的接口url:[http://ajax.googleapis.com/ajax/services/search/web?v=1.0&q=?&callback=? ](http://ajax.googleapis.com/ajax/services/search/web?v=1.0&q=?&callback=? )

q=?这个问号是表示你要搜索的内容，最重要的是第二个callback=?这个是正如其名表示回调函数的名称，也就是将你自己在客户端定义的回调函数的函数名传送给服务端，服务端则会返回以你定义的回调函数名的方法，将获取的json数据传入这个方法完成回调。



###jsonp原理

1、首先在客户端注册一个callback, 然后把callback的名字传给服务器。

2、此时，服务器先生成 json 数据。
然后以 javascript 语法的方式，生成一个function , function 名字就是传递上来的参数 callback.

3、最后将 json 数据直接以入参的方式，放置到 function 中，这样就生成了一段 js 语法的文档，返回给客户端。

4、客户端浏览器，解析script标签，并执行返回的 javascript 文档，此时数据作为参数，传入到了客户端预先定义好的 callback 函数里.（动态执行回调函数）

---

###小结

Ajax使用jsonp进行跨域请求数据，其实说白了就是利用了javascript的一个特性，即src属性并不被同源策略所约束，所以可以获取任何服务器上脚本并执行。然后在服务器端以javascript的语法生成一个function，并把这个function返回到客户端。




