---
title: JQuery
date: 2022-01-15 19:06:27
tags:
      - 前端
      - JavaScript
      - Ajax
categories:
      - 前端
description: 感觉没啥好讲的，随便弄一篇文章讲讲顺带最好了了Ajax的原理
keywords:
top_img: /img/jQuery.jpg
cover: /img/jQuery.jpg
---

# JQuery





#### 安装

下载jQuery,然后引入

https://jquery.com/download/

```xml
<head>
<script src="jquery.js"></script>
</head>
```

或者使用csdn

```xml
<head>
<script src="http://libs.baidu.com/jquery/1.10.2/jquery.min.js" rel="external nofollow" >
</script>
</head>
```





### 语法

#### 基础语法

$(选择的标签元素).要执行的函数



![image-20220115191627241](..\..\public\img\JQuery-1.png)



#### 定位元素(选择器):

##### 根据标签选择

```javascript
$("p").hide();
//选择p标签
```

##### 根据id选择

```JavaScript
$("#lll").hide()
//选择id为lll的标签
```

##### 根据class选择

```JavaScript
$(".kkk").hide()
//选择class为kkk的标签
```



##### 其他

| 语法                     | 描述                                                    |
| :----------------------- | :------------------------------------------------------ |
| $("*")                   | 选取所有元素                                            |
| $(this)                  | 选取当前 HTML 元素                                      |
| $("p.intro")             | 选取 class 为 intro 的 <p> 元素                         |
| $("p:first")             | 选取第一个 <p> 元素                                     |
| $("ul li:first")         | 选取第一个 <ul> 元素的第一个 <li> 元素                  |
| $("ul li:first-child")   | 选取每个 <ul> 元素的第一个 <li> 元素                    |
| $("[href]")              | 选取带有 href 属性的元素                                |
| $("a[target='_blank']")  | 选取所有 target 属性值等于 "_blank" 的 <a> 元素         |
| $("a[target!='_blank']") | 选取所有 target 属性值不等于 "_blank" 的 <a> 元素       |
| $(":button")             | 选取所有 type="button" 的 <input> 元素 和 <button> 元素 |
| $("tr:even")             | 选取偶数位置的 <tr> 元素                                |
| $("tr:odd")              | 选取奇数位置的 <tr> 元素                                |

##### 指定选择

如果选取了多个元素可以使用下面的方法来获取更详细的元素

- first() 获取第一个元素

  - > $("p").first() 获取第一个p

- last() 获取最后一个元素

- eq() 获取和指定下标一样的元素

  - >$(document).ready(function(){
    > $("p").eq(1);
    >});

![image-20220115210013313](..\..\public\img\JQuery-2.png)



放学喽不记喽~~~



#### 操作函数

##### 事件

- click():为元素添加一个click事件

  - ```JavaScript
    $("p").click(function(){      
      // action goes here!!        
    });
    ```

- 其他事件就不具体解释了

- dblclick():双击

- mouseenter():鼠标越过

- mouseleave():鼠标离开

- mousedown():按下

- mouseup():松开

- hover():悬停

- focus():聚焦

- blur():失去聚焦

- keydown():按下一直触发,返回键盘代码

- keypress():按下并产生一个字符时触发,==shift,ctrl等不会触发==

- keyup():松开时触发,返回键盘代码



##### 效果

- hide():隐藏元素

  - ```JavaScript
    $("#hide").click(function(){
      $("p").hide();
    });
    ```

- show():显示元素

- toggle():显示和隐藏之间切换

  - > $(*selector*).toggle(*speed,callback*);

  - speed代表显示速度

  - callback回调函数,$(selector)选中了几个callback就会调用几次,该函数可以是匿名函数,callback函数名后加括号，会立刻执行函数体，而不是等到显示/隐藏完成后才执行

- fadeIn():淡入已经隐藏的元素

  - > $(*selector*).fadeIn(*speed,callback*);

  - 参数和上面的差不多

- fadeOut():淡出已经显示的元素

  - > $(*selector*).fadeOut(*speed,callback*);

