---
title: Spring漏洞 持续更新中。。。。
date: 2022-03-07 19:59:16
tags:
      - Java
      - Spring
      - 安全
categories:
description:
keywords:
---

# SpringBug

### SpEL相关漏洞

SpEL表达式是spring出产的并且贯穿整个产品的表达式能够执行大多数Java语言能够做到的事情比如（变量，正则，bool，方法调用，创建bean等等）



最简单的表达式

```java
ExpressionParser parser = new SpelExpressionParser();
Expression exp = parser.parseExpression("'Hello World'");
String message = (String) exp.getValue();
```

首先介绍一下常见的类

> org.springframework.expression.ExpressionParser                     //一个表达式解析器
>
> org.springframework.expression.spel.standard.SpelExpression   //表达式对象,使用他的getValue即可执行表达式

下面介绍用该表达式如何引入类:

>![image-20220305190602756](D:\Code\pojo\Blog\BlogHexo\public\img\SpEL-1.png)

而且SpelExpressionParse这个类里面,被@Nullable修饰,而且默认情况下是,而且默认情况下是StandardEvaluationContext这个类

![image-20220307201540487](D:\Code\pojo\Blog\BlogHexo\public\img\springbug-1.png)

![image-20220307201704187](D:\Code\pojo\Blog\BlogHexo\public\img\springbug-2.png)

而StandardEvaluationContext他是继承EvaluationContext这个类是一个自动识别表达式的类型的一个类

EvaluationContext的子类有四个我们一般只用到两个



**SimpleEvaluationContext和StandardEvaluationContext**

SimpleEvaluationContext: 不包含类相关的危险操作，比较安全

StandardEvaluationContext: 包含所有功能，存在风险（默认加载）



所以默认来说这个类是比较危险的



#### 复现CVE-2018-1270

真实靶场可以使用docker来搭建

也可以使用docker cp来将文件copy 出来



当我们拿到这个包可以使用一下方式来查看是否存在危险类

比如我们要调查危险类org.springframework.expression.spel.standard

![image-20220305205254330](D:\Code\pojo\Blog\BlogHexo\public\img\代码审计-1.png)

>使用这种方式来查找这个包引用了那些类

![image-20220305205454772](D:\Code\pojo\Blog\BlogHexo\public\img\代码审计-2.png)

>这样就能找到这个包是否引用了该类,并且位置在哪里



能够在Java查看是否存在危险类

关于SpEL表达式最直观的修改方式是将StandardEvaluationContext修改为SimpleEvaluationContext



>基本所有的SpEL表达式都是由于这种情况触发的