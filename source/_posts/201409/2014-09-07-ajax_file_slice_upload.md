---
layout : post
title : Ajax大文件切割上传
category : Ajax
tags : Ajax
---
对于我们这波电脑控的90后来说，在网上上传照片、压缩文件、视频等等已经是常见的事了。但是，不知道大家有没有疑问，就是说我们可以上传只有几兆的图片、稍大一点的十几兆的打包文件，甚至于几十兆或者几百兆的文件，更有甚者是上G的大视频文件，是不是可以无限制的上传更大的数据文件呢？！

![pic](http://ww3.sinaimg.cn/mw690/bd5a4d63jw1ek3t011i4mj20b003waac.jpg)

答案正如你所想的，肯定是否定的嘛。服务器端怎么可以任由你上传无限大的文件呢，一般服务器端都会设置最大上传文件的大小来进行限制，那么当我们需要上传大文件时该如何实现呢？

<!--more-->

这里我们使用的是打文件切割技术来进行分块上传，然后在服务器端接受切割后的小文件进行一系列的合并，最后将打文件成功上传到服务器端，这就是其基本原理。

###思路：

* 设置文件切割大小，这里假设为10M
* 使用定时器进行函数调用
* 判断文件是否截取完毕


废话不多说，直接上代码：
**js代码**

	 <script type="text/javascript" charset="utf-8">
        //全局对象
        var xhr = new XMLHttpRequest();
        var clock = null;
        var mov = null;
        //事件监控函数
        function fire () {
            mov = document.getElementsByTagName('input')[0].files[0];
            clock = window.setInterval(sendfile, 1000);
        }
        //闭包计数器
        var sendfile = function () {
            const LENGTH = 10 * 1024 * 1024;    //10M
            var sta = 0;                        //切割开始位置
            var end = sta + LENGTH;             //切割结束位置
            var sending = false;                //标志正在上传
            var blob = null;                    //用来存储切割数据
            var fd = null;                      //表单对象声明

            //返回一个匿名函数
            return (function () {
                        if (sending == true) {
                            return;
                        }
                        //如果sta > mov.size ,就结束
                        if (sta > mov.size) {
                            window.clearInterval(clock);
                            return;
                        }
                        blob = mov.slice(sta, end);         //切割
                        fd = new FormData();                //定义表单对象

                        fd.append('part', blob);
                        up(fd);                             //开始上传

                        sta = end;
                        end = end + LENGTH;
                        sending = false;    //上传完毕

                        percent = 100 * end / mov.size;
                        if (percent > 100) {
                            percent = 100;
                        }
                        document.getElementById('bar').style.width = percent + '%';
                        document.getElementById('bar').innerHTML = parseInt(percent) + '%';
                    });
        }();

		//上传函数
        function up (fd) {
           xhr.open('POST', './upload.php', false);     //同步传输
           xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
           xhr.send(fd);
        }      
        </script>

**html代码**

	<head>
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
        <input type="file" name="pic" value="" id="pic" onchange="fire();"><br />
        <div id="one"><div id="bar"></div></div>
    </body>

**服务器端代码**

	<?php
    /**
	 *  测试代码
     *  服务器接收文件并合并
     * */   
    if (isset($_FILES['part']['tmp_name'])) {
        if (!file_exists('./upload/up.wmv')) {
            move_upload_file($_FILES['part']['tmp_name'], './upload/up.wmv');
        } else {
            file_put_contents('./upload/up.wmv', file_get_contents($_FILES['part']['tmp_name']), FILE_APPEND);
        }
    }
	?>

---

###知识点：

1、闭包处理

2、匿名函数

3、Blob对象的slice方法

4、表单FormData对象使用

5、XMLHttpRequest对象（Ajax引擎对象）

6、js的window.setInterval方法

**注意：**

*有时间的话，比较下setTimeout方法和setInterval方法的区别与适用范围？*

(END)







