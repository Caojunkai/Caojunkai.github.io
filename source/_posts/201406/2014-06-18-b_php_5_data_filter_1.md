---
layout : post
title : "PHP对数据库取出和插入数据进行过滤"
category : PHP
tags : PHP 递归 
date: 2014-06-18
---
首先，介绍个很有用的函数htmlspecialchars（），相信大家都不陌生吧，没错，这个函数就是把一些预定义的字符转换为HTML实体输出。

包括的预定义字符有：

    
    & （和号） 成为 &amp;
    " （双引号） 成为 &quot;
    ' （单引号） 成为 &#039;
    < （小于） 成为 &lt;
    > （大于） 成为 &gt;
    

当然，这里不仅仅只是为了介绍函数而总结的，那样就太没意思了...

有时候，当我们在从模型中取出想要的数据时，可能包含一些预定义的字符（比如css、js样式），这时，如果我们不对它们进行处理就很可能破坏掉前端的页面，造成意想不到的后果，所以，对数据进行转义是个很好的习惯。

<!--more-->

**如果只是单纯的是处理字符串，我们可以这样处理：**

    <?php   
        //对数据库取出的数据进行递归过滤  
        //$date默认为从数据库中取出的数据  
        $str = htmlspecialchars($date);   
    ?>  
    
**如果取出的数据是数组呢，怎么办奥？小意思，这样来...**

     <?php   
      //对数据库取出的数据进行递归过滤   
      //$date默认为从数据库中取出的数据  
       function htmlString ($date) {  
            if (is_array($date)) {  
                foreach ($date as $key => $value) {  
                $str[$key] = htmlString($value);    //递归调用函数  
                }  
            } else {  
                $str = htmlspecialchars($date);  
            }  
            return $str;  
        }  
    ?>  
    
**如果数据是对象呢，咋办？**

    <?php   
    //对数据库取出的数据进行递归过滤  
    //$date默认为从数据库中取出的数据  
    function htmlString ($date) {  
        if (is_object($date)) {  
            foreach ($date as $key => $value) {  
            $str->$key = htmlString($value); //递归调用函数  
            }  
        } else {  
            $str = htmlspecialchars($date);  
        }  
        return $str;  
    }  
    ?> 
    
**总结一下，对于不知道类型的数据，我们当然得进行分类考虑了，而且使用递归调用处理数据，直接上代码：**
    
    <?php   
    //对数据库取出的数据进行递归过滤  
    //$date默认为从数据库中取出的数据  
    function htmlString ($date) {  
        if (is_array($date)) {  
            //数组  
            foreach ($date as $key => $value) {  
                $_string[$key] = htmlString($value);    //递归  
            }  
        }elseif (is_object($date)) {  
            //对象  
            foreach ($date as $key => $value) {  
                $_string->$key = htmlString($value); //递归  
             }         
        } else {  
            //其他  
            $_string = htmlspecialchars($date);  
        }  
        return $_string;  
     }  
    ?>  
    

**当然,这是从数据库取出数据来进行过滤的，那当插入数据峙该如何处理呢？当然是递归转义字符了，使用的是addslashes()函数，直接上代码：**

    // 递归转义
    function _addslashes($arr) {
        foreach($arr as $k=>$v) {
            if(is_string($v)) {
                $arr[$k] = addslashes($v);
            } else if(is_array($v)) {  // 再加判断,如果是数组,调用自身,再转
                $arr[$k] = _addslashes($v);
            }
        }
        return $arr;
    }
    
*虽然，不是特别很有技术含量，不过，为了防止忘记这种过滤参数的方法，还是总结下来了:）*



    

    