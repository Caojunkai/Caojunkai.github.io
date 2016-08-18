---
layout : post
title : "Linux下搭建PHP环境"
category : PHP
tags : Ubuntu PHP
---
下面分别介绍使用`apt-get`方式在Linux下搭建PHP+Mysql+Apache运行环境，如果是root权限的话可不加`sudo`,普通用户需要使用`sudo`获取临时root权限来进行安装：

###安装Mysql

	sudo apt-get install mysql-server
	sudo apt-get install mysql-client

###安装Apache

	sudo apt-get install apache2

<!--more-->

###安装PHP

	sudo apt-get install php5
	sudo apt-get install libapache2-mod-auth-mysql
	sudo apt-get install php5-mysql

到此，基本就已经安装成功了，下面进行一些测试与权限修改：

**检测Mysql是否正常**

在终端中输入：`mysql -uusername -ppassword `(将username和password替换为你所设置的)看是否可以正常登陆

**检测Apache是否正常**

在浏览器中打开：http://localhost/

如果出现如下信息，则表明正常。

	It works!

**检测PHP是否正常**

Ubuntu下Apache的默认安装路径为/var/www/html/，到其目录下新建test.php文件，文件内容为：

	<?php
		phpinfo();
	?>

然后在浏览器中打开：http://localhost/test.php 看是否正常,若能显示PHP版本等其他模块一些信息则代表正常。

**注意：**

在默认情况下，该/var/www目录是有权限的，只能是root管理员进行修改和创建，
我们可以把权限修改下：

	su -

输入root用户密码，然后进入/var目录下，输入：

	chmod -R 0777 ./www/

切回普通用户：

	su - username

查看权限：

	ls -al

普通用户也就有权限了。
