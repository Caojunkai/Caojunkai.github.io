---
layout : post
title : Centos源码编译安装LNMP环境
category : LNMP
tags : LNMP
---
这里总结下如何在Centos下利用源代码编译安装LNMP环境。

首先，说明下我这里的安装环境：

	Centos 6.5(VMware下测试)
	笔记本一台

然后，我们先配置下本地测试服务器：

	配好IP、DNS、网关，确保可以'远程'连接服务器

<!--more-->

	配置防火墙，开启80和3306端口
	vim /etc/sysconfig/iptables
	-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
	-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
	//重启防火墙，使配置生效
	services iptables restart

接下来，关闭SELINUX

	vim /etc/selinux/config
	#SELINUX=enforcing       #注释掉 
	#SELINUXTYPE=targeted    #注释掉 
	SELINUX=disabled         #增加 
	shutdown -r now   #重启系统

下面下载软件包：
	
>* Nginx
>* Pcre
>* MySQL
>* PHP
>* Cmake
>* Libmcrypt

安装编译工具以及库文件，可以使用yum安装：

	yum install make apr* autoconf automake curl-devel gcc gcc-c++ zlib-devel openssl openssl-devel pcre-devel gd  kernel keyutils  patch  perl kernel-headers compat* mpfr cpp glibc libgomp libstdc++-devel ppl cloog-ppl keyutils-libs-devel libcom_err-devel libsepol-devel libselinux-devel krb5-devel zlib-devel libXpm* freetype libjpeg* libpng* php-common php-gd ncurses* libtool* libxml2 libxml2-devel patch freetype-devel

###安装cmake

	tar zxvf cmake-2.8.8.tar.gz 
	cd cmake-2.8.8 
	./configure 
	make && make install  

###安装MySQL

	roupadd mysql  #添加mysql组    
	useradd -g mysql mysql -s /bin/false  #创建用户mysql并加入到mysql组，不允许mysql用户直接登录系统    
	mkdir -p /data/mysql  #创建MySQL数据库存放目录    
	chown -R mysql:mysql /data/mysql   #设置MySQL数据库目录权限    
	mkdir -p /usr/local/mysql #创建MySQL安装目录    
	cd /root/lnmp/ 
	cd mysql-5.5.25    
	cmake  -DCMAKE_INSTALL_PREFIX=/usr/local/mysql  -DMYSQL_DATADIR=/data/mysql  -DSYSCONFDIR=/etc   #配置    
	make && make install  #编译和安装    
	cd /usr/local/mysql    
	cp ./support-files/my-huge.cnf  /etc/my.cnf   #拷贝配置文件（注意：如果/etc目录下面默认有一个my.cnf，直接覆盖即可）    
	vi /etc/my.cnf   #编辑配置文件,在 [mysqld] 部分增加下面一行    
	datadir = /data/mysql  #添加MySQL数据库路径    
	./scripts/mysql_install_db --user=mysql  #生成mysql系统数据库    
	cp ./support-files/mysql.server  /etc/rc.d/init.d/mysqld  #把Mysql加入系统启动    
	chmod 755 /etc/init.d/mysqld   #增加执行权限    
	chkconfig mysqld on  #设置开机启动    
	vim /etc/rc.d/init.d/mysqld  #编辑    
	basedir = /usr/local/mysql   #MySQL程序安装路径    
	datadir = /data/mysql  #MySQl数据库存放目录    
	service mysqld start  #启动    
	vim /etc/profile   #把mysql服务加入系统环境变量：在最后添加下面这一行    
	export PATH=$PATH:/usr/local/mysql/bin      
	shutdown -r now     #需要重启系统，等待系统重新启动之后继续在终端命令行下面操作    
	mysql_secure_installation    #设置Mysql密码    
	根据提示按Y 回车（默认密码为空）    
	然后输入2次密码    
	继续按Y 回车，直到设置完成    
	或者直接修改密码/usr/local/mysql/bin/mysqladmin -u root -p password "123456" #修改密码    
	service mysqld restart  #重启    
	到此，mysql安装完成！   
	
###安装pcre
	
	cd pcre-8.36
	./configure  --prefix=/usr/local/pcre 
	make && make install   

###安装nginx

	cd /usr/local/src    
	groupadd  www  #添加www组    
	useradd -g  www www -s /bin/false  #创建nginx运行账户www并加入到www组，不允许www用户直接登录系统    
	tar  zxvf nginx-1.2.0.tar.gz    
	cd nginx-1.2.0    
	./configure --prefix=/usr/local/nginx --without-http_memcached_module --user=www --group=www --with-http_stub_status_module --with-openssl=/usr/ --with-pcre=/usr/local/src/pcre-8.36   
	#注意:--with-pcre=/usr/local/src/pcre-8.36指向的是源码包解压的路径，而不是安装的路径，否则会报错 
	make && make install   
	#设置开机启动nginx服务器
	vim /etc/profile
	添加下面一句话
	/usr/local/nginx/sbin/nginx


