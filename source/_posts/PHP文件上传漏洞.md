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

# PHP upload labs

这里用了upload-labs靶场

第一题没啥好说的,关闭js然后就可以上传了



第二题

暂时不看代码首先测试上传1.php

然后用burp修改1.php为1.PHP3再把content-type:image/jpeg---上传成功

然后我们查看源码



```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        if (($_FILES['upload_file']['type'] == 'image/jpeg') || ($_FILES['upload_file']['type'] == 'image/png') || ($_FILES['upload_file']['type'] == 'image/gif')) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH . '/' . $_FILES['upload_file']['name']            
            if (move_uploaded_file($temp_file, $img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '文件类型不正确，请重新上传！';
        }
    } else {
        $msg = UPLOAD_PATH.'文件夹不存在,请手工创建！';
    }
}
```

根据源码可以发现上传类型要改成image/jpeg就可以了



第三题

使用PHP3首先进行修改名称来跳过过滤，并且Content-Type: image/jpeg

和第二题差不多



第四题我们首先尝试了和第三题一样的做法，发现无法上传。然后我们上传`.htaccess`

发现可以上传并且没有被重命名所以可以尝试图片注入的方式（不过这种上传方式会导致所有图片都没法使用）

![image-20220118203922248](D:\Code\pojo\Blog\BlogHexo\public\img\upload-1.png)

`.htaccess`的内容如上图

然后我们再看下源码:

```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        $deny_ext = array(".php",".php5",".php4",".php3",".php2","php1",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2","pHp1",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf");
        $file_name = trim($_FILES['upload_file']['name']);
        $file_name = deldot($file_name);//删除文件名末尾的点
        $file_ext = strrchr($file_name, '.');
        $file_ext = strtolower($file_ext); //转换为小写
        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
        $file_ext = trim($file_ext); //收尾去空

        if (!in_array($file_ext, $deny_ext)) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH.'/'.date("YmdHis").rand(1000,9999).$file_ext;
            if (move_uploaded_file($temp_file, $img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '此文件不允许上传!';
        }
    } else {
        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
    }
}
```

可以看出他屏蔽了很多的后缀所以我们必须改后缀了



第五题

提交一个1.PHP3. . . . 然后发现他去掉了后缀名和所以再来尝试双写发现不行,然后重新试了一下PHP发现可以....==事实证明你要一步一步的证明你的猜想不能急==



第六题提交一个`1.PHP  `直接成功

第七题提交了一个1.PHP.直接成功

第八题上传一个1.php::$DATA直接成功

第九题上传`1.php.  .`后成功

第十题上传1.php后发现只剩1.了所以采用双写发现成功绕过

第十一题请求一个php发现存在savePath所以可以采用%00截断

第十二题发现GET变成了POST所以在传输%00的时候要将%00选中右击Convert Selection->URL->URL-decode

十三题到十五题是文件包含漏洞

首先上传一个,包含gif头文件的1.gif内容是payload,然后使用include去包含这个文件

![image-20220118221823774](D:\Code\pojo\Blog\BlogHexo\public\img\upload-2.png)



