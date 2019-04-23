---
layout : post
title : 性能优化工具–-XHProf安装与使用
category : php
tags : php
date: 2015-04-18
---
XHProf是Facebook开源出来的一个PHP性能测试工具，他是一个轻量级的分析工具，跟xdebug差不多，但比 xdebug 分析的信息更加详细， 包括函数的调用次数，花费的时间（自身花费时间和包含内部函数花费的时间），所占内存/CPU，所占内存的峰值及所占百分比等，而xdebug对性能影响较大，即便是开启了`profiler_enable_trigger`参数，cpu也会立马飙到峰值。

<!--more-->

关于如何下载、编译、安装、配置、使用、测试等的使用方法，这里贴出几篇比较好的博客供自己日后查看和备用，希望也可以方便到你们 :)

[http://www.chenglin.name/php/optimization/439.html](http://www.chenglin.name/php/optimization/439.html)

[http://www.cnblogs.com/bluefrog/archive/2012/03/01/2374922.html](http://www.cnblogs.com/bluefrog/archive/2012/03/01/2374922.html)

[http://www.cnblogs.com/casatwy/archive/2013/01/17/2865241.html](http://www.cnblogs.com/casatwy/archive/2013/01/17/2865241.html)

[http://www.onexin.net/php-practical-performance-analysis-tools-xhprof-facebook/](http://www.onexin.net/php-practical-performance-analysis-tools-xhprof-facebook/)

(end)

