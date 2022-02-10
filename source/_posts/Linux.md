---
title: Linux
date: 2022-01-09 13:28:03
tags:
      - Linux
      - 服务器
top_img: /img/Linux.jpeg
cover: /img/Linux.jpeg
---


 # Linux



## 系统启动

从RBM读取Boot Loader然后进行3个启动选择，再选择不同的boot loader来加载内核

>但是！因为Winodws的loader不具有控制权转交的功能，即不能使用windows的loader来加载Linux的loader！所以需要先装windows在装linux。这样MBR里面，先装的windows的loader才会被后装的Linux的boot loader所覆盖，使用Linux的loader的选单功能，才能够启动双系统
>
>
>
>所以安装双系统的时候先安装Windows再安装Linux







如果Linux的boot loader获取控制权，进而会将内核根据boot loader加载到内存再对硬件设备进行再一次检测，一般内核文件放在/boot/vmlinuz

```bash
[root@YH boot]# ls --format=single-column -F
config-3.10.0-1160.11.1.el7.x86_64							#此版本内核被编译时选择功能与模块的配置文件
efi/											
grub/														#旧版grub
grub2/														#启动程序相关目录
initramfs-0-rescue-3a6d3fd1db2b4ab6983389e8c068011e.img		#用来恢复的
initramfs-3.10.0-1160.11.1.el7.x86_64.img					#正常启动会用到的
initramfs-3.10.0-1160.11.1.el7.x86_64kdump.img				#内核出问题时用到
symvers-3.10.0-1160.11.1.el7.x86_64.gz						
System.map-3.10.0-1160.11.1.el7.x86_64						#内核功能放置到内存地址的对应表
vmlinuz-0-rescue-3a6d3fd1db2b4ab6983389e8c068011e*			#恢复用的内核文件
vmlinuz-3.10.0-1160.11.1.el7.x86_64*						#内核文件
```

Linux内核可以通过动态加载内核模块,这些内核放在/lib/modules目录==不可与内核放在不同的硬盘分区==

>如果你使用SATA但是内核不认识SATA，所以需要SATA磁盘的驱动否则无法使用但是SATA放在/lib/modules内,所以根本无法获取驱动,这时就要用到虚拟文件系统
>
>这个一般是一个文件,可以被boot loader来加载到内存中而这个文件再内存中会被模拟成一个跟目录,这些一般都是磁盘的驱动程序
>
>文件名通常为/boot/initrd或/boot/initramfs
>
>详细内容可以使用man initrd来查看
>
>lsinitrd /boot/initramfs-3.10.0-1160.11.1.el7.x86_64.img查看文件内容


## 各种设备在linux的文件名

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190226160828364.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdV9zaXNp,size_16,color_FFFFFF,t_70)



>在上图中的[a-p]表示从a字母到p字母的罗列例如，/dev/sda /dev/sdb ..... /dev/sdp
>
>注意以后会有很多[]



## Linux安装与设置

#### GPT分区表和MBR分区表的区别

http://www.360doc.com/content/18/0614/22/6140124_762487520.shtml

#### 挂载的含义

>将真实的存储设备与对应的文件进行绑定即为挂载，所在的目录即为挂载点
>
>文件系统与目录树结合的操作我们称为[挂载]

### 安装模式与启动

>如果磁盘容量小于2TB的话，系统默认会使用MBR分区表来安装

#### 初次安装建议

- 建议一,只划分"/"以及"交换分区"
- 建议二,预留一个备份的剩余磁盘容量
- 建议三,选择Linux安装程序提供的默认硬盘分区方式
- 建议四,使用usb3.0时可能会将该设备识别为硬盘,设置启动项时一定要注意.
- 建议五,如果硬盘大小小于2TB默认采用MBR分区
- 建议六,如果没有采用MBR而是采用GPT则需要创建bios boot分区
- 如果不想安装下载文件可以选择LiveCD等Live系列
- 如果想练习可使用最小安装版本(minimal)来安装



#### 关于centos名称解读:

centos-7-x86_64-Everything-1503-01.iso

- 7表示大版本
- x86_64代表64位操作系统
- Everything代表全功能版本
- 1503代表是2015年3月发布
- 01代表属于centos7.1

### 磁盘分区

- **标准分区**：类似/dev/vda1
- **LVM**：一种可以弹性增加或缩小文件系统容量的分区
- **LVM精简配置**：相当于LVM的高级版

#### 文件系统选项

- **ext2/ext3/ext4**：Linux早期使用的文件系统,ext3/ext4文件系统多了日志功能，对于系统的恢复比较快速。不常用了，嘤嘤嘤！
- **swap**：磁盘模拟为内存的交换分区,不会挂载到树目录,交换分区不需要指定挂载点
- **BIOS Boot**：GPT分区表可能会用到的东西若使用MBR就用不到了
- **xfs**：centos7默认的文件系统，对于大容量磁盘管理很好,格式化很快
- **vfat**：同时支持Windows和Linux系统，可以进行数据交换



> 安装系统后所有的root密码等都会记录到/root/anaconda-ks.cfg中;



## 命令部分

### 切换X Windows 与 命令行模式

>Ctrl + Alt + F1 **切换到X Windows窗口**
>Ctrl + Alt + F2~F6 **切换到tty2~tty6命令行模式**

***如果安装centos7时默认的是命令行界面，那么tty1~tty6会被命令行界面占用***

**centos7环境下，启动完成默认提供一个tty，需要切换时才产生额外的tty**

### 一般的命令格式：

```
    command [-options] parameter1 parameter2
```

- command 一般是命令或者可执行文件
- 如果命令符太长了可以使用\（反斜杠，嘤嘤嘤）来连接
- Linux中，***英文大小写字母是不一样的***

### 如果输入命令乱码

可能时Linux检测为中文编码修改为英文即可

> [root@YH ~]# local
> -bash: local: can only be used in a function
> [root@YH ~]# locale
> LANG=en_US.utf8
> LC_CTYPE="en_US.utf8"
> LC_NUMERIC="en_US.utf8"
> LC_TIME="en_US.utf8"
> LC_COLLATE="en_US.utf8"
> LC_MONETARY="en_US.utf8"
> LC_MESSAGES="en_US.utf8"
> LC_PAPER="en_US.utf8"
> LC_NAME="en_US.utf8"
> LC_ADDRESS="en_US.utf8"
> LC_TELEPHONE="en_US.utf8"
> LC_MEASUREMENT="en_US.utf8"
> LC_IDENTIFICATION="en_US.utf8"
> LC_ALL=
>
> 修改为英文
>
> [root@YH ~]# LANG=en_US.utf8
> [root@YH ~]# export LC_ALL=en_US.utf8



### linux基础命令系列

- date:显示时间日期
- cal:显示日历
- bc:简单的计算器

### 切换root账户

```
    su -
```

### 格式化输出日期

```
    date +%Y/%m/%d
```

### 格式化输出时间

```
    date +%H:%M
```

### 日历命令

```
    cal [month] [year]
```

### 热键的使用

**[Tab]**

- [Tab]接在一串命令的第一个字段后面则为【命令补全】
- [Tab]接在一串命令的第二个字段后面则为【文件补全】

**[Ctrl+D]**

通常代表键盘输入结束，或者替代**exit**命令

[shift+{page up}/{page dwon}]

翻页，命令过长屏幕翻页

### 如何用了解命令的使用

使用--help可以查看基本用法使用 man 加命令可以看到更详细的用法

例如输入man date后

```bash
DATE(1)                                             User Commands                                             DATE(1)

NAME
       date - print or set the system date and time

SYNOPSIS
       date [OPTION]... [+FORMAT]
       date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]

DESCRIPTION
       Display the current time in the given FORMAT, or set the system date.

       Mandatory arguments to long options are mandatory for short options too.

       -d, --date=STRING
              display time described by STRING, not 'now'

       -f, --file=DATEFILE
              like --date once for each line of DATEFILE

       -I[TIMESPEC], --iso-8601[=TIMESPEC]
              output  date/time in ISO 8601 format.  TIMESPEC='date' for date only (the default), 'hours', 'minutes',
              'seconds', or 'ns' for date and time to the indicated precision.

       -r, --reference=FILE
.....
```

这里可以看到有一个DATE(1)这个意思是一般用户可以使用的命令还有更多的数字解析如(从man man命令查看)

>       	 	 	 	 	 	 	 	 1   Executable programs or shell commands
>       	 	 	 	 	 	 	    2   System calls (functions provided by the kernel)
>       	 	 	 	 	 	 	    3   Library calls (functions within program libraries)
>       	 	 	 	 	 	 	    4   Special files (usually found in /dev)
>       	 	 	 	 	 	 	    5   File formats and conventions eg /etc/passwd
>       	 	 	 	 	 	 	    6   Games
>       	 	 	 	 	 	 	    7   Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7)
>       	 	 	 	 	 	 	    8   System administration commands (usually only for root)
>       	 	 	 	 	 	 	    9   Kernel routines [Non standard]

1(用户可以在shell中可以操作的命令或者可执行文件)、5(配置文件或是某些文件的格式)、8(系统管理员可用的管理命令)

上面这三个含义很重要



```bash
CAT(1)                                              User Commands                                              CAT(1)

NAME
       cat - concatenate files and print on the standard output

SYNOPSIS
       cat [OPTION]... [FILE]...

DESCRIPTION
       Concatenate FILE(s), or standard input, to standard output.

       -A, --show-all
              equivalent to -vET

       -b, --number-nonblank
              number nonempty output lines, overrides -n

       -e     equivalent to -vE

       -E, --show-ends
              display $ at end of each line

       -n, --number
              number all output lines

       -s, --squeeze-blank
              suppress repeated empty output lines

       -t     equivalent to -vT

       -T, --show-tabs
              display TAB characters as ^I

       -u     (ignored)

       -v, --show-nonprinting
              use ^ and M- notation, except for LFD and TAB

       --help display this help and exit

       --version
              output version information and exit

       With no FILE, or when FILE is -, read standard input.

EXAMPLES
       cat f - g
              Output f's contents, then standard input, then g's contents.

       cat    Copy standard input to standard output.

       GNU   coreutils   online   help:  <http://www.gnu.org/software/coreutils/>  Report  cat  translation  bugs  to
       <http://translationproject.org/team/>

AUTHOR
       Written by Torbjorn Granlund and Richard M. Stallman.

COPYRIGHT
       Copyright  ©  2013  Free  Software  Foundation,  Inc.   License  GPLv3+:  GNU   GPL   version   3   or   later
       <http://gnu.org/licenses/gpl.html>.
       This  is  free software: you are free to change and redistribute it.  There is NO WARRANTY, to the extent per‐
       mitted by law.

SEE ALSO
       tac(1)

       The full documentation for cat is maintained as a Texinfo manual.  If the info and cat programs  are  properly
       installed at your site, the command

              info coreutils 'cat invocation'

       should give you access to the complete manual.

GNU coreutils 8.22                                  November 2020                                              CAT(1)

```

再一次解析man page每一个区域有着不同的含义

- NAME 简短的命令数据说明
- SYNOPSIS 简短的命令语法简介
- DESCRIPTION 较为完整的说法,建议仔细看
- OPTIONS 可选项说明
- COMMANDS 当这个程序在执行的时候,可以在此程序中执行的命令
- FILES 这个程序或数据所使用或参考或连接到的某些文件
- SEE ALSO 可以参照跟这个命令或数据有相关的其他说明
- EXAMPLE 可以参考的实例

> 使用info也可以查看



> /usr/share下面基本都是帮助文档安装的软件基本的帮助都是在这个文档中



### 有关另一个文档编辑器

nano

使用命令后的page

> GNU nano 2.3.1                             File: 1.txt 
>
> 
>
> ^G Get Help         ^O WriteOut         ^R Read File        ^Y Prev Page        ^K Cut Text         ^C Cur Pos
> ^X Exit             ^J Justify          ^W Where Is         ^V Next Page        ^U UnCut Text       ^T To Spell

第一行,为版本号和文件名

下面几行为基本操作^代表ctrl



configure命令

configure文件是bai一个可执行的脚本文件，它有很多选du项，在待安装的zhi源码目录下使用命令./configure –help可以输出dao详细的选项列表。

其中--prefix选项是配置安装目录，如果不配置该选项，安装后可执行文件默认放在/usr /local/bin，库文件默认放在/usr/local/lib，配置文件默认放在/usr/local/etc，其它的资源文件放在/usr /local/share，比较凌乱。

如果配置了--prefix，如：

$ ./configure --prefix=/usr/local/test1



scp上传文件

### 正确的关机方法

**who**：查看目前有谁在线

**netstat -a**：查看网络的联机状态

**ps -aux**：查看后台执行的程序

**sync**：将数据同步写入硬盘

**shutdown**：常用的关机命令

**reboot、halt、poweroff**：重新启动、关机


#### shutdown

```
    shutdown [-krhc] [时间] [警告信息]
```

- -k：不要真的关机，只是发送警告信息出去
- -r：在将系统的服务停掉之后就重新启动（常用）
- -h：将系统的服务停掉以后，立即关机（常用）
- -c：取消已经在进行的shutdown命令内容
- 时间：指定系统关机时间，若没指定，默认一分钟自动进行



## 文件权限与目录配置

### 用户与组

所有用户的相关信息都放在/etc/passwd

个人的密码保存在/etc/shadow文件夹中

所有的组名都记录在/etc/group中

***这三个文件夹不可随便删除***

不解释了不明白自己百度



### 基本命令统计

- ls	:显示文件名以及属性 -al所有、-l --full-time显示所有完整的时间格式 -S排序

- chgrp :修改文件所属用户组 -R递归进行修改联通子目录下的所有文件
- chown :修改文件所属用户 -R同上、也可以修改用户组
  - chown 用户名 文件名(修改所属用户)
  - chown 用户名:组名 文件名(修改所属用户和组)
- chmod :修改文件权限
  - 三种修改模式 +(增加权限) -(删除权限) =(设置权限为)
  - 太长了看这个吧..https://www.runoob.com/linux/linux-comm-chmod.html
- cp    :复制文件

#### chmod修改权限，数字类型修改文件权限

```linux
       chmod [-R] xyz 文件或目录

       xyz：数字类型的权限属性，为rwx属性数值的相加
       -R：进行递归修改，连同子目录下的所有文件都会修改
```

#### chmod修改权限，符号类型修改文件权限

