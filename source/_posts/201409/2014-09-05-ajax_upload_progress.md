---
layout : post
title : Ajax实现带进度条文件上传
category : Ajax
tags : Ajax
date: 2014-09-05
---
很多情况下，当我们在网站上传文件至远程的服务器时，都会看到一个上传进度条来告诉我们上传的情况、以及是否上传成功，这样可以极好滴提升用户体验程度！

那么，这个花哨的进度条是如何做到的呢？html5给出了很好的特性来实现带进度条的Ajax上传文件机制。

废话不多说，直接上代码，留作笔记，以作备忘：

<!--more-->

**js代码：**

	<script type="text/javascript" charset="utf-8">
            function pic () {
                //创建FormData对象
                var fd = new FormData();
                //获取文件对象
                var pic = document.getElementById('pic').files[0];
                //把文件内容追加到form中
                fd.append('pic', pic);
                //创建Ajax引擎
                var xhr = new XMLHttpRequest();
                xhr.open('post', './upload.php', true);
                xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
                xhr.onreadystatechange = function () {	//指定回调函数
                    if (this.readyState == 4) {
                        //window.alert('上传成功！');
                        document.getElementById('p').innertText = this.responseText;
                    }
                };

                //利用xhr2的新标准，为上传过程添加一个监听函数,实现进度条上传效果
                xhr.upload.onprogress = function (event) {
                    if (ev.lengthComputable) {			//true时
                        var percent = 100 * ev.loaded / ev.total;

                        document.getElementById('bar').style.width = percent + '%';
                        document.getElementById('bar').innerText = parseInt(percent) + '%';
                    }

                   // console.log(event);
                };

                xhr.send(fd);
            }
        </script>

**html代码：**

	<!DOCTYPE HTML>
	<!--
    	Ajax 带进度条文件上传
	-->
	<html>
    <head>
        <meta http-equiv="content-type" content="text/html; charset=utf-8">
        <title>Fileapi</title>
        <style type="text/css">
            #one {
                width : 500px;
                height :20px;
                border = 1px solid green;
            }
            #bar {
                width : 0%;
                height : 100%;
                background-color:green;
            }
        </style>

    </head>
    <body>
        <input type="file" name="pic" value="" id="pic" onchange="pic();"><br />
        <div id="one"><div id="bar"></div></div>
        <p id="p"></p>
    </body>
	</html>

**php代码：**

	<?php
		/*
			服务器端测试打印
	    */
    	echo 'upload success';
	?>


---

(end)
