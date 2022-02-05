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
top_img: /img/PHP_BUG.jpg
cover: /img/PHP_BUG.jpg
---

# PHP

### \=\=和\=\=\=

== 是比较数值，不比较类型

```php
var_dump(998=="998"); true

var_dump(998=="998ab"); true

var_dump(0=="haha"); true

var_dump(123===123); true
```



mdpass的第一题

```php
md4 md5加密都有可能出现一个0e开头
for($i=1;;$i++){
    //$r = 0e1 0e2 ... 0e251288019
    $r = "0e".$i;
    //$md4 = 0e874956163641961271069404332409 
    $md4 = hash("md4",$r);
    echo '<br/>';
    echo $i;
    if(preg_match("/^0e[0-9]*$/", $md4)){
        echo "md4加密前".$r;
        echo "md4加密后".$md4;
        break;
    }
}
```

所以使用md4的漏洞机制

>0e251288019
>0e001233333333333334557778889

![image-20220121131916793](D:\Code\pojo\Blog\BlogHexo\public\img\PHPBug-2.png)

```php
 <?php
    error_reporting(0);
    highlight_file("pass-02.php");

    if(isset($_GET['user']) && isset($_GET['pass'])){
        $user = $_GET['user'];
        $pass = $_GET['pass'];

        if($user != $pass && md5($user) == md5($pass)){
            echo "success!<br>";
            echo file_get_contents('flag.txt')."\n";
            echo "<a href='pass-03.php'>下一关</a>";
        }else{
            echo "fail~~~";
        }

    }else{
        echo "please input the user and pass!"."\n";
    }
?> 
```

第二关是md5的

可以看出上面的要求我们用户名和密码不一样但是md5是一样的所以只要找出两个0e开头就可以了,下面这些随便挑两个不一样的就行

```
md5弱类型比较
IHKFRNS 0e256160682445802696926137988570
QLTHNDT 0e405967825401955372549139051580
QNKCDZO 0e830400451993494058024219903391
3908336290 0e807624498959190415881248245271
4011627063 0e485805687034439905938362701775
4775635065 0e998212089946640967599450361168
0e215962017 0e291242476940776845150308577824
aabg7XSs 0e087386482136013740957780965295
aabC9RqS 0e041022518165728065344349536299
```

或者使用数组跳过也可以

## ==因为数组md5后是null==

> /pass-02.php?user[]=1&pass[]=2



第三题

```php
 <?php
    error_reporting(0);
    highlight_file("pass-03.php");

    if(isset($_GET['user']) && isset($_GET['pass'])){
        $user = $_GET['user'];
        $pass = $_GET['pass'];

        if(!ctype_alpha($user) && !is_numeric($pass) && md5($user) == md5($pass)){
            echo "success!<br>";
            echo file_get_contents('flag.txt')."\n";
            echo "<a href='pass-04.php'>下一关</a>";
        }else{
            echo "fail~~~";
        }

    }else{
        echo "please input the user and pass!"."\n";
    }
?> 
```

用函数判断一个是纯英文一个是纯数字同样使用弱命令即可

> ?pass=IHKFRNS&user=3908336290



第四题

```php
<?php
    error_reporting(0);
    highlight_file("pass-04.php");

    if(isset($_GET['user']) && isset($_GET['pass'])){
        $user = $_GET['user'];
        $pass = $_GET['pass'];

        if($user != $pass && md5($user) == md5(md5($pass))){
            echo "success!<br>";
            echo file_get_contents('flag.txt')."\n";
            echo "<a href='pass-05.php'>下一关</a>";
        }else{
            echo "fail~~~";
        }

    }else{
        echo "please input the user and pass!"."\n";
    }
?>
```

因为user!=pass所以不能两个都是数组又因为pass被md5两次所以不能使用弱密码了

采用pass为数组,user为空即可

>?pass[]=1&user=

第四题和第五题一样



第六题比较严厉,只能遍历出弱比较了

```php
 <?php
    error_reporting(0);
    highlight_file("pass-06.php");

    if(isset($_GET['user']) && isset($_GET['pass'])){
        $user = (string)$_GET['user'];
        $pass = (string)$_GET['pass'];

        if($user != $pass && md5($user) == md5(md5($pass))){
            echo "success!<br>";
            echo file_get_contents('flag.txt')."\n";
            echo "<a href='pass-07.php'>下一关</a>";
        }else{
            echo "fail~~~";
        }

    }else{
        echo "please input the user and pass!"."\n";
    }
?> 
```

因为将类型转化为String所以array,null,INF,NAN都不行

> user=240610708&pass=aawBzC
>
> 
>
> 240610708---aawBzC

![image-20220121134720525](D:\Code\pojo\Blog\BlogHexo\public\img\PHPBug-3.png)

第七题还是用数组过滤



第八题涉及到反序列化漏洞

> auth=O:4:"auth":2:{s:4:"user";d:INF;s:4:"pass";d:INF;}

```php
 <?php
    error_reporting(0);
    highlight_file("pass-08.php");

    class auth{
        public $user;
        public $pass;

        public function __destruct(){

            $this->user = (string)$this->user;
            if(strlen($this->user) > 3 || strlen($this->pass) >3){
                echo "level1 is fail~~~";
            }
            if($this->user !== $this->pass && $this->user != $this->pass && md5($this->user) === md5($this->pass)){
                echo "success!<br>";    
                echo file_get_contents("flag.txt");
            }else{
                echo "level2 is fail~~~";
            }
        }
    }
    unserialize($_POST['auth']);
?> 
```

