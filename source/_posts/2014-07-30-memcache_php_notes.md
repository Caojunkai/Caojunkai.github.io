---
layout : post
title : PHP程序操作Memcache笔记
category : PHP
tags : memcached
date: 2014-07-30
---
上一篇说明[telnet操作memcache](http://liyafeng.cn/memcache_telnet_notes/)时就提到有两中方式来操作memcache，一种是通过telnet工具，另一种是通过程序控制，下面简单总结下关于使用PHP程序来操作memcache步骤以及细节注意事项。

首先，准备工作是要先把`php_memcache.dll`扩展文件加载到php中，以下是简要步骤：

*	将`php_memcache.dll`文件拷贝到php的ext目录下。
*	修改`php.ini`文件，加载`php_memcache.dll`文件，添加`extension=php_memcache.dll`，注意前面不要有`；`号。
*	重新启动apache服务器

**注意：**

不同版本的php所使用的`php_memcache.dll`文件是不一样的，加载时要注意检查php版本，找到合适的`php_memcache.dll`文件，否则会出错。

<!--more-->


然后，需要启动memcache服务，在cmd控制台，输入以下命令启动memcache服务：

	memcache.exe -d start 
	或者
	memcache.exe -p port

下面是一个PHP程序写的操作memcache服务的事例代码：

	<?php
    /*
     *  PHP程序操作memcache服务的事例代码
     * */

    //实例化memcache对象
    $mem = new Memcache;

    //建立链接
    if (!$mem->connect('127.0.0.1', 11211)) {
        exit('与memcache服务建立链接失败');
    }

    //查看memcache版本
    echo '服务器版本信息：' .  $mem->getVersion() . '<br />';

    //准备测试数据
    $str = 'PHP is my love';    
    $num = 123456;
    $bool = true;
    $arr = array('PHP is my love', 123, array('Hanson', 'Liyaf'));
    Class Dog {
        Public $name = '';
        Public function __construct ($name) {
            $this->name = $name;
        }
    }
    $dog = new Dog('lucky_dog');

    //添加数据到memcache
    $res = $mem->add('s', $str, MEMCACHE_COMPRESSED, 120) && $mem->add('n', $num, MEMCACHE_COMPRESSED, 120) && $mem->add('b', $bool, MEMCACHE_COMPRESSED, 120) && $mem->add('a', $arr, MEMCACHE_COMPRESSED, 120) && $mem->add('o', $dog, MEMCACHE_COMPRESSED, 120);

    if (!$res) {
        echo '添加失败';
        die;
    } else {
        echo '添加成功';
    }
    
    //关闭链接
    $mem->close();
    
	?>

从内存中查询数据：

	<?php
    /*
     *  获取数据
     * */
    $m = new Memcache;

    $m->connect('localhost', 11211);

    //取出数据
    //string
    $str = $m->get('s');

    //number
    $num = $m->get('n');

    //bool
    $bool = $m->get('b');

    //array
    $arr = $m->get('a');

    //object
    $obj = $m->get('o');


    //print
    echo '字符串' . $str . '<br />'; 
    echo '数值' . $num . '<br />'; 
    echo 'bool值' . $bool . '<br />'; 
    echo '数组' . print_r($arr) . '<br />'; 
    echo '对象' . var_dump($obj) . '<br />'; 

    //关闭链接
    $m->close();

	?>

###细节：

当我们在添加数据时，使用函数：

	bool Memcache::add ( string $key , mixed $var [, int $flag [, int $expire ]] )

*	如果`$expire`设置为0，表示永不过期，只要mem不重新启动，就一直在内存中

*	如果`$expire`设置为秒数，则最大是30\*24\*3600

*	如果`$expire`希望保持时间超过30天，则可以使用**时间戳**：time()+天数\*24\*3600


(end)

---
_以下为补充内容_
###memcache生命周期

从数据放入memcache开始计时，直到时间到时就销毁，如果时间设置为0，则表示永不过期。

memcache数据被销毁的情况如下：

*	设置时间到。
*	重启memcache服务
*	重启memcache服务所在机器
*	delete删除或者flush销毁数据

###将session数据放入memcache服务中

*	修改php.ini配置文件

		设置session.save_handler=memcache		（可设置为files/user/memcache）
		session.save_path="tcp://127.0.0.0.1:端口"

*	重启apache服务器
*	正常使用$_SESSION超全局数组

**思考**

假如，不允许我们修改php.ini文件，我们可以通过ini_set()函数来处理session写入memcache这个功能。

代码：

	<?php
		ini_set("session.save_handler", "memcache");
		ini_set("session.save_path", "tcp://127.0.0.0.1:端口");

		//操作session代码
	?>

我们也可以通过ini_set()函数去动态的修改php.ini的其他设置，但不影响其他php页面，也不会去修改php.ini文件本身。


	


