---
layout : post
title : "Web部署UEditor编辑器实现"
category : 前端
tags : [UEditor]
date: 2014-06-18
---
当我们部署自己的博客或者其他内容管理系统站点时，都不可或缺的需要发布内容的编辑器来实现编写内容的功能模块，而网站内容发布编辑器有很多，下面介绍下UEditor的使用：

**首先** 到官网下载最新的UEditor发布版本，[下载链接](http://ueditor.baidu.com/website/download.html)

**其次** 解压缩下载包到本地的文件夹

**然后** 在html页面中添加js代码

	<js file="路径/ueditor/ueditor.config.js" />
	<js file="路径/ueditor/ueditor.all.min.js" />

<!--more-->

**再然后** 设置编辑器，并添加编辑器的实例化代码：

	<script type="text/javascript">
		window.UEDITOR_HOME_URL = '路径/ueditor/';
		window.onload = function () {
			window.UEDITOR_CONFIG.initialFrameHeight = 600;	//可更改
			window.UEDITOR_CONFIG.initialFrameWidth = 1200; //可更改
			UE.getEditor('content');
		}
	</script>

**最后** 如果看到以下这样的编辑器，说明已经部署成功：

![图片](http://ww1.sinaimg.cn/mw690/bd5a4d63gw1ehkj3953ltj20yf043wfq.jpg)


____
_下面是压缩包解压缩后的目录_

![图片](http://ww3.sinaimg.cn/mw690/bd5a4d63gw1ehkj36avaej208j07nwf3.jpg)