![](https://www.runoob.com/wp-content/uploads/2014/08/rwx-standard-unix-permission-bits.png)


#### 文件属性详解

> drwxr-xr-x   2 root root  4096 Aug 14 17:34 .pip

例如上面的参数(用ls -al查出来的).



==第一个属性==

##### 文件权限

第一个字符表示目录、文件、连接

- 当为[d]时为目录

- 当为[-]则是文件

- 当为[|]则是连接

- 当为[b]表示为设备文件里面的可供存储的周边设备

- 若是[c]则表示属于设备文件属于串行端口设备,如键盘鼠标

- > file 文件名     //查看文件类型

接下来每三个字符一组分别又rwx组成,r代表可读,w代表可写,x代表可执行[-]代表没有权限(如果为目录的话没有x不能进入,w权限并不能代表可以删除文件)



权限对文件来说	

r代表是否可以获取文件中的内容。

w代表是否能**修改、写入、新增、编辑(不包括删除该文件)**。

x代表该文件是否可执行，不像Windows一样用后缀名来代表文件是否可执行



权限对目录来说	

r代表你可以查询该目录下内容(可以使用ls来查看)。

w建立新的文件或目录、删除已存在目录与文件（不论该文件的权限是什么）、对文件或者目录进行改名、移动该目录内的文件、目录位置。

x代表**是否能进入该目录**



第一组代表文件拥有者（owner）可具备的权限,第二组为所属组（group）所具备的权限,第三组为其他人（others）的权限

----

##### 文件默认权限

当你建立一个新的文件或者文件夹的时候默认权限是什么?

用umask可以指定目前用户在建立文件或目录时候给的权限

umask 设置默认减掉的权限例如umask 777则变成新建文件什么权限不给 umask 000 则给予最高权限

-p以数字形式显示权限设置情况

-S以字符串方式显示



### 文件隐藏属性

- chattr 修改文件隐藏配置--- 增加隐藏属性,-a 只能增加数据不能删除不能修改数据只有root才能设置属性 -i 不能删除、改名、设置连接、修改数据或新增数据只有root 才能修改属性
- lsattr [-adR] 目录或文件 显示文件隐藏配置 ----  -a基本属性也显示 -d如果时目录进显示出本身属性而不是目录内的文件名 -R连通子目录一起显示



第二个属性是有多少文件名连接到此节点.

第三个属性是文件或者目录拥有者.

第四个属性是文件的所属组.

第五个属性是文件大小

第六个属性是文件创建日期或是修改日期(月/日,不是今年显示年)



### 文件隐藏权限SUID,SGID,SBIT

- SUID
  - 仅对二进制有效
  - 执行者需要具有x权限
  - 执行者将具有该程序的拥有者权限
- SGID
  - 对二进制有效
    - 执行者对于该程序需要具备x权限
    - 执行者在执行的过程中会获得该程序用户组的支持
  - 对目录有效
    - 若用户具有r与x权限才能进入该目录
    - 用户再次目录下的有效用户组会变成该组
    - 用户再次目录下若具有w权限则用户缩新建的文件与此目录相同
- SBIT
  - 只针对目录有效
  - 当用户具有w、x权限，即具有写入的权限
  - 当用户在该目录下建立文件或目录，仅有自己与root能删除该文件



### ACL特殊用户权限

可以针对单一用户、单一文件或目录进行r、w、x权限设置

查看是否能够使用acl

> dmesg | grep -i acl
>
> [root@YH ~]# dmesg | grep -i acl
> [    1.411790] systemd[1]: systemd 219 running in system mode. (+PAM +AUDIT +SELINUX +IMA -APPARMOR +SMACK +SYSVINIT +UTMP +LIBCRYPTSETUP +GCRYPT +GNUTLS +ACL +XZ +LZ4 -SECCOMP +BLKID +ELFUTILS +KMOD +IDN)
> [ 3572.030938] SGI XFS with ACLs, security attributes, no debug enabled

- getfacl filename:获取某个文件/目录的ACL设置选项

  - 几乎和下面一样

  - >[root@YH local]# getfacl myuser/
    >\# file: myuser/
    >\# owner: root
    >\# group: mygroup1
    >\# flags: -s-
    >user::rwx
    >user:user1:rwx
    >group::rwx
    >mask::rwx
    >other::---

- setfacl 目标文件名:设置某个目录/文件的ACL规范

  - 简单的 u:账号:权限

    - u:特定账户

    - g:特定用户组

    - m:用户权限或者组权限必须在mask权限设置范围才有效,即最大允许的权限如果mask最大权限为r那么无论怎么设置权限最大都是r

    - ```bash
      [root@YH local]# setfacl -m m::r myuser/
      [root@YH local]# getfacl myuser/
      # file: myuser/
      # owner: root
      # group: mygroup1
      # flags: -s-
      user::rwx
      user:user1:rwx			#effective:r--
      group::rwx			#effective:r--
      mask::r--
      other::---
      
      [root@YH local]# su user1
      [user1@YH local]$ cd myuser/
      bash: cd: myuser/: Permission denied
      
      ```

      - u:[g|u]:用户:权限,设置默认权限

  - -m :设置后续的ACL参数给文件使用

  - -x :删除后续的ACL

  - -b :删除所有ACL

  - -k :删除默认ACL参数

  - -R :递归设置ACL

  - -d :设置默认ACL只对目录有效(新建数据时会引用此默认值)

  - >[root@YH local]# setfacl -m u:user1:rwx myuser
    >
    >[root@YH local]# ll | grep 'myuser'
    >drwxrws---+  4 root mygroup1  4096 Sep 16 18:30 myuser[root@YH local]# ll | grep 'myuser'
    >drwxrws---+  4 root mygroup1  4096 Sep 16 18:30 myuser

  > 删除账号权限不用加权限等级如setfacl -x u:myuser1
  >
  > 设置账号无权限权限不能为空可以用-代替如setfacl -x u:myuser1:-



### 文件类型以及扩展名

自己看吧~~~额https://www.cnblogs.com/peida/archive/2012/11/22/2781912.html



## 文件与目录管理

特殊目录

>.	 代表此目录
>
>..	代表上层目录
>
>\-     代表前一个目录
>
>~	 代表当前身份的家
>
>~acc  代表这个身份的家(acc表示用户名)

### 文件与目录的查看：ls

>默认显示的只有：**非隐藏的文件名、以文件名进行排序及文件名代表的颜色显示**
>蓝色显示目录、白色显示一般文件

- -a:全部的文件，连同隐藏文件（开头为.文件）一起列出来（常用）
- -A:全部的文件，连同隐藏的文件，但不包括.与..这两个目录
- -d:仅列出目录本身，而不是列出目录内的文件数据（常用）
- -f:直接列出结果，而不进行排序（ls默认会以文件名排序）
- -Z:显示安全上下文
- -F:根据文件、目录等信息，给予附加数据结构
  - *:代表可执行文件；/:代表目录；=:代表socket文件；|:代表FIFO文件
    -l:详细信息显示，包含文件的属性与权限等数据（常用）


### 目录处理命令

- cd:切换
- pwd:显示当前目录 **-P可以知道目录的真实路径**
- mkdir:建立一个新目录 **-p可以一次建立多级目录，-m设置文件的权限，不使用默认权限**
- rmdir:**删除一个空目录**，-p连同上层“空”目录也一起删除
- rm -r xx 删除目录包括目录下的文件
- cp:复制 
  - -d若源文件为链接文件属性,则复制链接文件而不是文件本身 
  - -f若文件存在且无法开启则删除后再试
  - -a 相当于-dr --preserve=all大概是**全复制**(权限时间什么的都复制)
  - -i 若文件存在则覆盖时会询问
  - -p 连同属性一起复制过去
  - -r 递归复制,(用于**目录的复制**)，文件与目录的权限可能会被改变
    亦可以利用{cp -a /etc /tmp}来执行命令，尤其是备份情况下
  - -l硬链接
  - -s软链接，亦快捷方式
  - -u目标与源文件有差异才复制j
- mv:移动目录和文件（也可以拿来做重命名）

- basename:获取字符串的文件名
- dirname:获取字符串的路径名

#### cp（复制）命令

```linux
  cp [-adfilprsu] 源文件(source) 目标文件(destination)
  cp [options] source1 source2 source3 ... directory
```

复制时，必须要清除的了解到：

- 是否需要完整的保留源文件信息？
- 源文件是否为符号链接文件（symbolic link file）？
- 源文件是否为特殊的文件，例如FIFO、socket等？
- 源文件是否为目录？

### rm（删除文件或目录）

```
rm [-fir] 文件或目录
```

选项与参数：

- -f：就是force的意思，忽略不存在的文件，不会出现警告
- -I：交互模式，在删除前会询问使用者是否操作
- -r：递归删除，常用于目录的删除，**非常危险的选项*

### 文件内容查看

- tac:从最后一行显示
- nl:同时输出行号
- more:一页一页显示 (空格下一页,enter下一行,:f立即显示行数,q:立即离开,b往前翻)
- less:与more相同就但是是从后面显示
- head:只看前几行
- tail:只看后几行
- od:以二进制方式读取内容
- cat:从第一行开始显示 -n打印行号包含空白页 -A 显示特殊字符

### 修改文件内容与新建

- 修改时间:当内容改变而不是权限或属性改变时才会改变的时间
- 状态时间:当文件状态改变,例如属性或权限改变时才会改变的时间
- 读取时间:当文件的内容被读取时就会改变
- 使用touch可以修改文件时间



### 查看文件类型

file+文件



### 文件查找

- which 查找执行文件 根据path环境变量所规范的路径去查找 -a显示所有而不是第一个,-i 显示所有查找的路径,主要针对bin/sbin下面的文件以及/usr/share/man下面的man page文件 
- 使用locate来查找,寻找的数据是由与建立的数据库/var/lib/mlocate里面的数据,这个数据库是日更,直接输入updatedb系统会自动去读取/etc/updatedn.conf这个文件的设置并更新数据库里面的数据
- find [PATH] [option] [action]
  - -mtime n
    - 例如今天为2011-05-05
    - n如果前面没有符号则为在第前当n天修改的文件 -mtime 3 则查看2011-05-02
    - -n列出在n天之内的被修改的文件 -mtime -3 则查看2011-05-03---2011-05-05
    - +n 列出在n天前被修改的内容 -ntime +3 则查看-----到2011-05-01
  - -uid n 根据用户的ID(/etc/passwd)查找
  - -giu n 根据组名id(/etc/group)进行查找
  - -user name :name为使用者名字
  - -group name: name为组名
  - -nouser :查找文件拥有者不在(/etc/passwd)
  - -nogroup :查找拥有用户组不在(/etc/group)内的文件
    - 上面两个命令可以用在查找已删除用户或者组但是没删除的文件
  - -name filename:查找文件名为filename的文件
  - -size [+-]SIZE:若用+则查找比SIZE大的,若用-则查找比SIZE小的
  - -type :根据类型查找
  - -perm :根据权限查找
  - -exec :进行额外操作,就是将查找的结果移交到后面的



### 关于执行文件路径变量$PATH

ｅｃｈｏ＄PATH可以输出

将路径加入PATH中的命令 = ${PATH}:A

A为你要添加的路径



## Linux磁盘与文件管理系统

ext2（Linux second Extended file system）

由于ext2是属于索引表加数据的一种形式所以几乎不用磁盘碎片整理，但是FAT这种链式磁盘管理方式就需要磁盘碎片整理

ext2基本是多个区块群组，每个区块群组都有独立的inode、数据区块、超级区块系统,文件系统最前面有一个启动扇区

ext2原则上:

- 不再能修改区块的大小与格式除非格式化
- 每个区块最多能够放置一个文件数据
- 如果文件大于区块则会多占其他区块
- 如果文件小于区块则不能再放别的数据



> 不同启动扇区安装到别的文件系统最前端这样就能制作多重引导的环境

每一个区块群组有六个主要内容

- 数据区块

  - 有1K|2K|4K三种

    

    | 区块大小           | 1K   | 2K    | 4K   |
    | ------------------ | ---- | ----- | ---- |
    | 最大单一文件       | 16GB | 256BG | 2TB  |
    | 最大文件系统总容量 | 2TB  | 8TB   | 16TB |

    是根据inode决定的

- inode

  - 包含文件的读写属性、用户组、大小、时间、文件特性标识、真正指向的内容、每个inode表固定128B、每个文件都会占一个inode、记录一个数据区要用4B
  - inode容量计算假设区块为1K、有12个直接指向、1个间接指向、1个双间接、1个三间接
    - 所以直接指向容量=12\*1K = 12K
    - 间接容量=1K*1K/4 = 256K ----因为间接指向会指向一个新的inode但是这个inode必须直接指向数据区块所以1K/4
    - 双间接容量=256\*256\*1K=256^2 * 1K
    - 三间接容量=256^3 \* 1K
    - 总和为16GB

- superblock

  - 主要信息有数据区块和inode的总量
  - 未使用以及已经使用的inode、数据区块的数量
  - 数据区块与inode的大小
  - 文件系统的挂载时间、最近一次写入数据的时间、最近一次检验磁盘的时间等
  - 一个有效位数 0来显示文件已经挂载，1来显示为挂载

- Filesystem Description

  - 用来描述每个区块群的开始与结束，以及说明每个区块（超级区块、最招标、inode对照表）等的位置

- 区块对照表

  - 区块的使用情况都在这个表中

- inode对照表

  - 记录inode使用和未使用的inode号

ext2可以使用dumpe2fs去查看情况



> 现在centos默认为xfs所以无法使用dumpe2fs去查看



### 与目录树的关系

在新建一个目录的时候就会分配一个inode和至少一个区块

使用ls -i可以查看inode号码



### Linux文件系统的运行

当系统加载一个文件如果该文件没有被修改则设置为干净(clear),如果被修改了就设置为脏(dirty)不定时的会向硬盘中会写,还可以使用sync来进行同步来达到数据的一致性------异步处理



文件系统与内存的关系:

- 系统会把常用的文件放到内存中这样会加快系统性能,所以内存经常满了是正常的
- 关机命令会主动调用sync来将数据写到磁盘中
- 若不能正常关机数据不能写到硬盘中,因此重启时会有很长时间的数据校验,甚至可能导致文件系统的损坏

### Linux文件系统

查看支持的

> ls -l /lib/modules/$(uname -r)/kernel/fs

查看加载到内存的

> cat /proc/filesystems



VFS用来识别管理管理文件系统



接下来谈谈为啥要一起ext系统选择xfs

- 支持度最广,格式化很慢
- xfs适合高容量和巨型文件
- 速度虽然与xfs相似但是恢复速度-创建速度都是很慢

xfs分为三个部分数据区、文件系统活动登陆区、实时运行区

- 数据区：包含inode、数据区块、超级区块等数据，xfs与ext的不同是可以有多种不同的容量可以设置512B~64KB但由于文件系统的关系最高是4K，而inode最大是256B~2MB，一般默认256B够用了
- 文件系统活动登陆区：文件变化会记录在这里这个部分可以指定外部的磁盘来作为区块。
- 实时运行区：当文件建立时会在这个区段内找到一个到数个extent区块，将文件放置在这里等待分配完毕后，再写入data section的inode与区块中。extent区块可以设置4K~1G，最好和stripe一样大，最好不要乱动可能会影响到物理磁盘的性能

> 使用xfs_info去观察

xfs_info参数详解

```bash
[root@study ~]# df -T /boot
Filesystem Type 1k-blocks     Used     Available Use% Mounted on
/dev/vda2  xfs   1038336      133704  904632    13     /boot
# 可以看出来是xfs文件系统，观察一下
 
[root@study ~]# xfs_info /dev/vda2
1  meta-data=/dev/vda2         isize=256    agcount=4, agsize=65536 blks
2           =                  sectsz=512   attr=2, projid32bit=1
3           =                  crc=0        finobt=0
4  data     =                  bsize=4096   blocks=262144, imaxpct=25
5           =                  sunit=0      swidth=0 blks
6  naming   =version 2         bsize=4096   ascii-ci=0 ftype=0
7  log      =internal          bsize=4096   blocks=2560, version=2
8           =                  sectsz=512   sunit=0 blks, lazy-count=1
9  realtime =none              extsz=4096   blocks=0, rtextents=0
```

- 第 1 行里面的 isize 指的是 inode 的容量，每个有 256Bytes 这么大。至于 agcount 则是 前面谈到的储存区群组 (allocation group) 的个数，共有 4 个， agsize 则是指每个储 存区群组具有 65536 个 block 。配合第 4 行的 block 设置为 4K，因此整个文件系统的容 量应该就是 4655364K 这么大!
- 第 2 行里面 sectsz 指的是逻辑扇区 (sector) 的容量设置为 512Bytes 这么大的意思。
- 第 4 行里面的 bsize 指的是 block 的容量，每个 block 为 4K 的意思，共有 262144 个 block 在这个文件系统内。
- 第 5 行里面的 sunit 与 swidth 与磁盘阵列的 stripe 相关性较高。这部份我们下面格式化 的时候会举一个例子来说明。
- 第 7 行里面的 internal 指的是这个登录区的位置在文件系统内，而不是外部设备的意 思。且占用了 4K * 2560 个 block，总共约 10M 的容量。
- 第 9 行里面的 realtime 区域，里面的 extent 容量为 4K。不过目前没有使用。



### 使用df与du来查看磁盘使用情况



- df:列出文件系统的整体磁盘使用量
  - -h .以GB,MB,KB的形式显示
  - -u .不用磁盘容量,以inode来显示
  - -T .连通文件系统名称也列出来
  - -a .列出所有的文件系统包含/proc等文件系统
  - -i .列出inode资讯
- du:查看文件系统的磁盘使用情况
  - -a或-all 显示目录中个别文件的大小。
  - -b或-bytes 显示目录或文件大小时，以byte为单位。
  - -c或--total 除了显示个别目录或文件的大小外，同时也显示所有目录或文件的总和。
  - -D或--dereference-args 显示指定符号连接的源文件大小。
  - -h或--human-readable 以K，M，G为单位，提高信息的可读性。
  - -H或--si 与-h参数相同，但是K，M，G是以1000为换算单位。
  - -k或--kilobytes 以1024 bytes为单位。
  - -l或--count-links 重复计算硬件连接的文件。
  - -L<符号连接>或--dereference<符号连接> 显示选项中所指定符号连接的源文件大小。
  - -m或--megabytes 以1MB为单位。
  - -s或--summarize 仅显示总计。
  - -S或--separate-dirs 显示个别目录的大小时，并不含其子目录的大小。
  - -x或--one-file-xystem 以一开始处理时的文件系统为准，若遇上其它不同的文件系统目录则略过。
  - -X<文件>或--exclude-from=<文件> 在<文件>指定目录或文件。
  - --exclude=<目录或文件> 略过指定的目录或文件。
  - --max-depth=<目录层数> 超过指定层数的目录后，予以忽略。
  - --help 显示帮助。
  - --version 显示版本信息。

### 软硬链接

硬链接指代的是一个事实的文件并且新建一个硬链接并不会消耗inode号,因为他俩指向同一文件

不能跨文件系统,不能连接目录

ln硬链接

ln -s软连接

软链接就是快捷方式,至于大小....   ->右边几个字母大小就是几字节

```
lrwxrwxrwx 1 root root     11 12-07 16:01 link2013 -> log2013.log
```



当我们创建一个新目录的时候一般会有一下几个东西:

n

n/.

n/..

所以如果你新建一个目录则新目录会有两个链接(n或n/.)和n/.. 而上层目录的链接数会+1(因为n/..会跳到他)



### 如何划分新硬盘

#### 观察硬盘分区状态

使用lsblk列出所有存储设备

-i 代表使用ascii码

-p 代表输出完整名称不仅仅是最后的名称

>[root@YH ~]# lsblk
>NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
>sr0     11:0    1 141.4M  0 rom  
>vda    253:0    0    50G  0 disk 
>└─vda1 253:1    0    50G  0 part /

- NAME       代表设备文件名，会自动省略dev
- MAJ:MIN    代表设备是通过两个代码来实现的,分别是主要设备和次要设备
- RM         代表是否可卸载
- SIZE       代表最大容量
- RO         代表是否为只读设备
- TYPE       代表是硬盘(disk)还是分区(part)或是只读存储器(rom)等输出
- MOUNTPOINT 代表挂载目录



使用blkid来列出设备的UUID等参数

>[root@YH ~]# blkid
>/dev/sr0: UUID="2021-08-14-17-43-46-00" LABEL="config-2" TYPE="iso9660" 
>/dev/vda1: UUID="4b499d76-769a-40a0-93dc-4a31a59add28" TYPE="ext4" 



parted列出磁盘分区表类型与分区信息



### 建议的分区操作

先使用lsblk找到磁盘

使用blkid 磁盘  进行查看磁盘类型 或是使用parted 磁盘

==如果要进行分区注意了fdisk是MBR的分区表,而gdisk是GPT的分区表---!!!千万不要在MBR上面使用gdisk也不要在GPT上面使用fdisk==

使用fdisk或是gdisk进行分区

分区前先用p进行查看分区情况查看section的位置以免覆盖,使用n可以新建分区

<建议上面的操作用帮助先验证是否正确>

在使用w之前都可以用q退出来撤销,分区完后使用w进行写入,由于硬盘可能在使用中所以分区可能不会发生需要用重启或者partpeobe这个命令进行更新Linux内核信息-s会显示出来



==parted也可以进行分区==



使用fdisk进行分区

>由于我的linux采用的是ext4所有暂时先介绍fdisk

```bash
================首先使用这个命令进入分区系统============

[root@YH local]# fdisk /dev/vda
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

================d代表删除分区===============
Command (m for help): d
Selected partition 1
Partition 1 is deleted
============然后再新建一个分区===========
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 
==========默认即可========
First sector (2048-104857599, default 2048): 
Using default value 2048
=============最后的区号=============
Last sector, +sectors or +size{K,M,G} (2048-104857599, default 104857599): 83886079
Partition 1 of type Linux and of size 40 GiB is set

====================进行第二个分区=================
Command (m for help): n
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): p
Partition number (2-4, default 2): 
First sector (83886080-104857599, default 83886080): 
Using default value 83886080
Last sector, +sectors or +size{K,M,G} (83886080-104857599, default 104857599): 
Using default value 104857599
Partition 2 of type Linux and of size 10 GiB is set
=================写入分区=================
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.

==============使用这个命令更新分区表===============
[root@YH local]# partprobe
Warning: Unable to open /dev/sr0 read-write (Read-only file system).  /dev/sr0 has been opened read-only.
[root@YH local]# lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sr0     11:0    1 141.4M  0 rom  
vda    253:0    0    50G  0 disk 
├─vda1 253:1    0    40G  0 part /
└─vda2 253:2    0    10G  0 part 
[root@YH local]# blkid 
/dev/sr0: UUID="2021-09-10-09-46-39-00" LABEL="config-2" TYPE="iso9660" 
/dev/vda1: UUID="4b499d76-769a-40a0-93dc-4a31a59add28" TYPE="ext4" 
[root@YH local]# parted /dev/vda
GNU Parted 3.1
Using /dev/vda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print                                                            
Model: Virtio Block Device (virtblk)
Disk /dev/vda: 53.7GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags: 

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  42.9GB  42.9GB  primary  ext4
 2      42.9GB  53.7GB  10.7GB  primary

(parted) q                                                                

```



分区后进行格式化

使用mkfs

>[root@YH dev]# mkfs
>mkfs         mkfs.btrfs   mkfs.cramfs  mkfs.ext2    mkfs.ext3    mkfs.ext4    mkfs.minix   mkfs.xfs  

用tab tab可以查看出可以使用上面这个分区格式化命令(如果要进行格式化的分区已经存在文件系统需要加-f进行强制格式化)



#### 如果发生了意外导致关机

需要使用xfs_repair处理xfs文件系统

至于ext4则使用fsck.ext4进行处理

要使用上面的命令进行扫描磁盘不能挂载必须卸载



### 文件系统的挂载与卸载

挂载

- 单一文件系统不应该重复挂载
- 单一目录不应该重复挂载多个文件系统
- 要作为挂载点的目录,理论上要空目录才行

> 挂载了文件系统之后原目录下的东西会暂时消失

- mount:挂载命令

  ```bash
  [root@YH dev]# blkid
  /dev/sr0: UUID="2021-09-10-09-46-39-00" LABEL="config-2" TYPE="iso9660" 
  /dev/vda1: UUID="4b499d76-769a-40a0-93dc-4a31a59add28" TYPE="ext4" 
  /dev/vda2: UUID="77514366-9351-4fa2-b27e-7458d242e76c" TYPE="xfs" 
  [root@YH dev]# mkdir -p /usr/local/YH
  [root@YH dev]# cd /usr/local
  [root@YH local]# ls
  bin  etc  games  include  lib  lib64  libexec  qcloud  sbin  share  src  yd.socket.server  YH
  [root@YH local]# mount UUID="77514366-9351-4fa2-b27e-7458d242e76c" /usr/local/YH
  [root@YH local]# df /usr/local/YH
  Filesystem     1K-blocks  Used Available Use% Mounted on
  /dev/vda2       10475520  8276  10467244   1% /usr/local/YH
  
  ```

  如果挂载u盘则不能是NTFS格式

- remount:重新挂载

- umount:卸载

  - -f 强制卸载
  - -l 立即卸载(强制性再-f之上)

> 如果发现无法卸载device is busy则使用fuser -mki 目录  来进行删除



如果想要改为开机挂载需要修改/etc/fstab文件,但是实际的挂载文件在/etc/mtab内,但是如果fstab这个文件出现语法错误会导致无法开机

```bash
[root@YH ~]# cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Thu Mar  7 06:38:37 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=4b499d76-769a-40a0-93dc-4a31a59add28 /                       ext4    defaults        1 1
==文件名==                               ==挂载目录==            ==文件系统==
```





需要进入担任维护模式运行

>mount -n -o remount,rw

### 磁盘/文件参数

硬盘上主要通过major与minor数值代表设备

例如:

```bash
[root@YH local]# lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sr0     11:0    1 141.4M  0 rom  
vda    253:0    0    50G  0 disk 
├─vda1 253:1    0    40G  0 part /
└─vda2 253:2    0    10G  0 part /usr/local/YH
```

253代表主设备代码(major)而0代表次设备代码(minor)



- mknod:可以设置major与minor
- xfs_admin:可以修改设备的UUID与Label name
- tune2fs:修改ext4的UUID和Lable

### 内存交换分区

按照物理分区进行划分

1. 分区
2. 格式化 mkswap:格式化为交换分区
3. 使用,将该分区启动 swapon:启动
4. 观察通过free与swapon -s



使用文件创建内存交换文件

1. 使用dd命令在/tmp下面新增一个文件
2. 使用mkswap 将新建的文件转换为内存交换文件格式
3. 使用swapon
4. 使用swapoff关闭并设置自动启用



## 鸭缩

常见的鸭缩格式有\*.z \*.zip \*.gz \*.bz2 \*.xz \*.tar \*.tar.gz \*.tar.bz2 \*.tar.xz

其中\*.tar代表打包程序,\*.z \*.zip \*.gz \*.bz2 \*.xz代表鸭缩文件, \*.tar.gz这种代表先打包后鸭缩

> 由于鸭缩一次只能鸭缩一个文件所以才要用tar对多个文件进行打包



### 鸭缩命令

- gzip:

  - -c数据输出可以数据流定向 
  - -d 解吖缩
  - -t 验证一致性 
  - -v 鸭缩比 
  - -#鸭缩等级 1最快,鸭缩比最差 9最慢 
  - 默认情况下用gzip鸭缩后源文件不在了
  - 读取用zcat

- bzip2:

  - 参数同上
  - 读取用bzcat

- xz:

  - 用法同上
  - 读取用xzcat

  

**源文件**

```bash
[root@YH YH]# ls -als
total 88682
    0 drwxr-xr-x   2 root root       81 Sep 11 09:03 .
    4 drwxr-xr-x. 14 root root     4096 Sep 10 10:45 ..
    1 -rw-r--r--   1 root root       46 Sep 11 08:43 1.txt.gz
88677 -rw-r--r--   1 root root 90805089 Jan 20  2018 Java语言程序设计.进阶篇.原书第10版.pdf
```

**用gzip鸭缩后**

```bash
[root@YH YH]# ls -alsr
total 71500
71495 -rw-r--r--   1 root root 73210438 Jan 20  2018 Java语言程序设计.进阶篇.原书第10版.pdf.gz
    1 -rw-r--r--   1 root root       46 Sep 11 08:43 1.txt.gz
    4 drwxr-xr-x. 14 root root     4096 Sep 10 10:45 ..
    0 drwxr-xr-x   2 root root       84 Sep 11 09:03 .
```

鸭缩比19.4%



**用bzip2:**

> 1.262:1,  6.338 bits/byte, 20.78% saved, 90805089 in, 71940276 out.



**用xz:**

>  100 %         66.3 MiB / 86.6 MiB = 0.766   2.2 MiB/s       0:39 



### 打包命令

- tar:

  - -c 建立打包文件
  - -t 查看打包文件的内容有哪些文件名
  - -x 解包或解吖缩
  - -z 通过gzip的支持进行鸭缩或者解吖
  - -j 通过bzip2的支持进行鸭缩或解吖
  - -J 通过xz的支持进行鸭缩或解吖
  - -v 鸭缩与解吖过程中的文件名显示出来
  - **-f 后面要立即接要被处理的文件名**
  - -C 解吖到指定文件目录
  - -p 保留碑文数据的原本权限与属性
  - -P 保留绝对路径允许备份数据中含有根目录存在之意

- 常用的tar命令

  - 鸭缩:tar -jcv -f filename.tar.bz2 要被鸭缩的文件或目录;
  - 查询:tar -jtv -f filename.tar.bz2;
  - 解吖缩:tar -jxv -f filename.tar.bz2 -C 要解压的目录
  - 解开单一文件:
    - tar -jtv -f /root/etc.tar.bz2 | grep '要解吖的文件名' 这样可以查出来
    - tar -jxv -f 打包的文件.tar.bz2 待解开的文件名

  注意鸭缩的时候最好自己加上.tar.[bz2|xz|gz]



==建议经常使用tar备份etc==

time tar -zpcv -f etc.tar.gz /etc

```bash
real	0m3.923s
user	0m1.125s
sys	0m0.097s
```



time tar -jpcv -f etc.tar.bz2 /etc

```bash
real	0m2.945s
user	0m2.838s
sys	0m0.053s
```



time tar -Jpcv -f etc.tar.xz /etc

```bash
real	0m12.140s
user	0m11.764s
sys	0m0.129s
```

原本大小

> 37MB	/etc

```bash
-rw-r--r--   1 root root  9453958 Sep 11 10:08 etc.tar.bz2
-rw-r--r--   1 root root 10732764 Sep 11 10:04 etc.tar.gz
-rw-r--r--   1 root root  7632840 Sep 11 10:09 etc.tar.xz
```



#### 如果要打包目录但是不包含某些文件

tar -jcv -f /root/system.tar.bz2 --exclude=/root/etc*

--exclude=不想要包含的文件

*代表通配符



### 备份



#### 仅备份比某个时候更新的文件

--newer(包含mtime与ctime)

--newer-mtime(只能用mtime)

- atime:***\*显示的是文件中的数据最后被访问的时间\****
- mtime:***\*显示的是文件内容被修改的最后时间\****
- ctime:***\*显示的是文件的权限、拥有者、所属的组、链接数改变、文件内容改变的时间。\****

> 通常所说的tarfile代表只进行打包,tarbali代表打包鸭缩



#### 关于SELinux问题

SELinux可能会让你的系统无法读写配置文件,导致影响到系统的正常使用

如果使用上面的方法进行备份恢复但是无法正常使用可能是/etc/shadow这个密码文件的SELinux类在还原时被修改了导致无法正常登录

处理方法:

- 方法一:修改/etc/selinux/config文件将SELinux改成permissive模式重启;
- 方法二:在第一次恢复系统后不要立即重启先使用restorecon -Rv /etc 自动修复一下SELinux即可
- 方法三:通过各种可行的方式登录系统,建立/.autorelabel文件,重新启动后系统会自动修复SELinux并会再次重启后正常



#### XFS文件系统的备份与还原

xfsdump命令：增量备份,与git差不多显示出与上一份增加的文件的差异

记录文件放在/var/lib/xfsdump/inventory

注意:

- 不支持没有挂载的文件系统备份
- 必须使用root
- 只能备份xfs
- 备份的数据只能用xfsrestore解析
- 通过UUID来识别各个备份文件
- 支持文件系统的备份并不支持特定目录的备份,不能备份/etc

备份测试

```bash
xfsdump -l 0 -L vda2_all -M vda2_all -f /vda2.dump /usr/local/YH
进行备份

xfsdump -I
查看结果

dd if=/dev/zero of=/usr/local/YH/test.img bs=1M count=10
新增文件

 xfsdump -l 1 -L vda2_2 -M vda2_2 -f /vda2.dump1 /usr/local/YH
再次新增
//注意只有建立过-l 0 才能-l [1~9]进行增量备份

```



使用-xfsrestore进行恢复

xfsrestore [-f 备份文件] -r 待恢复目录



- dd:

  - if(input file):可以是设备

  - of(output file):可以是设备

  - bs:设置一个block的大小,默认512Bytes(一个扇区大小)

  - count多少个bs

  - > dd if=/dev/zero of=/usr/local/YH/test.img bs=1M count=10
    > 10+0 records in
    > 10+0 records out
    >
    > 例如上面这个例子代表将/dev/zero备份到/usr/local/YH/test.img 每个分区为1M 设置10个分区 下面10+0代表10个完整的1M以及0个未满1M的意思

    使用dd复制后如果发现无法使用某些分区或者文件系统使用下面的命令

    >xfs_repair -L /dev/sda1 #清理log
    >
    >uuidgen 获得新的uuid
    >
    >xfs_admin -U 新获得的UUID 设备驱动
    >
    >由于dd连uuid也一起复制二xfs主要使用uuid来区分文件系统所以要在给一个uuid

- cpio:以后再说



## vim

### vim的缓存与恢复

当我们编辑文件时会在与编辑的目录下生成一个.filename.swp的文件,例如

vi /YH.txt 会有.YH.text.swp 这个文件就是用来记录修改信息的

如果发生意外vim中断下次进入时按R即可恢复缓存内容但是那个缓存文件需要你自己删除否则会一直警告

如果这个缓存没用了按D进行删除vim会自己再建立一个

> 注意vi与vim不一样有的时候系统默认vi就是vim使用alias命令即可查看
>
> [root@YH vitest]# alias
> alias cp='cp -i'
> alias egrep='egrep --color=auto'
> alias fgrep='fgrep --color=auto'
> alias grep='grep --color=auto'
> alias l.='ls -d .* --color=auto'
> alias ll='ls -l --color=auto'
> alias ls='ls --color=auto'
> alias mv='mv -i'
> alias rm='rm -i'
> alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'

区块编辑

移动到区块最开始的位置按下ctrl+v然后移动光标会这样

![image-20210912110828669](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210912110828669.png)

按下y进行复制按p可以粘贴

### 多文件编辑

vim可以使用 vim [file1] [file2]进行同时编辑

使用:files可以显示所有要编辑的文件

>:files
>1 %a   "man.test.conf"                line 1
>2      "/etc/host"                    line 0
>Press ENTER or type command to continue



### 多窗口编辑

| 命令            | 结果                                                       |
| --------------- | ---------------------------------------------------------- |
| :sp [filename]  | 添加一个窗口不加filename就是创建两个窗口但是同一文件       |
| ctrl+w          | 进行窗口的切换                                             |
| ctrl+x          | 下方弹出所有的可补全提示常用的有ctrl+o以文件扩展名方式补充 |
| :set nu         | 设置与取消行号,nonu取消行号                                |
| :set autoindent | 设置是否自动缩进,noautoindent不缩进                        |
| :set all        | 显示目前所有的环境参数设置值                               |

![img](https://img2018.cnblogs.com/blog/1442837/201811/1442837-20181121151226556-202455126.png)



### 中文乱码问题

- 系统默认的语言:/etc/locale.conf

- bash的语系:LANG、LC_ALL变量有关

- 文件原本编码
- 打开终端的软件
- 使用iconv进行转码



常用快捷键

w:下一个单词的词首

e:下一个单词的词尾

d:上一个单词的词首

^:行首

$:行尾

dd:删除一整行

:[行] 跳到某一行

## Shell

查看可使用的shell

/etc/shells这个文件

再/etc/passwd这个文件中最后一个内容代表登陆后使用的shell类型

![image-20210913091729821](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210913091729821.png)

- 对于shell一般能存1000条命令,对于上次登陆的命令都留在~/.bash_history,而这一次都放在内存中
- 如果安装了bash-completion按tab时可以进行(选项/参数补齐)功能
- alise别名功能,举个例子 ll=ls -l --color=auto

- 使用type来查看是否为内置命令
  - type [-tpa] name
  - -t 会显示一些其他属性
  - -p 如果接name为外部命令才会显示完整名称
  - -a 会由path变量定义的路径中所有含有name的命令都列出来

### 环境变量

使用echo $PATH可以输出PATH变量

使用=可以对环境变量进行改变

> [root@YH mail]# YH=1
>
> [root@YH mail]# echo $YH
> 1

但是必须符合以下规则

- 变量与变量内容必须用=连接

- 等号两边不能有空格

- 变量名只能是字母或数字,但是不能是数字开头

- 双引号内如果有特殊字符,则会保留原意

  - >[root@YH ~]# YH=$PATH
    >[root@YH ~]# echo $YH
    >/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin

- 单引号内如果有特殊字符则不会保留原意

  - >[root@YH ~]# YH='$PATH'
    >[root@YH ~]# echo $YH
    >$PATH

- 使用``或者$()可以在命令中使用命令

  - > cd /lib/modules/\`uname -r\`/kernel
    >
    > 其中uname -r的值为
    >
    > [root@YH kernel]# uname -r
    > 3.10.0-1160.11.1.el7.x86_64

- 使用unset进行取消设置



shell一般有两种登录方式而这两种登录方式读取根据文件读取的shell也不一样

我们普通的登录时用密码就称为login,而使用Xwindows登录时第一次会输入密码再然后就不用输入密码了这种成为non-login

login启动时会读取以下两个配置文件:

- /etc/profile:这是系统整体的设置
- ~/.bash_profile或~/.bash_login或~/.profile:属于用户个人设置

#### bash环境配置文件

https://blog.csdn.net/qq_36121238/article/details/103473054

太多了。。。



#### 终端的环境设置

- stty(set tty)

  - 查看所有特殊字体 ^代表[CTRL]

  - >speed 38400 baud; rows 31; columns 120; line = 0;
    >intr = ^C; quit = ^\; erase = ^?; kill = ^U; eof = ^D; eol = <undef>; eol2 = <undef>; swtch = <undef>; start = ^Q;
    >stop = ^S; susp = ^Z; rprnt = ^R; werase = ^W; lnext = ^V; flush = ^O; min = 1; time = 0;
    >-parenb -parodd -cmspar cs8 -hupcl -cstopb cread -clocal -crtscts
    >-ignbrk -brkint -ignpar -parmrk -inpck -istrip -inlcr -igncr icrnl ixon -ixoff -iuclc -ixany -imaxbel -iutf8
    >opost -olcuc -ocrnl onlcr -onocr -onlret -ofill -ofdel nl0 cr0 tab0 bs0 vt0 ff0
    >isig icanon iexten echo echoe echok -echonl -noflsh -xcase -tostop -echoprt echoctl echoke
    >
    >intr:发送中断的信号给目前正在run的程序
    >
    >quit:发送一个quit给正在run的程序
    >
    >erase:向后删除字符
    >
    >kill:删除在目前命令行上的所有文字
    >
    >eof:end of file 代表[输入结束]
    >
    >start:在某个程序停止后重新启动他的output
    >
    >stop:停止目前屏幕的输出
    >
    >susp:送一个terminal stop 的信号给正在运行的程序

- set(可以设置以下额外的终端环境但是书上不建议修改)

  - https://www.runoob.com/linux/linux-comm-set.html



### 查看环境变量

- 使用env命令

  - >XDG_SESSION_ID=5449               <\=\=\=主机名
    >HOSTNAME=YH                
    >TERM=xterm                <\=\=\=使用的终端环境类型
    >SHELL=/bin/bash                 <\=\=\=使用的bash类型
    >HISTSIZE=3000                        <\=\=\=记录的命令个数
    >SSH_CLIENT=144.12.230.5 48025 22
    >OLDPWD=/usr/local/YH                <\=\=\=上一个工作目录
    >SSH_TTY=/dev/pts/6
    >USER=root                          <\=\=\=使用者名称
    >LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=01;05;37;41:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=01;36:*.au=01;36:*.flac=01;36:*.mid=01;36:*.midi=01;36:*.mka=01;36:*.mp3=01;36:*.mpc=01;36:*.ogg=01;36:*.ra=01;36:*.wav=01;36:*.axa=01;36:*.oga=01;36:*.spx=01;36:*.xspf=01;36:
    >MAIL=/var/spool/mail/root                        <\=\=\=mailbox的位置 
    >PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
    >PWD=/usr/local
    >LANG=en_US.utf8                <\=\=\=语言                             
    >SHLVL=1
    >HOME=/root                   <\=\=\=用户目录  
    >LOGNAME=root
    >SSH_CONNECTION=144.12.230.5 48025 172.17.0.5 22
    >LESSOPEN=||/usr/bin/lesspipe.sh %s
    >PROMPT_COMMAND=history -a; history -a; printf "\033]0;%s@%s:%s\007" "${USER}" "${HOSTNAME%%.*}" "${PWD/#$HOME/~}"
    >XDG_RUNTIME_DIR=/run/user/0
    >HISTTIMEFORMAT=%F %T 
    >_=/usr/bin/env

- 使用set观察(包含环境变量与自定义变量)

  - 其中PS1这个变量用于改变[11:08:32 root@YH YH]# 
  - 很多就不细讲了

- $?上一个命令的返回值一般正常返回0

- $$查看bash的pid

- OSTYPE、HOSTTYPE、MACHTYPE

- export将自定义变量转换为环境变量，子进程可以使用父进程的环境变量但是无法使用自定义变量

  - export [变量名称]如果不写变量名称会把所有环境变量都列出来

- 使用locale可以显示出所有支持的语系



### 设置变量

- 使用read可以交互式设置变量

  - read [-p提示字符t等待秒数] variable将数据读取到variable变量中

- declare,typeset:声明变量类型,如果declare后面没有任何参数直接显示出所有的变量名与内容与set差不多,

  - declare [-a数组-i整形-x与export一样-r设为readonly]

> 设置永久变量https://blog.csdn.net/u010794523/article/details/38622275/




### 使用ulimit

由于Linux可以多个用户同时登录使用所有要为每个用户设置资源占比

- ulimit

  - -a 　显示目前资源限制的设定。
  - -c <core文件上限> 　设定core文件的最大值，单位为区块。
  - -d <数据节区大小> 　程序数据节区的最大值，单位为KB。
  - -f <文件大小> 　shell所能建立的最大文件，单位为区块。
  - -H 　设定资源的硬性限制，也就是管理员所设下的限制。
  - -m <内存大小> 　指定可使用内存的上限，单位为KB。
  - -n <文件数目> 　指定同一时间最多可开启的文件数。
  - -p <缓冲区大小> 　指定管道缓冲区的大小，单位512字节。
  - -s <堆叠大小> 　指定堆叠的上限，单位为KB。
  - -S 　设定资源的弹性限制。(警告)
  - -t <CPU时间> 　指定CPU使用时间的上限，单位为秒。
  - -u <程序数目> 　用户最多可开启的程序数目。
  - -v <虚拟内存大小> 　指定可使用的虚拟内存上限，单位为KB。


### 删除变量中的内容

#从前面删除[最小匹配]  ${变量#关键字}

##从前面删除[最大匹配]  ${变量##关键字}

%从后面删除[最小匹配]  ${变量%关键字}

%%从后面删除[最大匹配]  ${变量%%关键字}

/替换符合字符串的第一个内容  ${变量/旧字符串/新字符串}

//替换符合字符串的所有字符串  ${变量//旧字符串/新字符串}



### 别名

alise 

命名规则与变量命名规则几乎一样

alise 别名='命令 选项'

### history

查看历史命令的命令

- n:最近n条命令
- -c:清除目前的shell中所有history内容
- -a:将目前新增的history写入到histfiles中默认写入~/.bash_history
- -r:将目前的history记录内容读到shell的histfiles中
- -w:将目前history内容写入histfiles中

使用!命令

- !!执行上一条命令
- !n执行第n条命令
- !vi执行近来vi开头的命令

### 命令执行顺序

- 包含绝对或者相对路径的命令
- 由alias命令执行
- 由bash内置的命令执行
- 通过$PATH变量来找到第一个命令执行



#### bash的欢迎信息

/etc/issue

/etc/motd



## 通配符与特殊符号

| 符号  | 意义                                                |
| ----- | --------------------------------------------------- |
| *     | 任意多个字符                                        |
| ?     | 任意一个字符                                        |
| [abc] | abc中的一个                                         |
| [-]   | 如果[]中有-代表从哪到哪例如[1-9]标识从1-9中任意一个 |
| [^]   | 不含有的意思例如[\^abc]里面只要没有abc就满足        |



特殊符号

| #                                                       | 注释                         |
| ------------------------------------------------------- | ---------------------------- |
| \                                                       | 转义符                       |
| \|                                                      | 管道                         |
| ;                                                       | 连续命令执行分隔符           |
| ~                                                       | 家                           |
| $                                                       | 变量前导符                   |
| &                                                       | 任务管理:将命令变成后台任务  |
| !                                                       | not的意思                    |
| /                                                       | 目录符号                     |
| >，>>                                                   | 输出流重定向分别时替换和累加 |
| <,<<                                                    | 输入流重定向                 |
| ''                                                      | 不具有变量替换功能           |
| ""                                                      | 具有变量替换功能             |
| ``    | 可以执行的命令或使用$()例如在""想要执行命令"``" |                              |
| ()                                                      | 为子shell的开始与结束        |
| {}                                                      | 命令区块的组合               |



## 数据流重定向

重定向数据表示

0=keyboard 键盘输入,并返回在前端 

1=monitor 正确返回值 输出到前端 

2= monitor 错误返回值 输出到前端

echo $? 输出上一个的状态码,不是0就是错误

- \>:标准输出流到指定位置并进行覆盖

  - > 一般来说, "1>" 通常可以省略成 ">". 

- \>\>:标准输出流到指定位置并进行叠加

- <:标准输入流,将命令所需的字符串通过流进行输入

- <<:标准输入流,将命令所需的字符串通过键盘输入并设置结束标志

  - ![111](D:\Code\pojo\Blog\BlogHexo\public\img\linux.png)

- 2\>:标准错误流到指定位置并进行覆盖

- 2\>\>:标准错误流到指定位置并进行叠加

> 如果错误流和正确流都要写入一个文件可以使用2>&1 或者 &>
>
> [root@YH YH]# find /home -name .bashrc &> $WORK/error
> [root@YH YH]# cat error
> /home/stranger/.bashrc
> [root@YH YH]# find /home -name .bashrc > $WORK/error 2>&1
> [root@YH YH]# cat error
> /home/stranger/.bashrc
> [root@YH YH]# find /home -name .bashc > $WORK/error 2>&1
> [root@YH YH]# cat error
> [root@YH YH]# find /home -name .b* > $WORK/error 2>&1
> [root@YH YH]# cat error
> /home/stranger/.bashrc
> /home/stranger/.bash_logout
> /home/stranger/.bash_profile
> [root@YH YH]# find /home -nam .b* > $WORK/error 2>&1
> [root@YH YH]# cat error
> find: unknown predicate `-nam'



### 多命令运行

- ; :使用这个符号可以让命令按照循序执行

  - > 例如在关机前进行同步
    >
    > sync;shutdown

- && :前面的命令执行成功后才会执行后面的命令

  - > ls /home/aaa && mkdir /home/aaa/bbb
    >
    > 如果存在 /home/aaa则建立文件夹否则不建立

- || :如果前面的命令执行成功则不执行后面的

  - > ls /home/aaa || mkdir /home/aaa
    >
    > 如果目录不存在则新建一个目录

注意这里||的优先级和&&一样

或者使用test命令来实现上面的功能

### 管道

用来处理标准输出流的信息例如:

>[root@YH YH]# ls | more
>1.txt
>1.txt.gz
>cat
>error
>etc.tar.bz2
>etc.tar.gz
>etc.tar.xz
>h
>Java语言程序设计.进阶篇.原书第10版.pdf
>services.[gz
>test.img
>vitest

==管道命令必须能够接受来自前一个命令的数据成为标准输入继续处理才行==

例如2>&1可以使用管道来修改达到同样的操作



#### 与管道配合使用的命令

- cut:将输入流中的数据进行分割

  - -d '分隔符':按照字符进行分割常常与-f使用

  - -f :取出分割的第n个片段

  - >[root@YH YH]# echo $PATH | cut -d ':' -f 1,3
    >
    >取出第一个和第三个
    >
    >/usr/local/sbin:/usr/sbin
    >[root@YH YH]# echo $PATH
    >/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin

  - -c取出固定字符区间的所有字符

  - >[root@YH YH]# export
    >declare -x HISTSIZE="3000"
    >declare -x HISTTIMEFORMAT="%F %T "
    >declare -x HOME="/root"
    >declare -x HOSTNAME="YH"
    >declare -x LANG="en_US.utf8"
    >declare -x LESSOPEN="||/usr/bin/lesspipe.sh %s"
    >declare -x LOGNAME="root"
    >
    >使用前
    >
    >[root@YH YH]# export | cut -c 12-
    >HISTSIZE="3000"
    >HISTTIMEFORMAT="%F %T "
    >HOME="/root"
    >HOSTNAME="YH"
    >LANG="en_US.utf8"
    >LESSOPEN="||/usr/bin/lesspipe.sh %s"
    >LOGNAME="root"
    >
    >使用后

- grep分析并找出需要的数据

  - -A :找出匹配的行之后的n行

  - -B :找出匹配的行之间的n行

  - -v :将不包含的行列出来

  - -n :显示行号

  - -i :忽略大小写

  - >[root@YH ~]# dmesg | grep -n --color=auto '0x80000000-0xfeffbfff'
    >100:[    0.000000] e820: [mem 0x80000000-0xfeffbfff] available for PCI devices
    >[root@YH ~]# dmesg | grep -n -A3 -B2 --color=auto '0x80000000-0xfeffbfff'
    >98-[    0.000000] PM: Registered nosave memory: [mem 0x000a0000-0x000effff]
    >99-[    0.000000] PM: Registered nosave memory: [mem 0x000f0000-0x000fffff]
    >100:[    0.000000] e820: [mem 0x80000000-0xfeffbfff] available for PCI devices
    >101-[    0.000000] Booting paravirtualized kernel on KVM
    >102-[    0.000000] setup_percpu: NR_CPUS:5120 nr_cpumask_bits:1 nr_cpu_ids:1 nr_node_ids:1
    >103-[    0.000000] percpu: Embedded 38 pages/cpu s118784 r8192 d28672 u2097152

  - 还可以使用正则表达式进行查找,不过命令太多了

  - >[root@YH YH]# ls | grep '.'
    >1.txt
    >1.txt.gz
    >cat
    >error
    >etc.tar.bz2
    >etc.tar.gz
    >etc.tar.xz
    >h
    >Java语言程序设计.进阶篇.原书第10版.pdf
    >services.[gz
    >test.img
    >vitest
    >[root@YH YH]# ls | grep 'h'
    >h

- sort:排序

  - -f:忽略大小写
  - -b:忽略最前面空格
  - -M:月份名称排序
  - -n:使用纯数字进行排序
  - -r:反向排序
  - -u:uniq相同的数据中,仅出线一行代表
  - -t:分隔符号,使用Tab来分割
  - -k:以哪个区间进行排序

- uniq:去重

  - -i:忽略大小写
  - -c:进行计数

- wc:统计字符

  - -l 多少行
  - -w 多少字母
  - -m 多少字符

- tee:可以将输出的数据流一份给文件一份

- tr [-ds] set1 set2:进行替换或者删除字符

  - -d 删除set1字符

  - -s 替换掉重复的字符

  - > last | tr '[a-z]' '[A-Z]'

- join:两个文件中传递数据

  - -t:设置分隔符默认时空格

  - -i:忽略大小写

  - > join -t ':' /etc/passwd /etc/shadow
    > root:x :0:0:root:/root:/bin/bash:$1$ZsTljEDo$JK/OTgZctSUcDODqjDrg.0:18880:0:99999:7:::
    > bin:x :1:1:bin:/bin:/sbin/nologin:*:17834:0:99999:7:::
    > daemon:x : 2:2:daemon:/sbin:/sbin/nologin:*:17834:0:99999:7:::
    > adm:x :3:4:adm:/var/adm:/sbin/nologin:*:17834:0:99999:7:::

  - 建议在连接两个文件之前先排序

- paste:和join差不多

- expand:将tab转换为空格

- split:将大文件进行划分

  - -l 按行划分
  - -b 按大小划分

- xargs:https://www.runoob.com/linux/linux-comm-xargs.html

  - emmmmmm,呃呃呃呃呃呃呃呃呃呃呃呃呃呃,啊啊啊啊啊啊啊啊啊啊啊

- 关于-:

  - 当我们使用了一个必须添加文件名的命令可以使用-来进行代替



## 正则与格式化处理文件

正则分为:基础正则表达式与扩展表达式

查找方式是or而不是and

对字符串排序影响语系意义的则会对正则结果有影响

>举个例子
>
>LANG＝C的英文编码顺序为01234....ABCD...abcd
>
>但是下面的编码顺序为
>
>LANG=zh_CH时编码顺序为:01234...aAbBcCdDeE
>
>如果使用[A-B]第一个语系会正确的选取到AB两个字母但是下面则会额外的选取到b这个字符

这里演示一般使用grep命令



由于语系有问题所以可能会用到一些符号

| 特殊符号   | 代表意义                                    |
| ---------- | ------------------------------------------- |
| [:alnum:]  | 所有英文字母以及数字                        |
| [:alpha:]  | 任何大小写字母                              |
| [:blank:]  | 空格或tab                                   |
| [:cntrl:]  | 控制按键                                    |
| [:digit:]  | 数字                                        |
| [:graph:]  | 除了空格与tab所有按键                       |
| [:lower:]  | 小写字母                                    |
| [:print:]  | 可以被打印出来的字母                        |
| [:punct:]  | 标点符号                                    |
| [:upper:]  | 大写字母                                    |
| [:space:]  | 会产生空格的字符                            |
| [:xdigit:] | 十六进制的数字类型、因此包含0~9、A~F、a~f等 |

- [] :括号内的字符任意一个都可以进行匹配

- \- :代表连续的字符

  - 例如查找a到z包含的字符串

  - > grep -n '[a-z]' re.txt

- ^ :如果被包含在中括号内则代表不包含的意思否则代表以后面那个字符开头的意思

  - >[root@YH YH]# grep -n '^[\^a-zA-Z]' regular_express.txt 
    >1:"Open Source" is a good mechanism to develop programs.
    >21:# I am VBird

    代表不以英文字母开头

- $ :代表以前面那个字符结尾

  - 如果想要找出空行可以使用

  - > ^&例如
    >
    > [root@YH YH]# grep -n '^$' regular_express.txt 
    > 22:

- . :匹配任意一个字符

- \* :重复前一个字符0~无穷次

- \+ :重复前一个字符1~无穷次,必须使用-e 或egrep

- ? :重复前一个字符0~1次,必须使用-e 或egrep

- | :或者 ,必须使用-e 或egrep

- ():群组没太看懂,必须使用-e 或egrep

- ()+:多重复群组判别,必须使用-e 或egrep

- {}:包含前一个字符的特定个数例如(注意因为{}都是bash的特殊字符所以要转义)

  - >'o\\{2\\}'
    >
    >找出连续的两个o
    >
    >[root@YH YH]# grep -n 'o\{2\}' regular_express.txt 
    >1:"Open Source" is a good mechanism to develop programs.
    >2:apple is my favorite food.
    >3:Football game is not use feet only.
    >9:Oh! The soup taste good.
    >18:google is the best tools for search keyword.
    >19:goooooogle yes!
    >[root@YH YH]# grep -n 'o\{4\}' regular_express.txt 
    >19:goooooogle yes!

找出所有不是空行且非注释的内容

>grep '^[\^#]' /etc/rsyslog.conf --color=auto > /usr/local/YH/2.txt 
>
>grep -v '^$' /etc/rsyslog.conf | grep -v '^#' --color=auto  > /usr/local/YH/1.txt
>
>上面两个语句结果一致(第一个我写的第二个书上写的)
>
>可以使用多窗口来同时比对结果

- printf:格式化输出

  - >https://www.linuxprobe.com/linux-printf-example.html

    ```
    [root@localhost ~]# printf "姓名：%s\n身高：%dcm\n体重：%dkg\n" "小明" "180" "75"
    姓名：小明
    身高：180cm
    体重：75kg
    ```

- sed: sed [-nerf] [操作]

  - -n :使用安静模式,只有讲过特殊处理的行或操作才列出来

  - -e :直接在命令行上进行sed操作编辑

  - -f :直接将sed操作写在一个文件内 -f filename 

  - -r :sed的操作使用的是扩展型正则

  - -i :直接修改读取的文件内容

  - 操作说明:[n1[,n2]] function $代表最后一行

    - a 新增

    - >[root@YH YH]# nl /etc/passwd | sed  '3a sadfasdfas\     
      >\> asdasfasd'
      >1	root:x:0:0:root:/root:/bin/bash
      >2	bin:x:1:1:bin:/bin:/sbin/nologin
      >3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
      >sadfasdfas
      >asdasfasd

    - c 替换

    - >nl /etc/passwd | sed  '2,20c aaaaa'
      >1	root:x:0:0:root:/root:/bin/bash
      >aaaaa
      >21	sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
      >22	postfix:x:89:89::/var/spool/postfix:/sbin/nologin

    - d 删除

    - i 插入

    - > [root@YH YH]# nl /etc/passwd | sed  '3i sadfasdfas\
      > \> adfasdfasdf'
      > 1	root:x:0:0:root:/root:/bin/bash
      > 2	bin:x:1:1:bin:/bin:/sbin/nologin
      > sadfasdfas
      > adfasdfasdf

    - p 打印

    - >显示2-5行
      >
      >[root@YH YH]# nl /etc/passwd | sed -n '2,5p'
      >2	bin:x:1:1:bin:/bin:/sbin/nologin
      >3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
      >4	adm:x:3:4:adm:/var/adm:/sbin/nologin
      >5	lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin

    - s 替换

    - >'s/要替换的字符/新的字符/g'
      >
      >[root@YH ~]# last
      >root     pts/3        153.118.34.219   Wed Sep 15 11:39   still logged in   
      >root     pts/2        153.118.34.219   Wed Sep 15 10:51   still logged in   
      >root     pts/0        153.118.34.219   Wed Sep 15 10:47   still logged in   
      >root     pts/2        153.118.34.219   Wed Sep 15 10:47 - 10:47  (00:00)    
      >root     pts/0        153.118.34.219   Wed Sep 15 10:19 - 10:47  (00:27)    
      >
      >替换前

      >[root@YH ~]# last | sed 's/  *.*$//g'
      >root
      >root
      >root
      >root
      >root
      >root
      >
      >替换后

    - 使用\来换行

    - >[root@YH YH]# nl /etc/passwd | sed '3,$d'
      >1	root:x:0:0:root:/root:/bin/bash
      >2	bin:x:1:1:bin:/bin:/sbin/nologin

    ==可以之间使用sed来修改文件,但是正则容易出现逻辑错误不建议直接修改文件==

- awk:数据处理工具

  - 适合小型的文本数据

  - >[root@YH ~]# last -n 5
    >root     pts/1        153.118.80.111   Wed Sep 15 12:20   still logged in   
    >root     pts/3        153.118.34.219   Wed Sep 15 11:39   still logged in   
    >root     pts/2        153.118.34.219   Wed Sep 15 10:51   still logged in   
    >root     pts/0        153.118.34.219   Wed Sep 15 10:47   still logged in   
    >root     pts/2        153.118.34.219   Wed Sep 15 10:47 - 10:47  (00:00)    
    >
    >wtmp begins Fri Sep 10 09:47:22 2021
    >[root@YH ~]# last -n 5 | awk '{print $1 "\t" $3}'
    >root	153.118.80.111
    >root	153.118.34.219
    >root	153.118.34.219
    >root	153.118.34.219
    >root	153.118.34.219
    >
    >这在里面可以使用 变量$1 这种
    >
    >也可以使用全局变量
    >
    >NR目前awk所处理的是第行数据
    >
    >FS 目前的分割字符
    >
    >NF 每一行拥有的字段总数
    >
    >[root@YH ~]# last -n 5 | awk '{print $1 "\t lines:" NR "\t columns:" NF "\t" $3}'
    >root	 lines:1	 columns:10	153.118.80.111
    >root	 lines:2	 columns:10	153.118.34.219
    >root	 lines:3	 columns:10	153.118.34.219
    >root	 lines:4	 columns:10	153.118.34.219
    >root	 lines:5	 columns:10	153.118.34.219
    >	 lines:6	 columns:0	
    >wtmp	 lines:7	 columns:7	Fri

    还有很多用法

    > https://www.linuxprobe.com/linux-awk-clever.html

- diff:查看两个问价你的差异

  - >[root@YH YH]# diff 1.txt 2.txt 
    >[root@YH YH]# vim 2.txt 
    >[root@YH YH]# diff 1.txt 2.txt 
    >14a15
    >
    >\> i
    >
    >通过vim在最后一行新加一个文件14a15应该是在14行后面新加了15行增加内容为i

- cmp:字节比对

- patch:这个和diff结合有点像git了

  - 首先使用diff来创建补丁文件

  - >[root@YH YH]# diff passwd.old passwd.new>passwd.patch
    >[root@YH YH]# diff -Naur passwd.old passwd.new>passwd.patch
    >[root@YH YH]# cat passwd.patch 
    >--- passwd.old	2021-09-15 14:09:27.393427041 +0800
    >+++ passwd.new	2021-09-15 14:08:20.921977408 +0800
    >@@ -1,9 +1,8 @@
    >root:x:0:0:root:/root:/bin/bash
    >bin:x:1:1:bin:/bin:/sbin/nologin
    >-daemon:x:2:2:daemon:/sbin:/sbin/nologin
    >adm:x:3:4:adm:/var/adm:/sbin/nologin
    >lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
    >-sync:x:5:0:sync:/sbin:/bin/sync
    >+asdfasdf
    >shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
    >halt:x:7:0:halt:/sbin:/sbin/halt
    >mail:x:8:12:mail:/var/spool/mail:/sbin/nologin

  - 使用补丁文件进行更新

    >[root@YH YH]# patch -p0 < passwd.patch
    >patching file passwd.old
    >[root@YH YH]# cat passwd.old
    >root:x:0:0:root:/root:/bin/bash
    >bin:x:1:1:bin:/bin:/sbin/nologin
    >adm:x:3:4:adm:/var/adm:/sbin/nologin
    >lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
    >asdfasdf

  - 使用补丁文件进行复原

    >[root@YH YH]# patch -R -p0 < passwd.patch
    >patching file passwd.old
    >
    >[root@YH YH]# head -n 5 passwd.old
    >root:x:0:0:root:/root:/bin/bash
    >bin:x:1:1:bin:/bin:/sbin/nologin
    >daemon:x:2:2:daemon:/sbin:/sbin/nologin
    >adm:x:3:4:adm:/var/adm:/sbin/nologin
    >lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin

### 区别是否可用正则

或许使用''的使用可以使用正则例如grep

不适用''不能用正则 ls(在bash下\*代表任意多个字符 例如ls v\*)



## shell

编写第一个bash脚本

>[root@YH YH]# vim hello.sh
>[root@YH YH]# cat hello.sh 
>#!/bin/bash
>echo "Hello World !"
>exit 0

第一行是用来声明这是一个shell脚本但是#为注释,exit 0是代表程序的返回值

>[root@YH YH]# sh hello.sh 
>Hello World !

执行脚本

>[root@YH YH]# vim hello.sh 
>[root@YH YH]# cat hello.sh 
>#!/bin/bash
>echo "Hello World !"
>read -p "请输入名字" firstname
>echo -e "\nYour Name ${firstname}"
>exit 0
>[root@YH YH]# sh hello.sh 
>Hello World !
>请输入名字YH
>
>Your Name YH

简单的交互式



关于计算

可以使用declare来建立数据类型的变量

$((计算式))在shell中使用这种来进行运算或使用bc来进行运算

>[root@YH ~]# echo "1\*2"|bc
>2
>[root@YH ~]# echo "2\*2"|bc
>4



### 脚本的执行方式

- 直接执行系统会给脚本再创建一个进程来执行也就是创建一个子进程所以在子进程创建的变量都会在程序结束后无效
- 使用source来执行脚本

test 用来检测文件,太多了...都是关于参数

利用[]进行判断(判断符号)

[ -z "${HOME}" ]中括号中间的两端必须有空格

- 在中括号[]内的每个组件都需要有空格来分隔;
- 中括号内的变量要用双引号引起来
- 中括号内的常数也要用双引号或单引号引起来



使用sh来调试

- sh [-nvx] *.sh
  - -n :检查语法
  - -v :执行之前,先将脚本内容输出到屏幕上
  - -x :将脚本内容输出到屏幕上 



## 用户管理

### 用户标识UID与GID

每个登录用户至少有两个ID一个是用户ID一个是UID

Linux识别用户和组大部分都是靠ID识别

用来记录UID与GID的/etc/passwd与/etc/shadow文件

首先解析/etc/passwd

```bash
[root@YH ~]# nl /etc/passwd | sed '5,$d'
1	root:x:0:0:root:/root:/bin/bash
2	bin:x:1:1:bin:/bin:/sbin/nologin
3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
4	adm:x:3:4:adm:/var/adm:/sbin/nologin
```



每一行都是以:分割

第一栏:账户名

第二栏:密码,由于放在这里不安全所以用x代替

第三栏:UID

| ID范围     | 该ID用户特性                                                 |
| ---------- | ------------------------------------------------------------ |
| 0          | 系统管理员                                                   |
| 1~999      | 除了0以外其他ID都一样只不过这个范围一般作为保留账号<br />1~200有Linux发行版本自己建立的系统账号<br />201~999:若用户有系统账号需求时可以使用UID |
| 1000~60000 | 一般用户                                                     |

第四栏:GID

第五栏:用来解释该账号用来干嘛的

第六栏:家目录

第七栏:登录后使用的shell 



讲解/etc/shadow文件

```bash
[root@YH ~]# nl /etc/shadow
     1	root:$1$ZsTljEDo$JK/OTgZctSUcDODqjDrg.0:18880:0:99999:7:::
     2	bin:*:17834:0:99999:7:::
     3	daemon:*:17834:0:99999:7:::
```

1. 账号名
2. 密码
3. 最近修改密码的日期
4. 密码不可被修改的天数(与第三字段相比)防止一改再改
5. 密码需要重新修改的天数(与第三字段相比)
6. 密码需要修改期限前的警告天数(与第五字段相比):密码再过n天就过期了
7. 密码过期后的账户宽限时间(与第五字段相比):可以使用账户权限可以登录但是过了这个时间就无法登录
8. 账号失效日期和第三个字段一样都是从1970年依赖的总天数
9. 保留

查看shadow的加密机制

```bash
[root@YH ~]# authconfig --test | grep hashing
 password hashing algorithm is md5
```



解析/etc/group

```bash
[root@YH ~]# head /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mem:x:8:
kmem:x:9:
```

1. 组名

2. 用户组密码

3. GID

4. 此用户组支持的账户

   - 由于该字段的特性所以可以设置一用户多组,下面介绍有效用户组

   - > 在/etc/passwd里GID指代的时初始用户组而初始用户组并不会将该用户写入第四字段
     >
     > 而一登陆就立即获取初始用户组
     >
     > 如果要创建文件那么使用的是有效用户组
     >
     > 使用groups查看支持的所有用户组,但是第一个支持的组就是有效用户组
     >
     > 通常有效用户组的作用就是新建用户

   - newgrp可以切换有效用户组但是使用了这个命令就进入了另一个shell环境

关于/etc/gshadow解析

```bash
[root@YH Java]# cat /etc/gshadow
root:::
bin:::
daemon:::
sys:::
adm:::
tty:::
disk:::
lp:::
mem:::
kmem:::
wheel:::
cdrom:::
mail:::postfix
```

1. 组名
2. 密码:开头为!标识无合法密码,所以无用户组管理员
3. 用户组管理员账号
4. 加入该组的用户



### 账户管理

- useradd:添加一个用户(创建时至少参考/etc/default/useradd,/etc/login.defs,/etc/skel/*这三个文件)

  - -u :输入一个uid

  - -g :初始用户组

  - -G :次要用户组

  - -M :强制不要建立家(系统账号默认)

  - -m :强制建立用户家(一般用户默认,默认权限700)

  - -c :介绍说明

  - -r :系统账户

  - -s :失效日期

  - -e :密码是否会失效

  - -D :查看默认的创建参数

    - ```bash
      [root@YH etc]# useradd -D
      GROUP=100   用户初始组id
      HOME=/home  家的基准目录
      INACTIVE=-1 密码是否会失效
      EXPIRE=     账户失效日期
      SHELL=/bin/bash 默认的shell
      SKEL=/etc/skel  用户家目录参考基准目录(从这个目录的文件复制到家里面)
      CREATE_MAIL_SPOOL=yes
      
      ```

      

如果添加用户但是没有使用passwd进行更改shadow中不会对密码进行加密(账户也会被锁定无法登录)

```bash
[root@YH etc]# cat shadow | grep test
testuser:123456:18886:0:99999:7:::

```

centos有两种机制

>私有用户组机制
>
>会创建一个与用户名一样的组因为用户有自己的组和家所以不会考虑GROUP=100这个参数
>
>公共用户机制
>
>以GROUP=100为值新建一个账号默认家目录的权限为drwxr-xr-x每个账户都属于用户组

关于UID/GID的密码参数

```bash
[root@YH etc]# cat login.defs | grep -v '^$' | grep -v '^#'
MAIL_DIR	/var/spool/mail			邮箱目录
PASS_MAX_DAYS	99999				需要修改密码的日期
PASS_MIN_DAYS	0					多久不可重新设置密码的日期
PASS_MIN_LEN    8					密码最短的字符长度(以及弃用)
PASS_WARN_AGE	7					过期警告提前日期
UID_MIN                  1000		最小UID
UID_MAX                 60000		最大UID
SYS_UID_MIN               201		系统最小UID
SYS_UID_MAX               999		系统最大UID
GID_MIN                  1000		最小GID
GID_MAX                 60000		最大GID
SYS_GID_MIN               201		系统最小GID
SYS_GID_MAX               999		系统最大GID
CREATE_HOME	yes						是否创建家目录
UMASK           077					默认权限(用最大权限删去这个就是默认权限)
USERGROUPS_ENAB yes					使用userdel删除时是否删除初始用户组(如果没有人属组与这个组就将其删除)
ENCRYPT_METHOD MD5					密码加密方式
MD5_CRYPT_ENAB yes

```

#### passwd

修改密码的简单方式

>echo "12345" | passwd --stdin stranger

但是 这样修改密码会留在历史记录里面

让密码具有60天修改10天后失效

> passwd -x 60 -i 10 stranger

不让登录

> passwd -l stranger
>
> passwd -u stranger (恢复正常)

#### chage

查看用户密码的详细参数

#### usermod

对用户属性进行调整https://www.runoob.com/linux/linux-comm-usermod.html

#### userdel

删除用户

#### id

查询用户的id

#### chfn(一般用户可以修改/etc/passwd)

修改个人信息

#### chsh(一般用户可以修改/etc/passwd)

-s：设置自己的shell



chfn、chsh属于SUID



### su与su -的区别

```bash
[stranger@YH ~]$ su root
Password: 
[root@YH stranger]# env | grep 'stranger'
USER=stranger
PATH=/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/stranger/.local/bin:/home/stranger/bin
MAIL=/var/spool/mail/stranger
PWD=/home/stranger
LOGNAME=stranger
```

看见没纵使切换用户很多变量仍然没有读取

读取的变量方式设置为非登录shell的方式

使用su -

```bash
[stranger@YH ~]$ su - root
Password: 
Last login: Thu Sep 16 19:27:17 CST 2021 on pts/3
[root@YH ~]# env | grep root
USER=root
MAIL=/var/spool/mail/root
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
PWD=/root
HOME=/root
LOGNAME=root
```

看到了吗变量变过来了

-c 用root运行一个命令



#### sudo

只有/etc/sudoers内的用户才能执行==要用visudo来去修改这个目录==

若用户执行sudo后便让用户输入自己的密码来确认

密码输入成功才进行sudo后接命令

若切换身份到相同用户不需要密码

- -u [账户]:切换到账户并执行命令



#### visudo

```bash
[root@YH etc]# cat sudoers | grep -v '^$' | grep -v '^#'
Defaults   !visiblepw
Defaults    always_set_home
Defaults    match_group_by_gid
Defaults    always_query_group_plugin
Defaults    env_reset
Defaults    env_keep =  "COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR LS_COLORS"
Defaults    env_keep += "MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE"
Defaults    env_keep += "LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES"
Defaults    env_keep += "LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE"
Defaults    env_keep += "LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY"
Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin
root	ALL=(ALL) 	ALL
%wheel	ALL=(ALL)	ALL
用户账户 登陆者的主机名 可切换身份 可执行的命令
```

- 单用户可使用root命令

  - 直接修改

- 利用wheel用户组以及免密的功能处理visudo

  - 任何加入wheel这个组的用户可以随意使用sudo当然只要修改sudoers这个表加入其他组也可以实现wheel组的效果 %后面加组名
  - 如果要免密就要在sudoers相关行的最后加入NOPASSWD:ALL

- 有限制的命令操作

  - >user	ALL=(ALL) 	/usr/bin/passwd   <\=\=这个路径必须是绝对路径
    >
    >user	ALL=(ALL) 	/usr/bin/passwd, !/usr/bin/passwd root   <\=\=这个路径必须是绝对路径

- 通过别名创建

  - sudoers

  - >User Alias ADMPW = pro1,pro2,pro3,pro4
    >
    >Cmnd Alias ADMPWCOM = !/usr/bin/passwd, /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root
    >
    >ADMPW ALL=(root) ADMPWCOM 
    >
    >//通过上面的写入将四个pro起别名并且权限也起别名进行统一管理

  - 别名必须全大写

- sudo的时间间隔

  - 输入了一次密码五分钟之内无需再输入就可执行sudo操作

- 输入自己的密码进入root

  - >visudo
    >
    >User Alise ADMINS = pro1,pro2
    >
    >ADMINS ALL=(root) /bin/su -



## 用户组管理

- groupadd :新建组
- groupmod :对组进行修改
- groupdel :删除组(如果有某个用户的初始组占用着该组则无论如何也无法删除)
- gpasswd groupname :用户组管理员功能
  - :无参是groupname密码
  - -A user:将管理权交给user



### 特殊的shell,/sbin/nologin

该shell无法登录并且如果存在/etc/nologin.txt登录后会显示文本内的内容

```bash
[root@YH etc]# useradd -s /sbin/nologin user4
[root@YH etc]# su user4
不让登录
[root@YH etc]# cat nologin.txt 
不让登录
```



### PAM 同一认证模块

> PAM使用配置/etc/pam.d/下的文件，来管理对程序的认证方式.应用程序 调用相应的配置文件，从而调用本地的认证模块.模块放置在/lib/security下，以加载动态库的形式进，像我们使用su命令时，系统会提示你输入root用户的密码.这就是su命令通过调用PAM模块实现的。

```bash
[root@YH pam.d]# ls
atd          fingerprint-auth     password-auth     remote          smartcard-auth-ac  sudo            systemd-user
chfn         fingerprint-auth-ac  password-auth-ac  runuser         smtp               sudo-i          vlock
chsh         login                polkit-1          runuser-l       smtp.postfix       su-l
config-util  other                postlogin         setup           sshd               system-auth
crond        passwd               postlogin-ac      smartcard-auth  su                 system-auth-ac
[root@YH pam.d]# cat passwd
#%PAM-1.0
auth       include	system-auth
account    include	system-auth
password   substack	system-auth
-password   optional	pam_gnome_keyring.so use_authtok
password   substack	postlogin
```

- 一共有三个字段
  - 第一个字段验证类别
    - auth:主要用来检验用户的身份,这种类别通常是需要密码来验证
    - account:验证用户是否具有权限
    - session:这次登录期间的会话管理,记录用户的登录注销
    - password:用于认证修订,例如修改密码
    - 这四种一般是有顺序的
  - 第二个字段控制标准
    - required:不论成功还是结束都会执行后续,例如日志
    - requisite:若验证失败则立刻返回源程序的failure标志,并种终止后续的验证流程
    - sufficient:若成功则立刻返回success给源程序并终止后续的认证流程
    - optional:大多数用于显示信息不用于认证
    - include代表认证交给后面的文件作为这个类别的认证
- PAM流程:
  - 使用passwd进行介绍
  - 用户开始执行PAM模块
  - passwd调用PAM进行验证
  - PAM到/etc/pam.d中找到与程序同名的配置文件
  - 根据文件内的配置进行设置
  - 验证结果返回
  - passwd返回的结果进行下一步
- PAM常用模块https://blog.csdn.net/weixin_34311757/article/details/92873089
  - /etc/pam.d/*:每个程序的PAM配置文件
  - /lib64/security/*:PAM模块文件的实际放置目录
  - /etc/security/*:其他PAM环境的配置文件
  - /usr/share/doc/pam-*/:详细的PAM说明文件
  - limits.conf系统管理员可以使用这个账户统一管理,对于已经登录的用户无效
  - /var/log/secure :记录模块发生的问题
  - /var/log/messages:记录模块发生的问题



## 有关用户

### 查询

- w
- who
- last
- lastlog



### 用户对谈

- write

  - root

  - ```bash
    [root@YH pam.d]# who
    root     pts/0        2021-09-17 09:41 (153.118.189.29)
    stranger pts/1        2021-09-17 10:48 (153.118.189.29)
    [root@YH pam.d]# write stranger aaa
    write: stranger is not logged in on aaa
    [root@YH pam.d]# write stranger pts/2
    write: stranger is not logged in on pts/2
    [root@YH pam.d]# write stranger pts/1
    hello
    ai
    
    Message from stranger@YH on pts/1 at 10:50 ...
    nixiangganma^[[D^[[D
    
    ```

    

  - stranger

  - ```bash
    [stranger@YH ~]$ 
    Message from root@YH on pts/0 at 10:49 ...
    hello
    ^C
    [stranger@YH ~]$ ai
    
    [stranger@YH ~]$ 
    [stranger@YH ~]$ write root pst/0
    write: root is not logged in on pst/0
    [stranger@YH ~]$ who
    root     pts/0        2021-09-17 09:41 (153.118.189.29)
    stranger pts/1        2021-09-17 10:48 (153.118.189.29)
    [stranger@YH ~]$ write root pts/0
    nixiangganma^[[D^[[D
    
    ```

  - 

- mesg:使用mesg n可以不接受write的消息但是如果对方是root则强制手下

- wall

### 检查账号

- pwck:用于检查/etc/passwd
- pwconv:将/etc/passwd中的账号密码移动到/etc/shadow中
- chpasswd:读入未加密的数据进行加密后写入/etc/shadow

> 创建大量账号模板passwd --stdin
>
> 还需要一个shell脚本



## 磁盘配额

注意以下几点:

- ext文件系统只能针对整个文件系统设置磁盘配额
- xfs可以使用project能够针对目录进行配额
- 内核必须支持磁盘配额(Centos7默认支持并开启)
- Centos默认开启SELinux,由于SELinux的限制只能配置/home的额度
- 不同的文件下系统有着不同的限额方式,限额之前必须知道文件系统
- xfs可以针对下面的几部分进行配额:
  - 分别对用户、用户组或个别目录进行配置
  - 容量限制或文件数量限制
  - 限制inode使用量:管理用户可以建立的[文件数量]
  - 限制block使用量:管理磁盘容量
  - 软硬限制(不是软连接和硬链接)
    - hard:用户绝不能超过这个值,超过则会锁定用户的磁盘使用权
    - soft:表示用户低于时可以正常使用如果超过这个值,系统会在用户登录时进行警告并给予宽限时间,如果用户在这段时间内没有把容量降低到soft以下则将hard的值降低到soft



#### 配额测试

进行操作时最好注销所有用户,并且不能有语法错误否则可能导致无法正常启动

​	过去版本通常使用mount -o remount 进行重新挂载开启磁盘配额功能

​	但是xfs似乎在挂载之初就声明了配额功能所有要修改/etc/fstab编辑该文件并在defaults后加入,usrquota,grpquota

配额选项有以下三种:

- uquota/usrquota/quota:针对账号
- gquota/grpquota:针对组
- pquota/prjquota:针对单一目录的设置,不可与grpquota同时存在

磁盘配额命令quote

- xfs_quote -x -c "命令" [挂载点]
- -x:专家模式
- -c:后面要接命令
- 命令 
  - print:打印信息
  - df:与df一样
  - report:列出目前的磁盘配额
  - state:目前支持磁盘配额的文件系统信息



## 软件磁盘阵列

磁盘阵列就是将多个较小的磁盘组成一个较大的磁盘设备，使用RAID可以将这些设备视为一个整体，一共有五个模式

- RAID 0(等量模式):性能最佳
  - 将所有的数据进行分块写入不同的磁盘,这样读写速度快但是一旦有一块磁盘损害都可能造成数据的大量丢失
- RAID 1(镜像模式):完全备份
  - 同一份数据保存在两个磁盘上
- RAID 1+0,RAID 0+1(推荐1+0,因为这样磁盘越多速度越快)
  - 可以先将数据进行分块再将数据分别复制,并将原数据与复制数据一起写入,如下图:
  - ![image-20210922093728257](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210922093728257.png)
- RAID 5:性能与数据备份的均衡考虑
  - 需要三个以上的磁盘
  - 采用RAID 0 的方式写入但是会进行奇偶校验将校验的结果放在不同盘,因此总体容量会少一块磁盘因为要放校验数据,如果一个磁盘损坏可以使用校验值来进行修复但是如果==两个或以上损坏则无法恢复==
- RAID 6:和5一样的做法但是使用两个磁盘作为奇偶校验结果,因此保证两个磁盘数据恢复

> 关于磁盘阵列的热备份(最好支持热插拔)
>
> 举个例子如果我安装了10快磁盘但是只用9块,某天系统发现其中一块损坏会将数据备份到那个没用到的磁盘上

- 优点:数据的可靠性与速度,容量得到提升

|                        | RAID 0      | RAID 1   | RAID 1 0       | RAID 5   | RAID 6   |
| ---------------------- | ----------- | -------- | -------------- | -------- | -------- |
| 最少磁盘数             | 2           | 2        | 4              | 3        | 4        |
| 最大容错数量(数据恢复) | 无          | n-1      | n/2            | 1        | 2        |
| 安全性                 | 无          | 最好     | 最好           | 好       | 好       |
| 写入性能               | n           | 1        | n/2            | <n-1     | <n-2     |
| 读出性能               | n           | n        | n              | <n-1     | <n-2     |
| 可用容量               | n           | 1        | n/2            | n-1      | n-2      |
| 应用                   | 速度,不备份 | 数据备份 | 服务器、云系统 | 数据备份 | 数据备份 |

==磁盘阵列的设备名为/dev/sd[a-p]==

软件磁盘阵列因为是系统模拟的所有名字为/dev/md[0-9]



#### 使用mdadm 进行软件磁盘阵列设置

mdadm --create /dev/md[0-9] --auto=yes --level=[015] --chunk=NK --raid-devices=N --spare-devices=N 设备名

- 设备名可以是磁盘也可以是分区但这些设备名总数加起来要 等于 --raid-devices=N --spare-devices=N这俩加起来的数量
- --create 建立RAID
- --auto=yes
- --chunk=NK 决定这个磁盘的chunk大小,也可以当成stripe大小,一般是64K或512K
- --raid-devices=N 使用几个磁盘分区
- --spare-devices=N 使用几个磁盘作为备用磁盘(热备份)
- --level=[015] 就是RAID 0,RAID 1,RAID 5这三个模式
- --detail 列出设备的详细信息

进行恢复

- --manage 设备
  - --add 设备:添加设备到md
  - --remove 设备:将设备从md删除
  - --fail :将设备设为出错

使用软件进行阵列设置后还能使用mkfs.xfs对虚拟后的/dev/md0(这个根据需求选择)进行格式化

mdadm的配置文件/etc/mdadm.conf

### RAID默认是自动开启且挂载如果不想用了需要关闭RAID



## 逻辑卷管理器

#### 建立LVM流程

- 进行分区并将system ID设为8e
- PV阶段
  - pvcreate:将分区建为PV
  - pvscan:扫描系统里面所有的PV磁盘
  - pvdisplay:显示出系统上面PV状态
  - pvremove:将PV属性删除
- 建立VG阶段
  - vgcreate:建立VG命令
  - vgscan:检查VG是否存在
  - vgdisplay:显示VG状态
  - vgextend:再VG内增加PV
  - vgreduce:在VG内删除PV
  - vgchange:设置VG是否启动
  - vgremove:删除一个VG

- LV阶段
  - lvcreate:建立LV
  - lvscan:查询系统上面的LV
  - lvdisplay:查看LV状态
  - lvextend:给LV增加容量
  - lvreduce:在LV里面减少容量
  - lvremove:删除LV
  - lvresize:重新调整大小
- 对LV进行格式化



剩下的操作还有扩大LV容量,让LVM具有自动调整功能,创建快照,关闭与开启LVM

#### 扩容

可以直接使用lvresize -L +500M LV分区(使用vgdisplay vg分区 ,来查看) 来进行直接分区

使用这个方法vg必须有剩余的空间,扩容完毕后使用xfs_growfs /srv/lvm进行更新



==xfs只能放大,ext4能放大能缩小==



## 计划任务

Linux常见的计划任务有

- 日志的轮询(logrotate):由于日志会越来越大大型文件不但占容量还会影响读写,所有有时候需要将新数据与旧数据分开存放

- 日志文件分析(logwatch):由于日志文件很乱所有一般不用vim等去查看而是使用logwatch查看登录信息

- 建立locate数据库:存储已经存在的文件名,存放在/var/lib/mlocate,通过update自动更新
- manpage查询数据库:和locate类似,提供快速查询,需要执行mandb才能创建通过系统计划任务来自动执行
- RPM软件日志,用来跟踪软件
- 删除缓存(tmpwatch)
- 与网络有关的分析例如apache

有很多软件也会自动添加计划任务

### 只执行一次的任务

首先我们需要开启单一计划服务atd

```bash
[root@YH ~]# systemctl restart atd
[root@YH ~]# systemctl enable atd
[root@YH ~]# systemctl status atd
● atd.service - Job spooling tools
   Loaded: loaded (/usr/lib/systemd/system/atd.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2021-09-22 12:27:24 CST; 20s ago
 Main PID: 10183 (atd)
   CGroup: /system.slice/atd.service
           └─10183 /usr/sbin/atd -f

Sep 22 12:27:24 YH systemd[1]: Started Job spooling tools.
```

必须enable和running才是真正的启动了

- at

  - 实际上我们使用at是以文本方式写入/var/spool/at/目录里面

  - 但是由于这个命令可能很危险所有会有/etc/at.allow与/etc/at.deny来实现对at的限制

    - 系统会先找at.allow这个文件,没有在这个文件的用户都不能使用at,如果不存在才会区查询at.deny
    - 如果两个文件都不存在那么只有root才能使用

  - at [-mldv] 时间

    - -m:即使没有输出信息,亦发email通知使用者

    - -l:相当于atq,列出所有该使用者的计划

    - -d:相当于atrm,取消一个计划

    - -v:可以使用较明显的时间格式列出at

    - -c:可以列出后面接的任务实际命令内容

    - 时间

      - HH:MM 今天的某一时刻
      - HH:MM YYYY-MM-DD:某一天执行

    - ```bash
      [root@YH ~]# at now + 5 minutes
      at> /bin/mail -s ""^[[D^H
      at> ^[[A
      at> /bin/mail -s "testing at job" root < /root/.bashrc
      at> <EOT>
      job 1 at Wed Sep 22 13:55:00 2021
      [root@YH ~]# at -l
      1	Wed Sep 22 13:55:00 2021 a root
      [root@YH ~]# at -c 1
      ......省略
      cd /root || {
      	 echo 'Execution directory inaccessible' >&2
      	 exit 1
      }
      ${SHELL:-/bin/sh} << 'marcinDELIMITER671da34f'
      /bin/mail -s ""
      /bin/mail -s "testing at job" root < /root/.bashrc
      
      marcinDELIMITER671da34f
      ```

    - 如果你要定时用echo发送给终端是直接无法发送因为at与用户使用的终端无关所以要使用echo "hello">/dev/tty1来代替

  - batch:系统有时间才执行命令,cpu负载数小于0.8,时才执行(cpu负载数量单一时间负责的任务数量)每分钟检查一次

    - 可以使用这些命令来配合查看

    - uptime查看平均任务负载

    - jobs查看已挂载的程序,不用携带任何参数

    - ```bash
      [root@YH dev]# echo "scale=100000; 4*a(1)" | bc -lq &
      [1] 25512
      You have mail in /var/spool/mail/root
      [root@YH dev]# echo "scale=100000; 4*a(1)" | bc -lq &
      [2] 25531
      [root@YH dev]# echo "scale=100000; 4*a(1)" | bc -lq &
      [3] 25538
      [root@YH dev]# echo "scale=100000; 4*a(1)" | bc -lq &
      [4] 25541
      [root@YH dev]# uptime
       14:13:13 up 12 days,  4:26,  2 users,  load average: 0.87, 0.21, 0.17
      [root@YH dev]# batch
      at> .u^H^H
      at> /usr/bin/updatedb
      at> <EOT>
      job 3 at Wed Sep 22 14:13:00 2021
      [root@YH dev]# date;atq
      Wed Sep 22 14:13:54 CST 2021
      3	Wed Sep 22 14:13:00 2021 b root
      [root@YH dev]# jobs
      [1]   Running                 echo "scale=100000; 4*a(1)" | bc -lq &
      [2]   Running                 echo "scale=100000; 4*a(1)" | bc -lq &
      [3]-  Running                 echo "scale=100000; 4*a(1)" | bc -lq &
      [4]+  Running                 echo "scale=100000; 4*a(1)" | bc -lq &
      [root@YH dev]# kill -9 %1 %2 %3 %4
      [root@YH dev]# uptime
       14:17:42 up 12 days,  4:30,  2 users,  load average: 0.36, 1.11, 0.67
      You have new mail in /var/spool/mail/root
      
      ```



### 循环执行

- crontab

  - 首先与at一样也有一个/etc/cron.allow和/etc/deny用来限制使用者

  - 任务会放到/var/spool/cron中,cron执行的每一项都会记录到/var/log/cron中

  - -u :只有root才能执行这个任务

  - -e :编辑crontab的内容

  - -l :查看crontab的任务内容

  - -r :删除所有的crontab的任务内容,若仅要删除一项使用-e

  - 使用-e后vi会给你打开

  - 在vi里面加入如下命令即可保存

    ```bash
     0  12 *  *  *  mail -s "at 12:00" dmtsai</home/dmtsai/.bashrc
    #分 时 日 月 年 |--------------------命令--------------------|
    
    ```

    \*  代表任何时间都接受

    ,  代表分割时间例如 1,2 * * * *:任何时间的第一个分钟和第二分钟都会执行

    \-  代表一段时间内都会执行例如 20 8-12 * * * 代表8点到12点的20分钟都会执行

    /n 代表间隔 例如 \*/5 * * * * 每隔五秒运行一次

  - 注意-e只是针对用户如果要执行系统的则编辑/etc/crontab

  - 系统最低检测是每分钟,所以每分钟都会检测/etc/crontab和/var/spool/cron下的文件

设置系统的循环命令

>vim /etc/crontab

```bash
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
```

MAILTO=root

就代表发生错误就将信息用mail发给root可以改成自己的邮箱例如1834519329@qq.com

PATH=要查找的文件路径(找命令的路径)

==这里的命令与-e不同时间后面接的是身份==

crond服务读取配置的地方有三个:

/etc/crontab

/etc/cron.d/*

/var/spool/cron

==如果你设置了出错报告并发送邮箱结果DNS出错了,导致又要将DNS报告发送这样一直循环会浪费大量的系统资源,所以最好找一个垃圾桶==



### 可唤醒的停机期间的任务

anacron:被cron执行每小时检测一次

以防止anacron误判时间因此会放在/etc/cron.hourly里面anacron才会在文件之前加一个0让anacron最先执行以免误判crontab未执行的任务

```bash
[root@YH cron.hourly]# cat /etc/anacrontab 
# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22

#period in days   delay in minutes   job-identifier   command
1	5	cron.daily		nice run-parts /etc/cron.daily
7	25	cron.weekly		nice run-parts /etc/cron.weekly
@monthly 45	cron.monthly		nice run-parts /etc/cron.monthly

```

查看anacron的配置

天数|延迟时间|工作名称定义|要执行的命令

anacron的流程大概为:

1. 首先由/etc/anacrontab分析到cron.daily这项任务名称的天数为1天
2. 从 /var/spool/anacron/cron.daily得出最后一次的时间
3. 若相差一天则执行
4. 通过/etc/anacrontab得到将延迟5分+3小时(通过START_HOURS_RANGE设置)
5. 延迟时间过后运行run-parts /etc/cron.daily
6. 执行后结束



## 进程管理

Linux的程序一般成为fork-and-exec。进程都会借用fork(复制)的生产方式一个一摸一样的子进程

==一般程序在文件名后面加个d(daemon)代表后台进程==例如atd,crond

#### PS命令

查看当前系统正在执行的进程信息

最常用的两个命令==ps -l== ==ps aux==

![image-20210923141832324](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210923141832324.png)

其中:

1. F代表权限,4代表root权限,1代表只能复制不能执行
2. S代表状态,R代表该进程正在运行,S代表正在睡眠,T代表停止运行,D代表不可唤醒状态,Z代表僵尸状态
3. UID/PID/PPID:
   1. 此进程被该UID所拥有
   2. 此进程的PID
   3. 此进程的父PID
4. C代表CPU的使用率
5. PRI代表优先级
6. ADDR代表内存在哪部分
7. SZ代表进程用掉了多少内存
8. WCHAN代表是否在运行 - 表示正在运行
9. TTY代表登陆这终端
10. TIME代表CPU占用时间
11. CMD代表真正运行的命令(==如果这一栏有\<default\>则代表该进程为僵尸进程==)僵尸进程是因为父进程没有完整的结束该进程

![image-20210923142725115](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210923142725115.png)

用户   PID  CPU占用率  内存占用率  虚拟内存大小  固定内存大小  终端  状态(和上面的意义)  启动时间 CPU占用时间 命令

参数:

- a  显示所有进程
- -a 显示同一终端下的所有程序
- -A 显示所有进程
- c  显示进程的真实名称
- -N 反向选择
- -e 等于“-A”
- e  显示环境变量
- f  显示程序间的关系
- -H 显示树状结构
- r  显示当前终端的进程
- T  显示当前终端的所有程序
- u  指定用户的所有进程
- -au 显示较详细的资讯
- -aux 显示所有包含其他使用者的行程 
- -C<命令> 列出指定命令的状况
- --lines<行数> 每页显示的行数
- --width<字符数> 每页显示的字符数
- --help 显示帮助信息
- --version 显示版本显示

```bash
[root@YH ~]# ps -alt
F   UID   PID  PPID PRI  NI    VSZ   RSS WCHAN  STAT TTY        TIME COMMAND
4     0  1113  1106  20   0 116336  2840 do_wai Ss   pts/2      0:00 -bash
0  1000  1180  1179  20   0 116204  2720 n_tty_ Ss+  pts/3      0:00 -bash
4     0  1270     1  20   0 110208   812 n_tty_ Ss+  tty1       0:00 /sbin/agetty --noclear tty1 linux
4     0  1271     1  20   0 110208   824 n_tty_ Ss+  ttyS0      0:00 /sbin/agetty --keep-baud 115200,38400,9600 ttyS0 vt
4     0  1324  1113  20   0 153328  1504 -      R+   pts/2      0:00 ps -alt
4     0 21527 21524  20   0 116336  2852 n_tty_ Ss+  pts/0      0:00 -bash
0  1000 21618 21617  20   0 116204  2740 n_tty_ Ss+  pts/1      0:00 -bash

```

PID指代当前进程的号，PPID指代父进程的PID

>| 在Linux中这个叫管道符号 比如A|B将A命令运行的结果送给B
>
>grep 查找文件中符合条件的字符串

ps -aux|grep mysql这个命令就可以查看所有有关mysql的进程

ps -ef可以看父进程（一般不这么用看父目录可以使用pstree）



>详细请看这个网站
>
>https://www.cnblogs.com/xiangtingshen/p/10920236.html





#### kill命令

==kill命令是用来控制其他进程的不单单只是杀死==

命令参数

- -9 杀死一个进程
- -1 重启一个进程
- -15 正常停止一个进程
- -l <信息编号> 　若不加<信息编号>选项，则 -l 参数会列出全部的信息名称
- 后面一般接进程的PID如果想要管理bash任务就要用%+数字

例如

强制杀死进程

```bash
kill -9 123456
```

杀死指定用户所有进程

```bash
kill -9 $(ps -ef | grep hnlinux) //方法一 过滤出hnlinux用户进程 kill -u hnlinux //方法二
```

>https://www.runoob.com/linux/linux-comm-kill.html



#### killall

通过名称对进程下命令

- -i 代表交互式
- -e 代表后面要接的命令完全一致
- -I 忽略大小写





#### pstree命令

Linux pstree命令将所有行程以树状图显示，树状图将会以 pid (如果有指定) 或是以 init 这个基本行程为根 (root)，如果有指定使用者 id，则树状图会只显示该使用者所拥有的行程。

> https://www.runoob.com/linux/linux-comm-pstree.html



```bash
[root@YH ~]# pstree
systemd─┬─YDLive───6*[{YDLive}]
        ├─YDService───20*[{YDService}]
        ├─abrt-dbus───3*[{abrt-dbus}]
        ├─acpid
        ├─2*[agetty]
        ├─atd
        ├─auditd───{auditd}
        ├─barad_agent─┬─barad_agent
        │             └─barad_agent───2*[{barad_agent}]
        ├─crond
        ├─dbus-daemon
        ├─dhclient
        ├─iscsid
        ├─lsmd
        ├─lvmetad
        ├─master─┬─pickup
        │        └─qmgr
        ├─nginx───nginx
        ├─ntpd
        ├─polkitd───6*[{polkitd}]
        ├─rshim───{rshim}
        ├─rsyslogd───2*[{rsyslogd}]
        ├─sgagent───{sgagent}
        ├─sshd───sshd───bash───pstree
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-udevd
        ├─tat_agent───4*[{tat_agent}]
        └─tuned───4*[{tuned}]

```

可以看出所有的进程都是托管子systemd下面



#### Linux的多人多任务环境

- 多人
  - 为啥会多人呢,就是因为每个人登陆后获取的BASH的PID不同
- 多任务
  - CPU可以进行任务的切换与调度
- 多重登录的七个终端界面
  - 当前Linux提供了六个命令登录界面,以及一个图形界面可以使用ALT+F1-7来切换终端,每个终端的登录者不同
- linux不像Windows一样因为某个进程出错而要重启,只需要切换终端杀死即可
- 使用&来将进程放到后台例如
  - cp file1 file2 &
  - 这样进程就会放到后台



#### top:查看进程的变化(持续查看)

- -d 秒数:刷新间隔
- -b 以批量的方式执行top
- -n 与-b搭配代表执行几次top结果
- -p 指定某些PID来执行查看检测而已
- 是一种类似Windows任务管理器的查看方式进入top后按h可以查看到相关命令

![image-20210923144205956](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210923144205956.png)

基本分为6行

1. 显示目前时间,经过时间,已经在线人数,系统在1\5\15分钟的平均任务负载
2. 显示目前进程总数与个别进程的状态还有最后一个zombie代表僵尸进程个数
3. 显示CPU的负载
4. 4-5行表示物理内存与虚拟内存的使用情况
5. 在当前top进程中输入命令显示的地方

按下M使用内存使用率排序按P(以CPU使用率来排序)恢复按q退出







### 进程之间的相互管理

如果我没想要管理进程其实是使用一个进程来控制另一个进程,而进程之间是用信号来管理的查看信号可以使用

kill -l(放心杀不了进程)或使用man 7 signal(如果报错就用yum 安装)



### 任务管理

由于/etc/security/limits.conf文件会设置用户的连接数,某些用户只能以一个连接来工作所以出错后不能切换到别的终端进行处理,所以加入了任务管理

有些进程需要与用户进行交互所以成为前台,而不需要交互的称为后台

后台程序不能使用ctrl+c来终止

任务管理的限制:

- 这些任务的触发必须来自于你的shell子进程
- 前台:可控制与执行命令的环境称为前台
- 后台:可以自动执行的任务,可以使用bg,fg来调用
- 后台中执行的进程不能等待terminal或shell的输入



使用&可以将命令在后台运行但是建议加上流控制这样免得报错或者提示信息将控制台弄的乱七八糟.

如果想要将正在运行的程序放到后台中使用ctrl+z



列出目前后台的状态jobs

- jobs

  - -l 除了job number与命令串之外,同时列出PID

  - -r 列出正在运行的任务

  - -s 列出stop的程序

  - 结果中+代表默认使用的任务使用fg即可得到

  - ```bash
    [root@YH YH]# logout
    There are stopped jobs.
    [root@YH YH]# jobs
    [1]+  Stopped                 vim cron.txt
    [root@YH YH]# fg
    vim cron.txt
    
    [1]+  Stopped                 vim cron.txt
    [root@YH YH]# vim readAgrs.sh 
    
    [2]+  Stopped                 vim readAgrs.sh
    [root@YH YH]# jobs
    [1]-  Stopped                 vim cron.txt
    [2]+  Stopped                 vim readAgrs.sh
    [root@YH YH]# fg
    vim readAgrs.sh
    [root@YH YH]# jobs
    [1]+  Stopped                 vim cron.txt
    
    ```

- fg:获取后台服务

  - fg [%number]

    - number后台服务的任务号

    - 可以使用fg -来获取带有-的任务

    - ```bash
      [root@YH YH]# jobs
      [1]   Stopped                 vim cron.txt
      [2]-  Stopped                 vim fstab
      [3]   Stopped                 vim installPath.txt
      [4]+  Stopped                 vim diff_fstab
      [root@YH YH]# fg -
      这样就直接获取任务2了
      ```

    - 什么都不加获取带有+的任务

- bg:让后台任务运行

  - bg %number



### 脱机管理

由于任务管理在用户退出Linux后任务也会自动终端所以可以使用nohup来处理这个命令.(也可以使用at 命令)

但是nohup不支持bash的内置命令

nohup这本书介绍的不多自己查吧



#### 关于CPU的调度顺序

Priority:PRI,值越低优先级越高.用户无法调整

Nice:NI PRI(new) = PRI(old)+NI,虽然我们可以手动调整NI但并不一定会按照理想的调整

例如:PRI = 50 我们调整NI为5 PRI并不一定等于55因为是系统自动调整的

NI的取值范围(-20~19)



- nice 执行新命令并给予一个nice
  - -n 数字
- renice 对一个已经执行的命令给予一个NI值



#### 查看系统资源

free查看内存使用

- -h最常用会显示单位
- -t输出结果显示物理内存与swap总量



```bash
[root@YH ~]# free -ht
              total        used        free      shared  buff/cache   available
Mem:           1.8G        226M        1.3G        640K        286M        1.4G
Swap:            0B          0B          0B
Total:         1.8G        226M        1.3G
```

有时候我们会遇到woc,buff/cache为啥把所有的内存都站了....没事因为正是因为你系统没啥东西可能导致的,再就是经常读写文件修改配置等导致的,如果你要用系统会自动调控的.

看到available了嘛那个才是真正能用的内存数量



#### uname查看内核信息

-a查看所有

#### uptime查看系统启动时间与任务负载

#### netstat网络追踪与socket文件

- -a 所有
- -u udp的
- -t tcp的
- -n 以端口列出服务名
- -l 列出正在监听的服务
- -p 列出网络进程的PID

显示数据分为两部分

```bash
[root@YH ~]# netstat -a
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:http            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN     
tcp        0      0 VM-0-5-centos:smtp      0.0.0.0:*               LISTEN     
tcp        0      0 YH:58988                169.254.0.138:d-s-n     ESTABLISHED
tcp        0      0 YH:ssh                  153.118.35.43:22177     ESTABLISHED
tcp        0      0 YH:ssh                  153.118.35.43:21795     ESTABLISHED
tcp        0      0 YH:55572                169.254.0.55:webcache   TIME_WAIT  
tcp        0     52 YH:ssh                  153.118.35.43:21834     ESTABLISHED
tcp        0      0 YH:ssh                  153.118.35.43:21817     ESTABLISHED
tcp        0      0 YH:42208                169.254.0.55:lsi-bobcat ESTABLISHED
tcp        0      0 YH:ssh                  153.118.35.43:21899     ESTABLISHED
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN     
tcp6       0      0 VM-0-5-centos:smtp      [::]:*                  LISTEN     
udp        0      0 0.0.0.0:bootpc          0.0.0.0:*                          
udp        0      0 YH:ntp                  0.0.0.0:*                          
udp        0      0 VM-0-5-centos:ntp       0.0.0.0:*                          
udp6       0      0 VM-0-5-centos:ntp       [::]:*                             
udp6       0      0 YH:ntp                  [::]:*   
```

- proto:协议
- Recv-Q:非由用户进程连接的Bytes总数
- Send-Q:非由远程用户传送过来的Bytes总数
- Local: Address本地连接地址
- Foreign: Address远程连接地址
- State: 状态ESTABLISHED为建立LISTEN为监听



另一部分sockets文件

```bash
Active UNIX domain sockets (servers and established)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  2      [ ACC ]     STREAM     LISTENING     13829    /var/run/lsm/ipc/sim
unix  2      [ ACC ]     STREAM     LISTENING     17652    public/pickup
unix  2      [ ACC ]     STREAM     LISTENING     17656    public/cleanup
unix  2      [ ACC ]     STREAM     LISTENING     17659    public/qmgr
unix  2      [ ACC ]     STREAM     LISTENING     17663    private/tlsmgr
unix  2      [ ACC ]     STREAM     LISTENING     17666    private/rewrite
unix  2      [ ACC ]     STREAM     LISTENING     13003    @ISCSID_UIP_ABSTRACT_NAMESPACE
unix  2      [ ACC ]     STREAM     LISTENING     17669    private/bounce
```

- proto:一般为unix
- RefCnf:连接到此socket的进程数量
- Flags:连接的标识
- Type:socket取值类型,stream(需要确认)和dgram(不需要确认)
- state:若为connected代表多个进程之间已经建立连接
- path:连接到此socket的相关进程路径

> 列出所有开放的端口命令
>
> netstat -tulnp



#### dmesg分析内核产生信息

建议管道过滤以下

#### vmstat检测系统资源变化

```bash
[root@YH ~]# vmstat -a
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free  inact active   si   so    bi    bo   in   cs us sy id wa st
 4  0      0 1341564  85916 237060    0    0     1    32   21   32  1  1 99  0  0
 --进程字段
# r等待运行的进程数量
# b不可被幻型的进程数量
--内存字段
# swpd虚拟内存被使用的容量
# free未被使用的内存容量
# buff用于缓冲存储器
# cache用于高速缓存
--交换内存分区
# si由硬盘中将进程取出的容量
# so由于内存不足而将没用到的进程写入到磁盘的swap容量
--硬盘读写
#bi:磁盘读入的块数
#bo:写到磁盘的快数
--系统
#in:每秒被中断的进程次数
#cs:每秒执行的次数
--CPU
#us:非内核的CPU状态
#sy:内核层CPU状态
#id:闲置状态
#wa:等待I/O所耗费的CPU
#st:被虚拟机所使用的CPU
```

参数自己查把



#### /proc/*

我们提到的所谓的进程都在内存中,而内存又写到了/proc/*这个文件中PID为1就是/proc/1

基本都有很多关于进程的文件

```bash
[root@YH 1]# cat cmdline 
/usr/lib/systemd/systemd--switched-root--system--deserialize22
[root@YH 1]# cat environ 
```

cmdline命令串

environ环境变量内容

具体每个文件啥样看文档吧https://blog.csdn.net/xiaodingqq/article/details/79943648

#### fuser通过文件找出正在使用该文件的进程

如果你要卸载某个分区但发现该分区为忙碌的可以使用该命令

- -u:列出使用者
- -m:对umount不成功有效,后面接文件名会主动地提到该文件系统的最顶层
- -v:可以列出每个文件与进程还有命令的相关性
- -k:找出使用该文件/目录的PID,并试图以SIGKILL这个信号给予PID
- -i:必须与-k配合
- -signal: 例如-1 ,-15如果不给出则默认-9



fuser -uv .列出当前目录使用的PID/所属账号/权限

```bash
[root@YH YH]# fuser -uv .
                     USER        PID ACCESS COMMAND
/usr/local/YH:       root     kernel mount (root)/usr/local/YH
                     root        972 ..c.. (root)bash
#关于access的值c代表当前目录下,e代表可被触发的执行状态,f是一个被开启的文件,r代表顶层目录,F代表文件被使用,不过在等待响应中,m代表可能是共享的动态函数库
```



模拟我要卸载/home但是被占用了

```bash
[root@YH home]# fuser -uv .
                     USER        PID ACCESS COMMAND
/home:               root        972 ..c.. (root)bash
                     stranger   2333 ..c.. (stranger)bash
```

通过fuser -mki /home来进行移除



#### lsof列出被进程所使用的文件名

和fuser相反,fuser是通过文件来找出进程,而lsof是通过进程找出文件

-a 更改属性

-u 所属使用者



#### pidof找出某个正在执行的进程的pid



### SELinux

避免资源被误用,传统的文件权限与账号的关系以策略规则指定特定进程读取特定文件

以前没使用SELinux的DAC,权限只有普通的rwx,这样很难针对性进行权限设置,例如我们有个www服务器里面有一个httpd假设被别人攻击并获取后它可以修改其他777权限的目录

但是使用了MAC后对httpd这个进程进行设置,为只在/var/www/html这个文件夹下有权限其他文件夹去都去不了就可以了



SELinux的相关的东西

- 主体:SELinux主要管理的就是进程,所以进程与主体基本一样
- 目标:主体能否进行读写的目标资源,可理解为文件系统
- 策略:根据策略来指定
  - targeted:针对网络服务较多,默认策略
  - minimum:由target自定义而来针对选择的进程来保护
  - mls:完整的SELinux限制
  - 安全上下文:==放在inode里面==

主体与目标的安全上下文必须一致才能顺利读写

查看目录下的安全上下文ls -Z

用两个冒号划分为三段

Identify:role:type

- Identify:账号的身份识别
  - unconfined_u:不受限制的用户,不受SELinux管制(一般由用户产生)
  - system_u:系统用户(一般由系统产生)
- Role:角色
  - object_r:代表文件或目录
  - system_r:代表的是进程
- Type(最重要):类型一般一个主体能不能读取文件或资源与这个字段有关
  - type:文件资源(Object)上面称为类型(Type)
  - domain:在主体进程(Subject)则称为域(Domain)



关于SELinux的三种模式

- Enforcing :强制模式,任何操作都需要进行检测如果不满足策略则直接拦截
- Permissive:兼容模式,任何操作都进行检测但是不满足不会拦截而是记录在log内
- Disable   :关闭模式



getenforce:列出目前模式

sestatus:列出目前的SELinux使用的哪个策略

```bash
[root@VM-0-5-centos bin]# ls -al | grep 'bash'
-rwxr-xr-x.  1 root root      964536 Apr  1  2020 bash
lrwxrwxrwx.  1 root root          10 Aug  7  2020 bashbug -> bashbug-64
-rwxr-xr-x.  1 root root        6964 Apr  1  2020 bashbug-64
lrwxrwxrwx.  1 root root           4 Aug  7  2020 sh -> bash
[root@VM-0-5-centos bin]# sestatus 
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   permissive
Mode from config file:          permissive
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      31
```



getsebool查看各个规则的bool值

chcon [-R] [-t type] [-u user] [-r role] 文件 修改SELinux类型

- -R :连同子目录也修改
- -t :后面接安全上下文的类型栏

用chcon修改文件我指定的类型

或使用chcon --reference=范例文件 文件  将文件设为范例文件的类型



restorecon -Rv 文件名

让文件恢复正确的SELinux



```bash
[root@YH YH]# ls -Z
drwxr-xr-x. root root unconfined_u:object_r:usr_t:s0   test1
[root@YH YH]# chcon -t aaa test1
[root@YH YH]# ls -Z
drwxr-xr-x. root root unconfined_u:object_r:aaa:s0     test1
[root@YH YH]# restorecon test1
[root@YH YH]# ls -Z
drwxr-xr-x. root root unconfined_u:object_r:usr_t:s0   test1
```



==/etc/selinux/config这个文件用来修改模式==

如果只是从宽容改成强制可以使用setenforce [0|1]

0为宽容

1为强制

为啥restorecon可以还原目录与文件的安全上下文.

semanage默认目录的修改与查看



#### 关于日志协助

日志信息放在/var/log/message与/var/log/setroubleshoot,但是需要安装setroubleshoot与setroubleshoot-server

安装完成后需要重启auditd



==这个SELinux讲的真cha！！！==



> ==如果由于SELinux配置出问题可以使用restorecon -Rv /来还原==



## 服务

一般服务的后缀名都带有一个d(daemon)字代表后台执行例如atd，crond

顺带提一嘴se开头的与SELinux有关



==有时服务是有依赖的所以关闭一个可能没用==



现在的systemd启动方式与传统unix的init启动方式的优点

- 并行处理加快了启动速度
  - 由于init是一个一个的启动服务因为要依赖很多服务,由于现在由于主机和操作系统支持多内核所以可以同时启动
- 一命令管理
  - systemd的全部就是仅有一个systemctl来处理的,不像init启动不同的需要不同的命令
- 服务依赖的自动检查
  - 如果你要启动一个服务需要另一个依赖,system会自动帮你检查然后帮你启动
- 更多的功能分类便于管理
  - 现在系统的system功能将服务统一为一个unit单元然后进行分类为比以前更合理的分类便于记忆
- 打包运行
  - 根据以前的init的5级别的运行特色,systemd将很多功能集合成一个所谓的target项目.亦或是执行了某个target就执行了很多个daemon项目
- 兼容init,但是也有一些不支持
  - 只有1,3,5级别
  - 全部都是systemctl进行管理,而systemctl语法有限制并且没法自定义
  - 如果手动启动某个服务systemd就无法检测到该服务(例如用crond启动啥啥啥)
  - systemd启动过程中无法交互,所以如果自己写systemd的启动程序时候要避免交互式



### systemd的配置文件与目录

/usr/lib/systemd/system/:每个服务最主要的管理系统,官方提供的软件安装后启动程序都放在这个目录下面

/run/systemd/system/:系统执行过程中所产生的服务脚本,比上面的优先级高

/etc/systemd/system/:管理员根据系统要求建立的脚本,比上面那两条的优先级高

/etc/sysconfig/*:几乎所有服务都会将初始化的一些选项设置写入到这个目录

/var/lib:产生数据的服务写入到这个目录

/run/:放置daemon的缓存

/etc/services定义服务与端口的映射



修改启动设置要去/usr/lib/systemd/system下面

要管理哪些启动哪些不启动要去/etc/systemd/system下面



#### 主要常用服务以及后缀名

| 后缀名             | 主要服务功能                                                 |
| ------------------ | ------------------------------------------------------------ |
| .service           | 一般服务类型,最常见的                                        |
| .socket            | 多半用于数据交互,本地用的多                                  |
| .target            | 一群unit的集合,一般执行一个.target就相当于执行了一堆.service和.socket |
| .mount和.automount | 文件系统相关的挂载的服务,用于管理系统的各个挂载              |
| .path              | 用来检测目录然后提供队列服务                                 |
| .timer             | systemd主动执行的循环服务                                    |







### 使用systemctl来管理服务

**systemctl**

命令部分:

- systemctl [command] [unit]

  - 单一服务控制

    - start :立即启动后面接的unit
    - stop :立即停止后面接的unit,但可能因为其他服务导致重新激活
    - restart :立即重启后接unit
    - reload :不关闭服务的情况下重启加载配置文件
    - enable :设置下次开机自启动后接unit
    - disable :设置下次开机后不启动unit
    - is-active:是否启动
    - is-enable:是否自启动
    - status:查看unit状态
    - mask:注销服务不会被激活
    - unmask:取消注销

  - 查看所有服务

    - list-units:依据目前启动的unit,加上--all显示没有启动的
    - list-unit-files:一局/usr/lib/systemd/system内的文件,将所有文件列表说明
    - --type=TYPE 就是上面那个表里面那个[类型](####主要常用服务以及后缀名)

    - list-sockets

  - 常见的target(下面的都在.target就不写了)

    - graphical:命令加上图形界面
    - multi-user:纯命令
    - rescue:无法使用root登录的情况下滋生的额外系统
    - emergency:紧急处理系统的错误
    - shutdown:关机
    - getty:设置你需要的tty

  - 切换不同的操作系统模式

    - get-default:取得目前的模式
    - set-default:设置默认的启动模式
    - isolate:切换到后面接的模式
    - poweroff:关机
    - reboot:重启
    - suspend:挂起(数据保存到内存中,关闭大部分的硬件设备)
    - hibernate:休眠(数据保存到硬盘然后关机)
    - rescue:强制进入恢复模式
    - emergency:强制进入紧急恢复模式

  - 通过systemctl分析各个服务之间的依赖

    - list-dependencies [unit] [--reverse]
      - --reverse:反向追踪

==在.service才使用start、stop、restart等命令，但是在.target时候用isolate==



内容解析

```bash
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: active (running) since Sun 2021-09-26 09:39:13 CST; 17min ago
     Docs: man:firewalld(1)
 Main PID: 28544 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─28544 /usr/bin/python2 -Es /usr/sbin/firewalld --nofork --nopid

Sep 26 09:39:13 YH systemd[1]: Starting firewalld - dynamic firewall daemon...
Sep 26 09:39:13 YH systemd[1]: Started firewalld - dynamic firewall daemon.
Sep 26 09:39:14 YH firewalld[28544]: WARNING: AllowZoneDrifting is enabled. This is considered an insecure configuration option... it now.
Hint: Some lines were ellipsized, use -l to show in full.
```

- Loaded:说明不自启动

- Active:正在执行(running)
  - exited:执行一次就结束
  - waiting需要等待
  - inactive:没有运行的意思

后面是PID

下面是日志 时间 发送给哪一台主机 哪一个服务信息 内容是啥



### systemctl针对service类型的配置文件

以sshd为例子

```bash
[root@YH ~]# cat /usr/lib/systemd/system/sshd.service 
[Unit]
Description=OpenSSH server daemon
Documentation=man:sshd(8) man:sshd_config(5)
After=network.target sshd-keygen.service
Wants=sshd-keygen.service

[Service]
Type=notify
EnvironmentFile=/etc/sysconfig/sshd
ExecStart=/usr/sbin/sshd -D $OPTIONS
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
```

unit:对本身的说明,以及与其他依赖的设置,什么时候启动

Service、Socket、Timer、Mount、Path:不同的unit类型对应着不同设置项目.所以这里是Service 这个项目主要用来规范服务启动的脚本、环境配置文件名、重启的方式

Install:这个项目就是将此unit安装到哪个target里面



其他:

> 项目内的设置通常是可以重复的,例如在[Unit]里面设置了两个After这样后面哪个After会覆盖前面那个
> 如果项目需要设置[是/否]的布尔值项目可以使用1,yes,true,on代表开启 0,no,false,off代表关闭 
> 空白行,#开头或;那一行都代表注释



表格说明:

Unit部分

| 参数          | 含义                                                  |
| ------------- | ----------------------------------------------------- |
| Description   | systemctl list-units时进行输出的介绍                  |
| Documentation | 手册                                                  |
| After         | 强调在那个项目之后执行,不是强制性只是用来说明启动顺序 |
| Before        | 与After相反                                           |
| Requires      | 是在什么服务启动之前最好启动这个服务                  |
| Wants         | 与Requires相反,在什么程序启动之后才能够启动这个       |
| Conflicts     | 代表冲突的服务.就是后接的项目如果启动则不启动         |

Service

| 参数            | 含义                                                         |
| --------------- | ------------------------------------------------------------ |
| Type            | simple默认值,这个daemon主要由ExecStart,接的命令来启动,启动后常驻于内存<br />forking:由ExecStart启动的程序通过spawns扩展出来的程序作为该程序的主要服务,在原生父进程启动结束后终止运行<br />oneshot:与simple类似,不过这个进程在工作完毕就关闭不驻于内存<br />dbus:与simple类似,但这个daemon必须要在获取一个D-Bus的名称后,才会继续运行通常要设置BusName=才行<br />idle:与simple类似,意思是要执行这个daemon必须要所有的工作都顺利执行完毕后才会执行通常是开机最后任务 |
| EnvironmentFile | 可以指定启动脚本的环境配置文件                               |
| ExecStart       | 实际执行daemon的命令或者脚本                                 |
| ExecStop        | 关闭服务的命令                                               |
| ExecReload      | 与重新加载配置文件有关                                       |
| Restart         | 当为1时关闭该服务会立即再次开启                              |
| RemainAfterExit | 为1时,这个daemon所属的进程都终止之后会再次尝试启动           |
| TimeoutSec      | 这个服务在启动或者关闭时,因为某些命令无法顺利运行的我们要等多久才能进入强制结束状态 |
| KillMode        | process:daemon终止时只终止主要进程ExecStart后接的命令串<br />control-group:daemon终止时此daemon所产生的其他control-group进程都会关闭<br />none:daemon终止时没有进程被关闭 |
| RestartSec      | 服务被关闭需要多少秒才能重新启动                             |



Install

| 参数     | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| WabtedBy | 大部分时.target的含义是依附于那个target                      |
| Also     | 当前这个unit本身被enable,Also后接的unit也请enable的意思(一般是依赖服务) |
| Alise    | 运行一个连接的别名                                           |



#### 关于Systemctl针对timer的配置文件

systemd.timer的优势

- 由于所有的systemd的服务所产生的信息都会被记录（log），因此比crond在debug上面要清楚。
- 各项timer的任务可以跟systemd的服务相结合
- 各项timer的任务可以跟control group结合



想要使用timer的必要条件：

1. 操作系统的timer.target一定要启动
2. 要有个*.service的服务存在
3. 要有个*.timer的时间启动服务存在



在/etc/systemd/system下面建立一个*.timer文件要有下面这些内容

| 参数              | 含义                                                         |
| ----------------- | ------------------------------------------------------------ |
| OnActiveSec       | 当timers.target启动后多久才启动这个unit                      |
| OnBootSec         | 当启动完成后多久才执行                                       |
| OnStartupSec      | 当systemd第一次启动之后多久才执行                            |
| OnUnitActionSec   | 这个timer配置文件所管理的那个unit服务在最后一次启动隔多久在启动 |
| OnUnitInactiveSec | 这个timer配置文件所管理的unit服务最后一次停止后隔多久在执行一次 |
| OnCalendar        | 使用实际时间的方式来启动服务                                 |
| Unit              | 在.timer中要制定哪一个server unit                            |
| Persistent        | 当使用OnCalender的设置时,指定该功能要不要持续进行的意思      |

关于OnCalender的时间如果想要从crontab转换成这个timer功能的话,需要了解时间设置格式

语法:英文周名 YYYY-MM-DD HH:MM:SS

例子 Thu     2015-08-13 13:13:13

也可以使用时间间隔为分割 (时间)(单位),或者使用英文单词

例如

首先编写如下文件

```bash
[root@YH system]# cat YH_backup.timer 
[Timer]
OnBootSec=2hrs
OnUnitActiveSec=2days
[Install]
WantedBy=multi-user.target
```

然后进行运行

```bash
[root@YH system]# systemctl enable YH_backup.timer
[root@YH system]# systemctl restart YH_backup.timer
```



## 日志

==一般日志文件只有root能看==

- /var/log
  - /boot.log:开机启动的时候系统内核会去检测与启动硬件
  - /cron:记录crontab执行知否出错
  - /dmesg:记录系统在开机的时候内核检测过程所产生的各项信息.
  - /lastlog:最近一次登录的信息
  - /maillog或/mail/*:记录邮件来往信息
  - ==/messages:几乎系统出错的信息都在上面==
  - /secure:涉及到输入用户名密码的都在上面
  - /wtmp:正确登录的账户信息
  - /faillog:错误登录的账户信息
  - /httpd/*:记录网络服务日志
  - /samba/*:关于samba服务器的

如果任由日志记录下去则会很大所以可以用logrotate来记录

针对日志常需要的服务与程序有

==rsyslogd的日志文件只要进行编辑过就不能继续记录,所有用vim查看的时候不要wq退出,重启rsyslog.service可以让他继续提供服务==

- systemd-journald.service:最主要的信息记录者,内容在内存中可以使用journalctl来查看

  - 通过journalctl来查看
  - -n:显示几行
  - -r:反向输出
  - -p:后面所接重要性排序
  - -f:tail -f的功能持续显示journal的内容
  - --since --until 设置开始与结束时间
  - _COMM=bash 只输出bash
  - _PID=pid 只输出该pid的信息
  - _UID=uid 只输出该uid的信息

- rsyslog.service:主要收集登录系统与网络服务的信息

  - 该服务的配置文件/etc/rsyslog.conf,为了将不同的信息放到不同的文件夹中

  - ```bash
    [root@YH ~]# cat /etc/rsyslog.conf | grep -v "^$" | grep -v "^#"
    $ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
    $ModLoad imjournal # provides access to the systemd journal
    $WorkDirectory /var/lib/rsyslog
    $ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
    $IncludeConfig /etc/rsyslog.d/*.conf
    $OmitLocalLogging on
    $IMJournalStateFile imjournal.state
    *.info;mail.none;authpriv.none;cron.none                /var/log/messages
    authpriv.*                                              /var/log/secure
    mail.*                                                  -/var/log/maillog
    cron.*                                                  /var/log/cron
    *.emerg                                                 :omusrmsg:*
    uucp,news.crit                                          /var/log/spooler
    local7.*                                                /var/log/boot.log
    #格式一般为:
    #服务名称 服务等级                                       信息记录的文件名与主机(可以是远程主机用@,用户,文件路径,打印设备,当前在线的所有人*)
    ```

  - 第九行代表mail,authpriv,cron产生的信息较多所以就不记录到/var/log/messages里面了

  - 第十到十二行进行在对其进行了分类记录

  - 第十三行如果发生了严重的错误以广播的方式给所有系统账号

  - 十四行,早期unix的数据传递组

  - 十五行,启动信息

  - 第十一行中文件名前面的-代表先放入缓存等数据量大的时候再写入磁盘

  - > 服务名称可以使用man 3 syslog查到(可是查到的与这里面的不大一样)    
    >
    > 默认格式为服务名称.服务等级(包含该等级以及更严重的等级都会按照这行的格式记录下来)但是还有别的关系式
    >
    > .=代表所需要的等级只有后面接的等级
    >
    > .!代表不等于,除了该等级以外的其他等级都记录

- logrotate:主要进行日志文件轮询功能

日志文件的一般格式:

- 事件发生的日期与事件
- 发生此事件的主机名
- 启动此事件的服务名称
- 内容

例如

```bash
[root@YH ~]# journalctl 
-- Logs begin at Fri 2021-09-24 16:26:44 CST, end at Mon 2021-09-27 10:49:08 CST. --
Sep 24 16:26:44 VM-0-5-centos systemd[1]: Reloading.
Sep 24 16:26:44 VM-0-5-centos rsyslogd[1232]: imjournal: journal reloaded... [v8.24.0-57.el7_9 try http://www.rsyslog.com/e/0 ]
Sep 24 16:26:48 VM-0-5-centos dracut[1665]: *** Including module: qemu ***
Sep 24 16:26:48 VM-0-5-centos dracut[1665]: *** Including module: fstab-sys ***
Sep 24 16:26:48 VM-0-5-centos dracut[1665]: *** Including module: iscsi ***
Sep 24 16:26:49 VM-0-5-centos dracut[1665]: *** Including module: rootfs-block ***
Sep 24 16:26:49 VM-0-5-centos dracut[1665]: *** Including module: terminfo ***
Sep 24 16:26:49 VM-0-5-centos dracut[1665]: *** Including module: udev-rules ***
Sep 24 16:26:49 VM-0-5-centos dracut[1665]: Skipping udev rule: 40-redhat-cpu-hotplug.rules
Sep 24 16:26:49 VM-0-5-centos dracut[1665]: Skipping udev rule: 91-permissions.rules
Sep 24 16:26:49 VM-0-5-centos dracut[1665]: *** Including module: pollcdrom ***
Sep 24 16:26:49 VM-0-5-centos dracut[1665]: *** Including module: systemd ***
Sep 24 16:26:50 VM-0-5-centos dracut[1665]: *** Including module: usrmount ***
Sep 24 16:26:50 VM-0-5-centos dracut[1665]: *** Including module: base ***
Sep 24 16:26:50 VM-0-5-centos dracut[1665]: *** Including module: fs-lib ***
Sep 24 16:26:50 VM-0-5-centos dracut[1665]: *** Including module: kdumpbase ***
Sep 24 16:26:50 VM-0-5-centos dracut[1665]: *** Including module: shutdown ***

```



### logrotate

#### 配置文件

/etc/logrotate.conf:主要的参数文件,默认配置文件

/etc/logrotate.d:文件目录

logrotate会对每个日志文件进行记录备份,如果进行了三次备份就开始删除

```bash
[root@YH ~]# cat /etc/logrotate.conf | grep  "^[^#]" 
weekly    #默认一周轮询一次
rotate 4  #保留几个日志文件
create    #由于日志文件被改名因此建立一个新的来存储是意思
dateext   #轮询的文件名称会加上日期
include /etc/logrotate.d #这个目录的所有文件都来执行轮询任务
/var/log/wtmp { #针对这个目录所设置的参数
    monthly
    create 0664 root utmp
	minsize 1M
    rotate 1
}
/var/log/btmp {
    missingok
    monthly
    create 0600 root utmp
    rotate 1
}
```

对于logrotate.d目录下的文件来说

```bash
[root@YH logrotate.d]# cat syslog 
/var/log/cron
/var/log/maillog
/var/log/messages
/var/log/secure
/var/log/spooler
{
	sharedscripts
	dateext
	rotate 25
	size 40M
	compress
	dateformat  -%Y%m%d%s
	postrotate
		/bin/kill -HUP `cat /var/run/syslogd.pid 2> /dev/null` 2> /dev/null || true
	endscript
}

#该文件结构为
#文件名
#{
#  sharedscriptes
# 执行的脚本prerotate在logrotate执行之前执行,postrotate在昨晚logrotate之后启动命令
#  endscript
#}
```

#### 例子

如果要对/var/log/admin.log添加a+属性并且

日志一个月轮询一次

如果大于10M立即轮询

保存5个文件

备份压缩

```bash
[root@YH logrotate.d]# cat admin 
/var/log/admin.log{
	monthly
	size=10M
	rotate 5
	compress
	sharedscripts
	prerotate
		/usr/bin/chattr chattr -a /var/log/admin.log
	endscript
	sharedscripts
	postrotate
		/usr/bin/chattr chattr +a /var/log/admin.log
	endscript
}

```



## 软件安装

在软件源代码中makefile用来管理一系列哪些文件编译先编译哪些后编译

make这个命令就需要makefile

操作系统中最好安装make与autoconfig一个是用来编译的，另一个是用来检测是否有makefile文件



### 常用安装软件

RPM:Centos常用(软件管理):软件安装的环境必须与打包的环境需求一致或相当,满足软件依赖,反安装时需要特别小心最底层的软件不要先删除否则可能系统出现问题,不能安装其他Linux发行版本/var/lib/rpm

> 为了解决不能安装其他的Linux发行版本出了个SRPM,里面包含源码,与Tarball不同的是这里面还包含所需要的依赖
>
> 先将软件以RPM的方式编译此时变成RPM文件
>
> 然后编译好的RPM安装到Linux中
>
> 文件名通常为.src.rpm

dpkg:Ubuntu常用(软件管理)

yum:Centos软件升级,更新/var/cache/yum

apt-get:Ubuntu软件升级

> https://www.cnblogs.com/gshelldon/p/13276253.html
>
> RPM包名解析



#### RPM安装

rpm -ivh XXXX.rpm

- --nodeps:软件因为依赖问题无法安装但是执意安装
- --replacefiles:已经安装在你的系统上面,又或者出现版本不合信息
- --replacepkgs:某个软件已经安装过了
- --force:上面两个的综合体
- --test:测试找到依赖
- --justdb:由于RPM数据库损坏或因为某些原因产生故障
- --nosignaturn:跳过数字签名
- --prefix 新路径:安装到非正规路径
- --noscripts:不想让该软件在安装过程中自行执行某些系统命令



#### RPM的升级与更新

-Uvh:没有安装就安装,如果是旧版本直接更新

-Fvh:没有安装不安装,只有已经安装的软件会升级



#### RPM查询

-qa:查询所有

-q:仅仅查询后接软件是否安装

-qi:列出软件的详细信息

-ql:列出文件与目录所在的文件名

-qc:列出配置文件

-qd:列出所有说明文档

-qR:列出软件的依赖

-qf:找出该文件属于哪个已经安装的软件

-q --scripts:列出是否包含需要安装后执行的脚本



#### RPM认证与数字签名

-V:后接软件名显示该软件被修改的内容

-Va:显示所有软件被修改的内容

-Vp:列出软件可能被修改的文件

-Vf:显示某个文件是否被修改



```bash
[root@YH ~]# rpm -Va
S.5....T.  c /etc/infiniband/openib.conf
.......T.  c /etc/modprobe.d/ib_ipoib.conf
.......T.    /etc/udev/rules.d/10-knem.rules
S.5....T.  c /etc/NetworkManager/NetworkManager.conf
missing     /var/run/abrt
.M.......  g /var/lock/iscsi
.M.......  g /var/lock/iscsi/lock
....L....  c /etc/pam.d/fingerprint-auth
....L....  c /etc/pam.d/password-auth
....L....  c /etc/pam.d/postlogin
....L....  c /etc/pam.d/smartcard-auth
....L....  c /etc/pam.d/system-auth
S.5....T.  c /etc/security/limits.conf
S.5....T.  c /etc/security/limits.d/20-nproc.conf
```

前面几个字段

S代表容量改变, M代表文件的属性或类型被改变, 5代表MD5这一种校验值的内容已经不同了, D代表主/次代码已经改变, L代表连接文件路径已经改变, U代表文件所属用户已经改变, G代表文件所属用户组已经改变, T代表文件的建立时间已经改变, P代表功能已经改变

后面的代表c:配置文件 ,d:数据文件 ,g:幽灵文件(通常该文件不被某个软件所包含) ,l:许可认证文件 ,r:自述文件



安装数字签名

> rpm --import 数字签名文件
>
> #查找数字前面方式
>
> locate GPG-KEY
>
> find '\*GPG-KEY\*'
>
> #首先使用这个查找数字签名的公钥
>
> rpm -qa | grep pubkey
>
> ```bash
> [root@YH ~]# rpm -qa | grep pubkey
> gpg-pubkey-352c64e5-52ae6884
> gpg-pubkey-f4a80eb5-53a7ff4b
> ```
>
> #在根据数字签名公钥名来查找
>
> ```bash
> [root@YH ~]# rpm -qi  gpg-pubkey-352c64e5-52ae6884
> Name        : gpg-pubkey
> Version     : 352c64e5
> Release     : 52ae6884
> Architecture: (none)
> Install Date: Tue 05 Nov 2019 10:55:36 PM CST
> Group       : Public Keys
> Size        : 0
> License     : pubkey
> Signature   : (none)
> Source RPM  : (none)
> Build Date  : Mon 16 Dec 2013 10:42:12 AM CST
> Build Host  : localhost
> Relocations : (not relocatable)
> Packager    : Fedora EPEL (7) <epel@fedoraproject.org>
> Summary     : gpg(Fedora EPEL (7) <epel@fedoraproject.org>)
> Description :
> -----BEGIN PGP PUBLIC KEY BLOCK-----
> Version: rpm-4.11.3 (NSS-3)
> 
> mQINBFKuaIQBEAC1UphXwMqCAarPUH/ZsOFslabeTVO2pDk5YnO96f+rgZB7xArB
> OSeQk7B90iqSJ85/c72OAn4OXYvT63gfCeXpJs5M7emXkPsNQWWSju99lW+AqSNm
> jYWhmRlLRGl0OO7gIwj776dIXvcMNFlzSPj00N2xAqjMbjlnV2n2abAE5gq6VpqP
> vFXVyfrVa/ualogDVmf6h2t4Rdpifq8qTHsHFU3xpCz+T6/dGWKGQ42ZQfTaLnDM
> jToAsmY0AyevkIbX6iZVtzGvanYpPcWW4X0RDPcpqfFNZk643xI4lsZ+Y2Er9Yu5
> S/8x0ly+tmmIokaE0wwbdUu740YTZjCesroYWiRg5zuQ2xfKxJoV5E+Eh+tYwGDJ
> n6HfWhRgnudRRwvuJ45ztYVtKulKw8QQpd2STWrcQQDJaRWmnMooX/PATTjCBExB
> 9dkz38Druvk7IkHMtsIqlkAOQMdsX1d3Tov6BE2XDjIG0zFxLduJGbVwc/6rIc95
> T055j36Ez0HrjxdpTGOOHxRqMK5m9flFbaxxtDnS7w77WqzW7HjFrD0VeTx2vnjj
> GqchHEQpfDpFOzb8LTFhgYidyRNUflQY35WLOzLNV+pV3eQ3Jg11UFwelSNLqfQf
> uFRGc+zcwkNjHh5yPvm9odR1BIfqJ6sKGPGbtPNXo7ERMRypWyRz0zi0twARAQAB
> tChGZWRvcmEgRVBFTCAoNykgPGVwZWxAZmVkb3JhcHJvamVjdC5vcmc+iQI4BBMB
> AgAiBQJSrmiEAhsPBgsJCAcDAgYVCAIJCgsEFgIDAQIeAQIXgAAKCRBqL66iNSxk
> 5cfGD/4spqpsTjtDM7qpytKLHKruZtvuWiqt5RfvT9ww9GUUFMZ4ZZGX4nUXg49q
> ixDLayWR8ddG/s5kyOi3C0uX/6inzaYyRg+Bh70brqKUK14F1BrrPi29eaKfG+Gu
> MFtXdBG2a7OtPmw3yuKmq9Epv6B0mP6E5KSdvSRSqJWtGcA6wRS/wDzXJENHp5re
> 9Ism3CYydpy0GLRA5wo4fPB5uLdUhLEUDvh2KK//fMjja3o0L+SNz8N0aDZyn5Ax
> CU9RB3EHcTecFgoy5umRj99BZrebR1NO+4gBrivIfdvD4fJNfNBHXwhSH9ACGCNv
> HnXVjHQF9iHWApKkRIeh8Fr2n5dtfJEF7SEX8GbX7FbsWo29kXMrVgNqHNyDnfAB
> VoPubgQdtJZJkVZAkaHrMu8AytwT62Q4eNqmJI1aWbZQNI5jWYqc6RKuCK6/F99q
> thFT9gJO17+yRuL6Uv2/vgzVR1RGdwVLKwlUjGPAjYflpCQwWMAASxiv9uPyYPHc
> ErSrbRG0wjIfAR3vus1OSOx3xZHZpXFfmQTsDP7zVROLzV98R3JwFAxJ4/xqeON4
> vCPFU6OsT3lWQ8w7il5ohY95wmujfr6lk89kEzJdOTzcn7DBbUru33CQMGKZ3Evt
> RjsC7FDbL017qxS+ZVA/HGkyfiu4cpgV8VUnbql5eAZ+1Ll6Dw==
> =hdPa
> -----END PGP PUBLIC KEY BLOCK-----
> ```

反安装一定要==从上层往下层==卸载,如果rpm数据库损坏可以使用rpm --rebuilddb来重建



#### YUM

- --installroot=/some/path:将软件安装在/some/path下面

- -y:自动输入yes

- search:查找软件名或者是描述的重要关键字

- list:列出目前安装的所有

- info:同上(数据更详细)

  - ```bash
    [root@YH ~]# yum info samba
    Loaded plugins: fastestmirror, langpacks
    Loading mirror speeds from cached hostfile
    Installed Packages
    Name        : samba            #软件名
    Arch        : x86_64           #CPU硬件架构名
    Version     : 4.10.16          #软件版本
    Release     : 15.el7_9         #发布版本
    Size        : 2.2 M
    Repo        : installed        #安装状态
    From repo   : updates
    Summary     : Server and Client software to interoperate with Windows machines
    URL         : http://www.samba.org/
    License     : GPLv3+ and LGPLv3+
    Description : Samba is the standard Windows interoperability suite of programs for Linux and
                : Unix.
    
    ```

- install

- update

- remove

- repolist all:列出所有库源

- grouplist:列出所有可使用的软件群组

- groupinfo:后接groupname可以了解group内含的所有软件名

- groupinstall:安装一组

- groupremove:删除某个软件群组

yum组群配置文件/etc/yum.conf有些组群中的软件因为是可选安装所以默认不安装,需要修改这个文件将可选安装改成默认安装



yum配置文件位置 /etc/yum.repos.d/CentO S-Base.repo

内容

```bash
[extras]
gpgcheck=1
gpgkey=http://mirrors.tencentyun.com/centos/RPM-GPG-KEY-CentOS-7
enabled=1
baseurl=http://mirrors.tencentyun.com/centos/$releasever/extras/$basearch/
name=Qcloud centos extras - $basearch
[os]
gpgcheck=1
gpgkey=http://mirrors.tencentyun.com/centos/RPM-GPG-KEY-CentOS-7
enabled=1
baseurl=http://mirrors.tencentyun.com/centos/$releasever/os/$basearch/
name=Qcloud centos os - $basearch
[updates]    #软件源名称不能重复不重要
gpgcheck=1   #检查数字签名
gpgkey=http://mirrors.tencentyun.com/centos/RPM-GPG-KEY-CentOS-7 #数字签名公钥位置
enabled=1   #是否启用
baseurl=http://mirrors.tencentyun.com/centos/$releasever/updates/$basearch/ #软件源实际位置
name=Qcloud centos updates - $basearch  #说明一下名称
~                                        
```

==如果你修改了软件源但是无用需要清空一些缓存yum clean [all|package|headers]==因为服务器会将软件信息先缓存放到/var/cache/yum里面



#### 扩展软件源地址

http://dl.fedoraproject.org/pub/epel/7/x86_64/

还有ELRepo软件源



### Tarball

由于是使用c语言写的所以大部分平台都可以使用

在Centos或Red Hat中要安装一些tarball软件要选择Development Tools以及Kernel Source Development的相关包

如果安装gcc等开发工具使用 yum groupinstall “Development Tools”

如果安装图形用户界面支持一般还要安装，yum groupinstall “X Software Development”

安装的软件较为旧则可能需要yum groupinstall “Legacy Software Development”



#### 安装步骤

1. 将tarball文件在/usr/local/src下面解压
2. 查看解压下面的README/INSTALL并安装依赖(必要)
3. 建立makefile:以自动检测程序测试操作系统环境(configure或config)
4. 用make并用目录下的makefile作为参数
5. 根据install这个目标的指定来安装到正确的路径



安装时候运行的命令

./configure:建立Makefile文件

make clean (读取makefile中的clean工作,虽然不一定有效但是可以清除目标文件)

make编译

make install

> 如果安装一个独立的目录,例如/usr/local/package这样需要将这个软件的man page手动写入/etc/man_db.conf中

软件建议安装在/usr/local,而源代码安装在/usr/local/src



### 使用RPM还是Tarball

- 优先使用原厂的RPM功能
- 使用软件官网发布的RPM或是提供的软件源地址
- 使用Tarball安装特殊软件
- 用Tarball测试软件,(如果已经有一个旧版本了但是防止新版本会导致什么问题所以可以使用Tarball来安装防止影响到原来的软件)



#### 利用默认值安装SRPM文件(--rebuid/--recomplie)

rpmbuild --rebuid 后接*.src.rpm文件(仅仅编译与打包)

运行后生成一个*.rpm

rpmbuild --recomplie 后接srpm文件(编译打包安装)

rpmbuild -ba 软件:打包生成rpm与srpm

rpmbuild -bb 软件:打包成rpm





#### Linux发行默认用到的路径

以apache为例子:

/etc/httpd	//配置文件

/usr/lib	//函数库	

/usr/bin	//执行文件

/usr/share/man	//联机文档



如果默认放在/usr/local里面就会可能变成(实际根据makefile)

/usr/local/etc/

/usr/local/lib/

/usr/local/bin/

/usr/local/man/



因为Tarball反安装比较难所以建议

- 将Tarball数据解压到/usr/local/src中
- 安装时最好考虑安装到/usr/local这个默认目录
- 考虑未来的反安装做好可以将每个软件单独安装在/usr/local下面
- 为安装到单独目录的软件man Page加入man path查找(加入安装到/usr/local/software,那么在/etc/man_db.conf中大约40~50行写入MANPATH_MAP /usr/local/software)



升级使用.patch文件来进行更新



### 动态与静态函数库

#### 静态

- 扩展名一般为libXXX.a
- 编译操作:文件会较大一些
- 独立执行的状态
- 如果想升级需要重新编译



#### 动态

- 扩展名一般为libXXX.so
- 编译操作:由于只有一个指针被整合到执行文件中所以文件会比较小
- 独立执行的状态:不能独立执行,程序需要去读取库函数,库函数的目录也不能改变
- 如果要升级直接更换指向就可以了

大部分函数库都是动态的

函数库一般放在/lib与/lib64里面,内核的函数库放在/lib/modules里面



#### 提高函数库的速度

- 将函数库加载到告诉缓存中,必须将函数库写入/etc/ld.so.conf(这里面只能写入文件不能写入目录)
- 利用idconfig来加载这个文件
  - -f conf:使用conf来代替/etc/ld.so.conf
  - -C cache:指定某个文件为高速缓存
  - -p:列出目前所有的函数库内容
- 同时将记录保存到/etc/ld.so.cache



- ldd
  - -v:列出所有内容信息
  - -d:重新将数据有遗失的连接点显示出来
  - -r:将ELF有关的错误内容显示出来





## 最后的几个命令

- nmcli:查看网络
- timedatectl 时间查看 [set-time 时间]进行时间设置
- ntpdate:时间矫正
- localectl :查看语言 [set-locale LANG=语言]设置语言
- dmidecode:查看硬件设备
  - 常用检测硬件信息命令
  - gdisk:分区
  - dmesg:内核运行的各项信息
  - vmstat:可分析系统目前的状态
  - lspci:列出整个PC的PCI接口设备
  - lsusb:列出USB信息
  - iostat:列出整个CPU与连接的输入输出设备
- smartd提供了smartctl命令来检测硬盘是好是坏

查看cpuhttps://www.cnblogs.com/hapday/p/6336905.html



### 备份

推荐的备份目录

- /etc
- /home
- /root
- /var/spool/mail,/var/spool/cron/,/var/spool/at
- /var/lib

不推荐备份的目录

- /dev
- /proc,/sys,/run
- /mnt,/media
- /tmp

常用备份工具dd、cpio、xfsdump、xfsrestore

https://www.cnblogs.com/wanao/p/12838917.html

## 内核源代码目录

https://blog.csdn.net/zn2857/article/details/78804818



### Nginx服务的安装与配置

https://www.runoob.com/linux/nginx-install-setup.html

如果访问不到请开放端口或关闭[防火墙](#防火墙)

## 云服务器安装samba

腾讯云为了安全禁用了samba的两个端口地址需要改映射

https://blog.csdn.net/zsdt345a780rfajwet/article/details/107845859



## 日志文件服务器

配置很简单就不做了



### 创建自己的服务

只需要创建一个.sh的脚本文件在任何文件夹

然后写一个/etc/systemd/system/*.service的配置文件与刚刚的脚本文件建立关联就可以使用systemctl进行管理了



## 安装jdk

https://blog.csdn.net/pdsu161530247/article/details/81582980

source让配置文件生效

### 防火墙

firewall-cmd --list-ports查看开启 或者iptables -nL

查看版本： firewall-cmd --version

查看帮助： firewall-cmd --help

显示状态： firewall-cmd --state

查看所有打开的端口： firewall-cmd --zone=public --list-ports

更新防火墙规则： firewall-cmd --reload

查看区域信息:  firewall-cmd --get-active-zones

查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0

拒绝所有包：firewall-cmd --panic-on

取消拒绝状态： firewall-cmd --panic-off

查看是否拒绝： firewall-cmd --query-panic



开启关闭防火墙以及其他操作https://www.cnblogs.com/Crazy-Liu/p/10837478.html



## 文件夹以及配置文件作用-----目录配置依据FHS

|      | 可分享                  | 不可分享(与主机有关不适合分享) |
| ---- | ----------------------- | ------------------------------ |
| 不变 | /usr(存放软件)          | /etc(配置文件)                 |
|      | /opt(第三方辅助软件)    | /boot(启动与内核文件)          |
| 可变 | /var/mail(用户邮箱)     | /var/run(程序相关)             |
|      | /var/spool/news(新闻组) | /var/lock(程序相关)            |

- **可分享**：可以分享给其他系统挂载使用的目录，包括执行文件与用户的邮件等数据，能够分享给网络上其他主机挂载用的目录
- **不可分享**：自己机器上面运行的设备文件或是与程序有关的socket文件等，由于仅与自身机器有关，所以当然就不适合分享给其他主机
- **不变**：有些数据是不会经常变动的，跟随着发行版而不变动。例如函数库、文件说明、系统管理员所管理的主机服务配置文件
- **可变动**：经常修改的数据，例如日志文件、一般用户可自行接受的新闻组等

>事实上FHS针对目录树架构仅定义出三层目录下面应该放置什么数据而已，分别是下面这三个目录的定义：

- /（root，根目录）：与启动系统有关
- /usr（unix software resource）：与软件安装/执行有关
- /var（variable）：与系统运行过程有关
  - /lib
    - /rpm:用来记录各种安装的软件

根目录（/）的意义与内容

>所有的目录都是由根目录衍生出来，同时根目录与启动、还原、系统修复等操作有关
>系统启动时需要特定的启动软件、内核文件、启动所需程序、函数库等文件数据
>软系统出现错误是，根目录也必须包含能够修复文件系统的程序才行

>FHS标准建议：根目录所在的分区应该越小越好，且应用程序所安装的软件最好不要与根目录放在同一个分区内，保持根目录越小越好。如此不但性能较佳，根目录所在的文件系统也较不容易发生问题。

根目录下文件的作用https://www.cnblogs.com/jszd/p/11182190.html

- /bin:(普通用户和管理员常用命令，存放在**单人维护模式下还能够被使用的命令**)
- /boot:(主要放置启动会用到的文件)
- /dev:(任何设备与接口都是以文件形式存放在这个目录)
  - /sd[a-p]:磁盘阵列
  - /md[0-9]
  - /shm通常是利用虚拟出来的磁盘通常占内存的一般物理内存,在里面建东西的速度是很快的
- /usr:(存放系统软件资源，放置的数据属于可分享与不可变动)
  - /bin:所有一般用户能够使用的命令都放在这里。同时此目录下不应该有子目录
    - /cron:保存crontab -e(非root)的循环任务
  - /lib:与/lib功能相同所以/lib链接到此目录的
    - /systemd/
      - system/:每个服务最主要的管理系统
  - /local:root在本机安装自己下载的软件，建议安装到本目录，便于管理
  - /sbin:非系统正常运行所需要的系统命令。目前/sbin目录就链接到此目录中的
  - /share:存放命令帮助文档和者软件帮助文档，几乎都是文本文件
  - /games:与游戏相关的数据
  - /include:c++等程序语言的头文件与包含文件放置处
  - /libexec:某些不被一般用户常用的执行文件或脚本
  - /src:一般源码放在这里
- /etc:(系统配置文件几乎都在这个目录但只有root有权利修改,==建议不要将可执行文件放在该目录里面==)
  - /systemd
    - /system/:管理员根据系统要求建立的脚本,优先级比/run/systemd/system高
  - /passwd:存放各个用户的信息
  - /shadow:存放个人密码
  - /ld.so.conf:需要写入缓存的记录文件
  - /crontab:系统循环任务
  - /group:所有组个名在这里面
  - /locale.conf:可以修改系统语言
  - /opt（必要）:这个目录在防止第三方辅助软件/opt的相关配置文件
  - /filesystems:系统指定的测试挂载文件系统类型的优先级
  - /shells:显示可以使用的shell
  - /pam.d:用来保存PAM的所有认证配置文件
  - /mdadm.conf
  - /services:定义端口与服务的映射
  - /yum.repos.d
    - /CentO S-Base.repo:yum配置文件
  - /profile.d
    - /*.sh:这里面所有的用户r权限的文件都会被/etc/profile调用,包含了操作界面的颜色、语系、别名等
  - /sysconfig
    - /network-scripts:网络配置目录
- /media: 存放**可删除的设备**
- /mnt:暂时挂载某些额外的设备
- /var：系统运行后占用硬盘容量的目录
  - /cache:应用程序本身运行过程中产生的一些缓存
    - /yum:yum软件缓存
  - /lib:程序本身执行过程中，需要使用到的数据文件放置的目录。此目录下各自软件要有各自的目录
    - /xfsdump
      - /inventory:xfsdump的备份记录
  - /lock：防止设备或文件同一时刻被多用户使用，使用时给该设备上锁，确保该设备只给单一软件使用（已经转移到/run/lock中）
  - /run：程序或服务启动后，将PID放置到这个目录，与/run相同，链接到/run目录
  - /mail：个人用户邮箱，也被放置到/var/spool/mail目录，通常这两个目录互为链接文件
  - /spool:放置队列数据。排队等待其他程序使用的数据，使用后会被删除
    - /cron: 计划任务数据
  - /spool
    - /news(新闻组)
  - /log：非常重要，日志文件放置的目录
    - /boot.log:记录系统检测与启动硬件信息
    - /wtmp这个文件用来存放登录数据
- /run:系统启动后所产生的各项信息
- /tmp:一般用户或正在执行的程序暂时放置文件的地方。任何人都可以存取，建议在启动时将本目录下数据都清除
- /run或/tmp数据接口文件常用来通过soocket来进行数据沟通
  - /run
    - /systemd
      - /system/:系统执行过程中所产生的服务脚本,优先级比/usr/lib/systemd/system高
- /lib:(放置的是启动时会用到的库函数,以及在/bin和/sbin下面的命令会调用的库函数)
  - /modules:放置驱动程序(内核模块)
    - $(uname -r)/kernel/fs
      - 例如ext4驱动就在/lib/modules/$(uname -r)/kernel/fs/ext4下
- /opt:(给第三方辅助软件放置的目录，以前的linux系统中，习惯安装在/usr/local下)
- /sbin:(很多命令是用来设置系统环境的,这些命令只有root才能设置系统里面包含了修复启动还原系统所需要的命令)
  - 某些服务器软件程序放置在/usr/sbin中.本机自行安装的软件所产生中的系统执行文件,则放置到/usr/local/sbin
- /srv:(网络服务器运行后所需要使用的数据目录)
- /home:(普通用户的家目录,~回到这个目录下的指定文件)
- /root:(root用户的家目录，root的家目录和根目录放置在同一个分区中)
- /proc:(本身是虚拟文件系统，数据都放置在内存中。系统的信息例如内核、进程信息、外接设备、网络状况，他放置的数据都在内存中)
  - /proc/mdstat:磁盘阵列数据
  - filesystems:Linux已经加载的文件系统类型
  - 数字目录都是进程中的pid比如/proc/1就是对应进程中pid为1的进程,里面存放着相关进程的信息
- /sys:不占硬盘容量也是保存系统信息的.





## 小知识

安装中文帮助手册

> yum install man-pages-zh-CN.noarch
>
> echo "alias cman='man -M /usr/share/man/zh_CN'" >> ~/.bashrc
>
> source .bashrc



重定向

