---
title: group by和rand造成的报错原理
date: 2022-01-14 12:15:18
tags:
      - MySQL
      - 安全
categories: SQL注入
description: 深刻研究报错注入
keywords:
---



# 报错注入原理

首先从报错导致的语句开始

```mysql
select count(*),concat(0x3a,0x3a,(select database()),0x3a,0x3a,floor(rand()*2))a from information_schema.columns group by a;
```



这个语句会导致报错,至于报什么错呢...看下面

> ERROR 1062 (23000): Duplicate entry '::security::0' for key '<group_key>'

这句话的意思是有两个主键

这时我猜想,group by时可能会创建一个虚表,然后使用后面的字段来当作主键,但是这种(如下):

```mysql
select 1,2 from information_schema.columns group by a;
```

就不会报错,所以我猜想可能和聚合函数一起使用的时候才会有主键



这时我把语句稍作修改

```mysql
select count(*),2 from information_schema.columns group by floor(rand()*2);
```

发现这样就是最简的报错语句



那么再说他是怎么报错的.

这篇文章就很好啊~~~

:smirk:

https://blog.csdn.net/weixin_31481495/article/details/113229331

