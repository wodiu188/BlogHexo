---
title: Hexo和MD冲突问题
date: 2022-01-09 13:55:06
tags:
      - Blog
top_img: /img/Hexo.jpeg
cover: /img/Hexo.jpeg
---



> 有时候我们在写文章的时候会用到{{}}或者{##}这种标识符,但是在Hexo使用的是Nunjucks进行渲染,这些都是特殊用法

### 第一种方法

根据报错为止将文章中的{{}}或者{##}进行修改,或者不要被 

> \`\`\`包裹



### 第二种方法

对含有这些特殊字符的首位根据下面的例子添加代码

>{% raw %}
>
>{{ 包裹的内容 }}
>
>{% endraw %}



### 第三种方法

> node_modules/nunjucks/src/lexer.js

在这个文件夹下将,这种东西进行修改

```csharp
var VARIABLE_START = '{{';
var VARIABLE_END = '}}';
```

> 但是你要是修改了这些,有一些功能可能就使用不了了,比如新建文章时的title,data什么的都是object null
>
> 需要修改\scaffolds文件夹下面的文件

而且如果使用npm install进行更新那么这个文件可能被更改回来这样你要还重新改
