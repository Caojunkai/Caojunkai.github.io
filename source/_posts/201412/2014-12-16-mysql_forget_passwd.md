---
layout : post
title : MySQL的root密码丢失怎么办？
category : MySQL
tags : MySQL
date: 2014-12-16
---
当我们Linux服务器中的MySQL超级用户密码丢了或者忘记了（唉，真是脑残！），这时候我们应该咋办，下面给出解决方案，分分钟搞定你的烦恼。

首先，当我们在重新设置MySQL数据库密码期间必须确保此时服务器的安全状态，最安全的方法就是在服务器命令行操作，并且拔掉网线。

接下来，关闭MySQL服务;

	service mysqld stop
	//或者直接干掉进程
	pkill mysqld

<!--more-->

然后，我们使用`skip-grant-tables`绕过权限启动：

	/usr/bin/mysqld_safe --skip-grant-tables --user=mysql &

等几秒钟...

下面，我们直接使用无密码命令登录MySQL:

	mysql -u root

此时，奇迹般地绕过密码直接进入MySQL了。

好了，下面开始重新设置密码：

	update user set password=password('your password') where user='root' and host = 'localhost';

**注意：**

不要使用`set password=password('your pasword')`的方式来更改密码，会报错，因为使用了`skip-grant-tables`命令的缘故，切记。

到此，我们就重设root密码成功了，界面我们重启MySQL服务。

	pkill mysqld
	/usr/bin/mysqld_safe --user=mysql &  或者 service mysqld restart
	mysql -uroot -p your pasword

最终，我们已经给MySQL的root用户重新设置密码成功了，就这么回事，下面碰到这类问题就不是事儿了吧。

(end)