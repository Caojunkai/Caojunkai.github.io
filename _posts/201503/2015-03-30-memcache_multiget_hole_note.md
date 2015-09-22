---
layout : post
title : 缓存无底洞现象
category : Memcache
tags : Memcache
---
该问题由facebook 的工作人员提出的, facebook 在2010 年左右,memcached 节点就已经达
3000 个,缓存数千G 内容.

他们发现了一个问题，即由于memcache连接频繁，导致效率下降了。于是他们开始添加memcache节点，添加新节点后，发现因为连接频繁导致的问题,仍然存在,并没有好转,称之为”**无底洞现象**”. 具体原文在这:[Facebook Multiget Hole](http://highscalability.com/blog/2009/10/26/facebooks-memcached-multiget-hole-more-machinesmore-capacit.html)

<!--more-->

##Multiget Hole问题分析

以用户为例: user-133-age, user-133-name,user-133-height .....N 个key,
当服务器增多时,133号用户的信息也被散落在更多的节点。所以,同样是访问个人主页,得到相同的个人信息, 节点越多,需要连接的节点也越多.对于memcached 的连接数,并没有随着节点的增多,而降低. 于是无底洞问题出现.

##Multiget Hole现象解决方案

> 把某一组key,按其共同前缀,来分布在同一个节点上。

比如user-133-age, user-133-name,user-133-height 这3 个key（当然也可能有更多的key，这里只是举例说明）,在用分布式算法求其节点时,应该以**共同前缀**‘user-133’来计算,而不是以user-133-age、user-133-name、user-133-height来计算.

于是，三个关于个人信息的key都落在同一个节点上面，当访问个人主页时候，只需要连接一个节点即可，问题解决。

这里是官方的回应：[http://dormando.livejournal.com/521163.html](http://dormando.livejournal.com/521163.html "http://dormando.livejournal.com/521163.html")

---

###小结

事实上，非关系型数据库和传统的关系型数据库,并不是水火不容,两者在某些设计上,是可以相互参考的.
对于memcached, redis 这种kv 存储, key 的设计,可以参考MySQL 中表/列的设计.
比如在关系型数据库中: user 表下,有age 列,name 列,height列。对应在NoSQL中的key,可以用user:133:age = 23, user:133:name = ‘lisi’, user:133:height = 168来表示。

(end)

