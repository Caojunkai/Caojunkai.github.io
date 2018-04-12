---
layout : post
title : 网站在线支付总结
category : PHP
tags : 在线支付
---
最近，在做一个购物商城的项目时，其中购买商品用到了关于网站在线支付的技术，就好好研究了下关于网站在线支付的流程与实现原理以及一些特别需要注意的地方。特此总结，备忘，以便查阅！

大家都知道，在电子商务告诉发展的今天，网上购物已经成为大多数消费者最直接、最便利的消费方式了，但是消费者要想达到真正的“足不出户”，网站在线支付就派上用场了。

<!--more-->

在线支付是指卖方与买方通过互联网上的电子商务网站进行交易时，银行为其提供网上资金结算服务的一种业务。它为企业和个人提供了一个安全、快捷、方便的电子商务应用环境和网上资金结算工具。在线支付不仅帮助企业实现了销售款项的快速归集，缩短收款周期，同时也为个人网上银行客户提供了网上消费支付结算方式（摘自[百度百科](http://baike.baidu.com/link?url=7KptIGkIWdw2j7T0S41j91BcrCJLxRpTiaSdOeE7LGt-Yp_nllQFb9z2DqLTYucc)）。


###在线支付方式

*	**直接与银行对接**

![pic](http://ww1.sinaimg.cn/mw690/bd5a4d63jw1eisvnftccgj20if06mt95.jpg)


**优点**：因为直接与银行进行财务结算，交易资金结算比较安全。适合资金流量比较大的企业，这种方案适合于每月结算金额佰万以上的企业。

**缺点**：开发工作量比较大，而且银行会不定期升级交易系统，随着银行系统的升级，企业也需要作 相应改动，所以维护工作量也是比较大的，而且企业每年还需要向银行交纳一定数量的接口使用费。

*	**第三方支付**

![pic](http://ww3.sinaimg.cn/mw690/bd5a4d63jw1eisvy668vpj20jf08ot9y.jpg)


**优点**：开发工作量较少，因为使用的是中间企业提供的接入规范，所以银行升级系统，不需要企业作相应修改，除非中间企业的接入规范发生了改变，企业才作相应修改。相对 前一种接入方案，这种方案的维护工作量比较少的。因为只与一家企业对接，所以接入费用相对比较低。这种方案适合于每月结算金额在几十万以下的中小企业。

**缺点**：因为是与中间企业进行资金结算，目前所有中间企业都是私企，资金安全是个大问题。


###在线支付流程

1、首先，商家需要先去第三方支付平台申请注册，获取商户编号以及设置密钥（用于网站和支付平台对接加密使用）。

2、当客户将商品加入购物车并且点击结算时，后台需要进行商品信息的处理，比如：生成获取商户编号、商品信息、生成hmac签名字符串、指定支付成功后的重定向url地址等等，将处理好的信息按照第三方支付平台的官方文档格式放入表单的隐藏域（切记：必须严格遵守第三方的文档格式编写，否侧会出错！）。

3、将表单隐藏域中的信息提交给第三方支付平台进行处理，如果有任何错误，都会返回失败，全部通过，才会成功！

4、重定向页面的信息处理，当支付成功后，根据第三方重定向后返回的信息进行商品发货以及判断是否为恶意网站请求（可通过签名字符串）。

###什么是签名字符串

签名字符串，顾名思义，就是一堆字符串，只不过该字符串的生成是有严格要求的。必须严格按照官方文档的说明，将一些信息（比如：商品编号、业务编号、币种、密钥等等）以字符串的形式拼接起来，再经过加密算法生成签名字符串。

签名字符串是为了防止恶意伪造表单信息进行http请求而用来加密和验证客户合法身份的一种有效方式，其中最重要的就是密钥了，密钥存在于商家和第三方手中，可随时进行更改。由于加密字符串前加入了密钥，所以当与第三方对接时，即使被恶意用户篡改了表单信息，但由于恶意用户不知道密钥，又因为加密字符串的不可逆，所以一旦表单被篡改，第三方就会立马检测出来即使阻止，有效保证了购物的安全性。

###代码实例

该事例使用的是易宝支付平台，详细请参考官方文档。


表单输入订单号和金额（测试使用，可以填写0.01）

        <form method="POST" action="payConfirm.php">
        <table border="0">
            <tr><td colspan="4">订单号：<input type="text" name="p2_Order">支付金额：<input type="text" name="p3_Amt"></td></tr>
            <tr><td colspan="4">请选择银行</td></tr>
            <tr>
                <td><input type="radio" name="pd_FrpId" value="CMBCHINA_NET">招商银行</td>
                <td><input type="radio" name="pd_FrpId" value="ICBC-NET">工商银行</td>
                <td><input type="radio" name="pd_FrpId" value="ABC-NET">农业银行</td>
                <td><input type="radio" name="pd_FrpId" value="CCB-NET">建设银行</td>
            </tr>
            <tr><td colspan="4"><input type="submit" value="确认支付"></td></tr>
        </table>
        </form>

确认信息，以及进行一些信息的处理和生成签名字符串，还有将信息放入表单隐藏域中进行提交。

	<!DOCTYPE HTML>
	<html>
    <head>
        <meta http-equiv="content-type" content="text/html; charset=gb2312">
        <title>支付测试</title>
    </head>
    <body>
        <?php
            /**
            获取提交信息
            */
            $p0_Cmd = 'Buy';                    //业务编号
            $p1_MerId = ***;                    //商户编号
            $p2_Order = $_REQUEST['p2_Order'];  //商户订单号
            $p3_Amt = $_REQUEST['p3_Amt'];      //支付金额
            $p4_Cur = 'CNT';                    //交易币种
            $p5_Pid = '';                       //商品名称，没有可为空
            $p6_Pcat = '';                      //种类
            $p7_Pdesc = '';                     //商品介绍
            $p8_Url = 'http://localhost:80/pay/res.php';   //回调url，支付成功后，给这个url返回信息
            $p9_SAF = 0;                        //是否需要送货地址
            $pa_MP = '';                        //商户扩展信息
            $pd_FrpId = $_REQUEST['pd_FrpId'];  //支付通道编码
            $pr_NeedResponse = 1;               //是否需要应答机制

            /**
              hmac 是一个签名串，是易宝和商家互相确认的关键字，需要使用算法来生成（md5-mac）
             */
            //顺序不可错，切记
            $data = "";
            $data .= $p0_Cmd;
            $data .= $p1_MerId;
            $data .= $p2_Order;
            $data .= $p3_Amt;
            $data .= $p4_Cur;
            $data .= $p5_Pid;
            $data .= $p6_Pcat;
            $data .= $p7_Pdesc;
            $data .= $p8_Url;
            $data .= $p9_SAF;
            $data .= $pa_MP;
            $data .= $pd_FrpId;
            $data .= $pr_NeedResponse;

            //密钥
            $merchantKey = ***;

            //获取hmac签名串
            $hmac = hash_hmac("md5", $data, $merchantKey);
            
            
        ?>

    您的订单号是:<?php echo $p2_Order;?> 支付的金额是：<?php echo $p3_Amt;?>

    <!--表单的信息放到hidden中-->
    <form action="https://www.yeepay.com/app-merchant-proxy/node" method="POST">
        <input type="hidden" name="p0_Cmd" value="<?php echo $p0_Cmd;?>" />
        <input type="hidden" name="p1_MerId" value="<?php echo $p1_MerId;?>" />
        <input type="hidden" name="p2_Order" value="<?php echo $p2_Order;?>" />
        <input type="hidden" name="p3_Amt" value="<?php echo $p3_Amt;?>" />
        <input type="hidden" name="p4_Cur" value="<?php echo $p4_Cur;?>" />
        <input type="hidden" name="p5_Pid" value="<?php echo $p5_Pid;?>" />
        <input type="hidden" name="p6_Pcat" value="<?php echo $p6_Pcat;?>" />
        <input type="hidden" name="p7_Pdesc" value="<?php echo $p7_Pdesc;?>" />
        <input type="hidden" name="p8_Url" value="<?php echo $p8_Url;?>" />
        <input type="hidden" name="p9_SAF" value="<?php echo $p9_SAF;?>" />
        <input type="hidden" name="pa_MP" value="<?php echo $pa_MP;?>" />
        <input type="hidden" name="pd_FrpId" value="<?php echo $pd_FrpId;?>" />
        <input type="hidden" name="pr_NeedResponse" value="<?php echo $pr_NeedResponse;?>" />
        <input type="hidden" name="hmac" value="<?php echo $hmac;?>" />
        <input type="submit"  value="确认提交">
    </form>
    </body>
	</html>


重定向url（省略了判断签名字符串）
	
	<!DOCTYPE HTML>
	<html>
    <head><meta http-equiv="content-type" content="text/html; charset=gb2312"><title>支付测试</title></head>
    <body>
        <?php
            //res
            echo '支付成功';
        ?>
    </body>
	</html>

以上模拟了使用易宝第三方支付平台进行在线支付。

---

###小结

*	发送GET或者POST请求都行
*	页面是GBK或者GB2312编码的
*	发送的参数请求不是自己随便起的,是接入规范中定义好的
*	密钥只能商家和第三方知道
*	生成签名字符串时是严格按照官方文档拼接的
*	**切记：**必须严格按照官方文档规范进行接入

	