- fadeToggle():淡入淡出切换

- fadeToggle():不透明度

  - > $(*selector*).fadeTo(*speed,opacity,callback*);

  - opacity:用于设置不透明度0-1

下面的都差不多了就不给例子了

- slideDown():往下滑
- slideUp():往上滑
- slideToggle():上下切换



- animate():使用css动画

  - > $(*selector*).animate({*params*}*,speed,callback*);

    params是css参数

  - ```JavaScript
    $("button").click(function(){
      $("div").animate({
        left:'250px',
        opacity:'0.5',
        height:'150px',
        width:'150px'
      });
    }); 
    ```

  - 上面的150px这种数可以换成==height:"+=150px"==这种相对参数或者使用预设值==height:"toggle"==这种

  - animate也可以进行按顺序

    - ```JavaScript
      $("button").click(function(){
        var div=$("div");
        div.animate({height:'300px',opacity:'0.4'},"slow");
        div.animate({width:'300px',opacity:'0.8'},"slow");
        div.animate({height:'100px',opacity:'0.4'},"slow");
        div.animate({width:'100px',opacity:'0.8'},"slow");
      }); 
      ```

- stop():用来停止

  - > $(*selector*).stop(*stopAll,goToEnd*);

  - stopAll的意思:是否停止当前所有的动画,无论是否完成默认是,false

  - goToEnd的意思:立刻完成所有的动画

  - ```JavaScript
    $("#stop").click(function(){
      $("#panel").stop();
    });
    ```

  

> 注意回调函数是在事件完成时调用
>
> 动作时可以一直.的如下:
>
> ```JavaScript
> $("#p1").css("color","red")
>   .slideUp(2000)
>   .slideDown(2000);
> ```



#### 标签元素操作

##### 获取值

- text():获取或者设置值

  - ```JavaScript
    $("#bt1").text()//获取值
    $("#bt1").text("asdfas")//设置值
    ```

- html():获取或者设置值(可以生成html标签)

- val():设置或者获取表单的数据

  > 上面这几个设置值的时候用回调函数的时候
  >
  > function(i,origText){
  >   return "Old text: " + origText + " New text: Hello world!
  >   (index: " + i + ")";
  >  }
  >
  > 如上的函数第一个param是代表当前元素的下标,第二个代表以前的值

- attr():用于获取或者设置属性的值

##### 添加或者删除元素

- append(s) 在标签的内部的最后加一个元素

  - 可以生成s中的标签

- prepend(s) 在标签的开头部分插入内容

- after 代表在选定标签的外部,的后面插入元素

- before 代表在选定标签的外部,的前面插入元素

- remove():删除当前以及子元素

  - > $("p").remove(".italic");
    >
    > 删除p标签下面所有的class=italic的元素

- empty():删除子元素

##### class操作

- addClass():添加class

- removeClass():删除class

- toggleClass():

  - $(p).toggleClass("sss"):如果p的class有sss就删除,没有就加上

- css():改变html的css

  - ```JavaScript 
    $("p").css("background-color","red");//改变p标签的背景色
    $("p").css();//返回css的属性值
    ```

##### 尺寸

:smirk:https://www.w3cschool.cn/jquery/jquery-dimensions.html



##### 遍历相关元素

下面这些都可以写参数用于定位

- parent() 返回直接元素

- parents() 返回所有祖先元素

- parentsUntil() 返回介于两个元素之间的元素

  - ```JavaScript
    $(document).ready(function(){
      $("span").parentsUntil("div");
    });
    ```

- children() 返回所有直接子元素

- find() 返回后面所有的元素

同胞元素和查找父子元素类似

- siblings() 方法返回被选元素的所有同胞元素
- next() 返回下一个同胞元素
- nextAll() 返回所有同胞元素
- nextUntil() 返回介于元素之间的同胞元素
- prev() 返回前一个元素
- prevAll() 返回前面所有的元素
- prevUntil() 返回前面介于元素之间的同胞元素





