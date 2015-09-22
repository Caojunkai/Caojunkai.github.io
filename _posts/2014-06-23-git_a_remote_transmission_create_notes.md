---
layout : post
title : "Git远程仓库配置"
category : Git
tags : Git
---
首先，我们要了解[Github](https://github.com/)这个神奇的网站，这个网站就是提供Git仓库托管服务的，所以，只要注册一个GitHub账号，就可以免费获得Git远程仓库。所以我们要先注册一个github帐号。

由于你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，下面介绍一下具体的配置：

第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），输入命令创建SSH Key：

	$ ssh-keygen -t rsa -C "youremail@example.com"

<!--more-->

你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可.

如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。


第2步：登陆[Github](https://github.com/)，打开“Account settings”，“SSH Keys”页面,
然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容,下面是图示：

![图片](http://ww4.sinaimg.cn/mw690/bd5a4d63gw1ehobvqxm5rj20s70gydjg.jpg)

点击`Add key`就ok了。

下面使用命令`ssh -T git@github.com`来查看是否设置成功：

	$ ssh -T git@github.com
	Hi YourName! You've successfully authenticated, but GitHub does not provide shell access.

为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。

当然，GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

**最后友情提示：**在GitHub上免费托管的Git仓库，任何人都可以看到喔（但只有你自己才能改）。所以，不要把敏感信息放进去。