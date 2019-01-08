---
layout : post
title : PDO事务处理
category : PHP
tags : PHP PDO
date: 2014-06-27
---
事务是确保数据库一致的机制，作为一个单元的一组有序的数据库操作。在事务的组中哪怕有一个环节操作失败，事务也不成功，则整个事务将被回滚，该事务中的所有操作都将被撤销。只有组中的所有SQL语句都操作成功，则认为事务成功，事务才被提交。

下面，介绍几个PDO中要用到的关于事务处理的函数：

###`beginTransaction()`函数：

该方法来启动一个事务，标明了回滚起始点。如果底层驱动成程序不支持事务，那么PHP将会抛出一个PDOException的异常。

<!--more-->

###`commit()`函数：

该函数用来提交一个事务，并且执行SQl。

###`rollback()`函数：

该函数用来回滚一个事务。

---

下面来构建一个事务处理的程序：

在淘宝购物时，当我们选好一个价格为100元的宝贝，我们采用网银进行转账。假设用户A向B转账100元。首先在demo数据库准备好一个InnoDB引擎的数据表account。废话不多说，直接上代码：

	<?php
	//PDO事务处理练习
	$dsn = 'mysql:host=localhost;dbname=demo';
	$user = 'root';
	$pwd = 'password';
	$pdo = new PDO($dsn, $user, $pwd);
	$pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);      //设置异常处理模式
	$pdo->setAttribute(PDO::ATTR_AUTOCOMMIT, 0);                        //关闭自动提交

	//异常处理转账事务
	try {
    	$price = 100;
    	$pdo->beginTransaction();          //开启一个事务

    	$affected_rows = $pdo-exec("update account set cash=cash-{$price} where name='A'");     //转账

    	if ($affected_rows > 0) {
        	echo 'A转出{$price}成功';
    	} else {
        	throw new PDOException ('A转账失败');       //抛出异常
    	}

    	$affected_rows = $pdo-exec("update account set cash=cash+{$price} where name='B'");     //转入
    
    	if ($affected_rows > 0) {
        	echo 'B转入{$price}成功';
    	} else {
        	throw new PDOException ('B转入失败');       //抛出异常
    	}
    
    	echo '交易成功';
    	$pdo->commit();       //事务执行成功
	} catch (PDOException $e) {
    	echo '交易失败' .$e->getMessage();
    	$pdo->rollback();     //事务回滚
	}
	?>

这个事务处理的过程需要两条sql更新语句来一起合作完成，所以采用事务处理来确保两条sql语句对数据操作的一致性。一旦发生错误，则catch快就可以捕获异常并进行回滚事务发生的所有更改，并打印一条错误信息。