###安装libmcrypt

	tar zxvf  libmcrypt-2.5.8.tar.gz
	cd  libmcrypt-2.5.8 
	./configure 
	make && make install   #编译安装 

###安装php

	cd php-5.4.12
	./configure --prefix=/usr/local/php5 --with-config-file-path=/usr/local/php5/etc --with-mysql=/usr/local/mysql --with-mysqli=/usr/local/mysql/bin/mysql_config --with-mysql-sock=/tmp/mysql.sock --with-gd --with-iconv  --with-zlib  --enable-xml --enable-magic-quotes --enable-safe-mode --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curlwrappers --enable-mbregex  --enable-fpm --enable-mbstring --enable-ftp --enable-gd-native-ttf --with-openssl --enable-pcntl --enable-sockets --with-xmlrpc --enable-zip --enable-soap --without-pear --with-gettext --enable-session --with-mcrypt --with-curl --with-jpeg-dir --with-freetype-dir 
	make   #编译    
	make install    #安装    
	cp  php.ini-production   /usr/local/php5/etc/php.ini  #复制php配置文件到安装目录    
	rm -rf /etc/php.ini   #删除系统自带配置文件    
	ln -s /usr/local/php5/etc/php.ini  /etc/php.ini    #添加软链接    
	cp  /usr/local/php5/etc/php-fpm.conf.default   /usr/local/php5/etc/php-fpm.conf      #拷贝模板文件为php-fpm配置文件    
	vi  /usr/local/php5/etc/php-fpm.conf  #编辑    
	user = www    #设置php-fpm运行账号为www    
	group = www   #设置php-fpm运行组为www    
	pid = run/php-fpm.pid    #取消前面的分号    
	cp /usr/local/src/php-5.4.12/sapi/fpm/init.d.php-fpm   /etc/rc.d/init.d/php-fpm  #设置 php-fpm开机启动，拷贝php-fpm到启动目录    
	chmod +x /etc/rc.d/init.d/php-fpm  #添加执行权限    
	chkconfig php-fpm on    #设置开机启动    
	vi /usr/local/php5/etc/php.ini    #编辑配置文件    
	找到：disable_functions =    
	修改为：disable_functions = passthru,exec,system,chroot,scandir,chgrp,chown,shell_exec,proc_open,proc_get_status,ini_alter,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server,escapeshellcmd,dll,popen,disk_free_space,checkdnsrr,checkdnsrr,getservbyname,getservbyport,disk_total_space,posix_ctermid,posix_get_last_error,posix_getcwd, posix_getegid,posix_geteuid,posix_getgid, posix_getgrgid,posix_getgrnam,posix_getgroups,posix_getlogin,posix_getpgid,posix_getpgrp,posix_getpid, posix_getppid,posix_getpwnam,posix_getpwuid, posix_getrlimit, posix_getsid,posix_getuid,posix_isatty, posix_kill,posix_mkfifo,posix_setegid,posix_seteuid,posix_setgid, posix_setpgid,posix_setsid,posix_setuid,posix_strerror,posix_times,posix_ttyname,posix_uname   
	#列出PHP可以禁用的函数，如果某些程序需要用到这个函数，可以删除，取消禁用。    
	找到：;date.timezone =    
	修改为：date.timezone = PRC   #设置时区    
	找到：expose_php = On    
	修改为：expose_php = OFF  #禁止显示php版本的信息    
	PS：在编译PHP的过程中可能会报UNDEFINED REFERENCE TO `LIBICONV_OPEN 无法编译PHP LIBICONV错误.

###配置nginx支持php

	vi /usr/local/nginx/conf/nginx.conf      #编辑配置文件    
	user   www  www;          #首行user去掉注释,修改Nginx运行组为www www；必须与/usr/local/php5/etc/php-fpm.conf中的user,group配置相同，否则php运行出错    
	index  index.php  index.html index.htm;    #添加index.php    
	# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000    
	#    
	location ~ \.php$ {    
   		root           html;#此处和server下面root保持一致,默认为html    
   		fastcgi_pass   127.0.0.1:9000;    
   		fastcgi_index  index.php;    
   		fastcgi_param  SCRIPT_FILENAME   /usr/local/nginx/html/$fastcgi_script_name;    
   		include        fastcgi_params;     
	注意：取消FastCGI server部分location的注释,并要注意fastcgi_param行的参数,改为/usr/local/nginx/html(此为网站根目录绝对路径)$fastcgi_script_name
	pkill nginx	
	/usr/local/nginx/sbin/nginx #重启nginx
	
###测试

	cd /usr/local/nginx/html/
	echo <?php phpinfo(); > ./info.php

打开浏览器，输入：`192.168.1.110/info.php`,显示如下界面，说明环境搭建成功。

![](../../images/201412/2014-12-10_173100.jpg)

---

到此,Centos的LNMP环境已经搭建成功，其中很多部分参考了网络资料，不为别的，只作为本人练习笔记，仅此而已。

(end)


	
	