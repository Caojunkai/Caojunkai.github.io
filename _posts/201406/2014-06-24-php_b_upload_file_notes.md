---
layout : post
title : "PHP实现文件上传机制"
category : PHP
tags : upload
---
在Web开发中，经常需要将本地的文件上传到Web服务器上，也可以从服务器上进行文件的下载。文件的上传和下载十分的广泛，在PHP中可以接受来自几乎所有类型浏览器上传的文件，还允许对服务器的上传和下载进行控制。

上传文件过程：

* 本地浏览器客户端选择磁盘文件，并进行上传；
* 服务器接受并处理来自客户端上传的文件。

##客户端上传设置

最基本的方法，是使用html的表单选择本地文件并进行提交，在form表单中使用`<input type="file">`标签来选择本地文件。如果想要支持文件上传，还必须在form中加入`enctype`和`method`属性：

<!--more-->

* `enctype="multipart/form-data"`用来指明表单编码数据方式。让服务器知道我们要上传一个文件
* `method="POST"`必须使用POST的发送方法

下面是一个表单的样式：

	<!DOCTYPE html>
	<html lang="en">
	<head><title>上传文件</title></head>
	<body>
		<form action="upload.php" method="POST" enctype="multipart/form-data">
			选择文件：<input type="file" name="picture"><br />
			<input type="submit" value="提交">
		</form>
	</body>
	</html>


##服务器端PHP处理上传

当客户端提交上传文件给服务器后，剩下的就交给PHP来处理了。

首先，服务器端是把上传的文件和与文件相关的信息存储在$\_FILES这个超全局数组中的，其他的数据由$\_POST去接受，我们可以使用print\_r打印下$\_FILES这个数组。	
	
	
	Array
	(
    	[picture] => Array
        	(
            	[name] => 1_120809092822_7.jpg
            	[type] => image/jpeg
            	[tmp_name] => C:\Windows\Temp\php1A5D.tmp
            	[error] => 0
            	[size] => 11424
        	)

	)


下面说明代表的含义：

1、name：上传文件的原名称，带扩展名

2、type:说明了上传文件的主类型和子类型

3、tmp_name：代表的是文件被上传到服务器后临时存储的目录

4、size:代表上传文件的大小，单位是字节

5、error：代表上传文件后产生的错误信息：

* 0：表示上传成功
* 1：表示文件大小超过了PHP配置文件中upload_max_filesize限制的值
* 2：表示文件大小超过了html表单MAX_FILE_SIZE限制的值
* 3：表示文件被部分上传
* 4：表示没有上传任何文件


**函数is\_uploaded\_file()**

该函数判断指定的文件是否是通过HTTP POST上传的，如果是则返回true，否则则返回false；唯一的参数是$\_FILES数组中的tmp\_name变量。

**函数move\_uploaded\_file()**

文件上传成功后，会首先存储在服务器的临时目录内，可以使用该函数将上传的文件移动到指定的目录内，并且该函数可以判断上传的文件是不是合法的上传文件。其中第一个参数是$\_FILES数组找那个的tmp\_name变量，第二个参数是指定被移动到的文件。

下面是一个处理上传文件的简单实例：

	<?php 
	$file = $_FILES;
	if (is_uploaded_file($file['picture']['tmp_name'])) {
		move_uploaded_file($file['picture']['tmp_name'], './uploads/' . $file['picture']['name']);
	} else {
		exit('错误');
	}
	?>

---
###小结

这里只是简单总结了下上传文件的过程和原理，以及服务器是如何处理的，实际开发中肯定还是更复杂的配置和处理的。

	

