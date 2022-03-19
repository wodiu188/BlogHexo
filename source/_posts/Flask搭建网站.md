---
title: Flask搭建网站
date: 2022-01-13 15:16:07
tags:
      - Python
      - 后端
top_img: /img/Flask.jpeg
cover: /img/Flask.jpeg
categories: 
description: 简单的对Flask进行讲解
keywords:
      - Flask开发基础
      - 转发和重定向
---





## Flask

安装环境

pip install flask

pip install Jinjia2



创建python环境的项目



然后创建python文件，并设置Flask环境

```python
app = Flask(__name__,
            static_url_path='/static',#静态文件路径
            static_folder='static',
            template_folder='templages' #模板文件
            )

if __name__ == '__main__':
    app.run()
```



### 路由

#### 普通路由

```python
@app.route("/")
def index():
    return "wodiu"
```

这是普通的路由添加方式



#### 带参数路由

```python
@app.route("/aa/<user_id>")
def aaaa(user_id):
    return "wodiu %s"%(user_id)
```

或者使用下面这种方式

```python
from flask import request
@app.route("/aa")
def ccccc():
    return "wodiu %s"%(request.args['aaa'])
```

关于flask.request

https://blog.csdn.net/u011146423/article/details/88191225

![image-20220113152615716](..\..\public\img\Flask-1.png)





### 访问方法设置



```python
@app.route("/sss",methods=['POST'])
def a_page():
    user_name = request.form.get('uname')
    user_pass = request.form.get('upass')

    return "this is a page %s --- %s  hahahaha"%(user_name,user_pass)
```



### 模板

#### HTML部分

首先新建templates(最开始定义的环境名称)

然后新建tempa.html,然后代码如下

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

 <h1>i am father temp1</h1>

{% block contentBlock %}
 <h1>father html temp2</h1>
{% endblock %}

<h1>i am father temp last3</h1>

</body>
</html>
```

子页面定义如下

```html
{% extends 'tempa.html' %}

{% block contentBlock %}

{{super()}}
<h2>zi mo ban -------{{ida}}</h2>

{% endblock %}
```



#### python路由部分返回



```python
@app.route("/daicanshu/<id>")
def bbb(id):
    return flask.render_template("child.html",ida=id)
```

这个还进行了参数传递,ida就是页面要接收的数据





### 过滤器部分

自定义过滤器

```python
#首先是route

@app.route("/list")
def a_list():
    aaaaa=["1","2","3"]
    return flask.render_template("tempa.html",aList=aaaaa)
    
#然后定义过滤器
@app.template_filter('dore')
def do_reverse(li):
    temp = list(li)
    temp.reverse()
    return temp
```

html部分

```html
{{aList | dore}}
```

这句话的意思是将aList这个变量传递给名字叫dore这个过滤器





jinjia2自带的过滤器

```html
{{ range(1,100) | random }}
```

random是自带的



### jinjia2的if和for

```html
{% for item in aList %}
 <li>{{item}}</li><hr>
{% endfor %}

{% for num in range(1,10) %}
 <li>{{num}} 号</li>
{% endfor %}

{% if vip == 0 %}
 <h1>你没充值</h1>
 {% elif vip ==1 %}
 <h1>lv1的内容</h1>
{% endif %}
```



### Session

#### 第一种,返回一个response

```python
@app.route("/login")
def login():
    response = flask.make_response("success")
    response.set_cookie("name","wodiu",max_age=1000)
    response.set_cookie("type","0",max_age=1000)
    return response
```



获取的时候

```python
@app.route("/login_success")
def login_success():
    vip = int(request.cookies.get("type"))
    return flask.render_template("tempa.html",vip=vip)
```



想要删除的时候

```python
@app.route('/logout')
def logout():
    response = flask.make_response('退出')
    response.delete_cookie('name')
    response.delete_cookie('type')
    return response
```



#### 第二种,使用变量

创建session的时候添加变量

```python
@app.route('/login/<id>')
def b_page(id):
    #设置cookie
    session['user_id']=id
    session['vip'] = '0'
    return 'success'
```

删除session中的变量

```python
@app.route('/logout2')
def logout2():
    #session.pop('user_id',None)
    #session.pop('vip',None)

    #session['user_id']=False

    session.clear()

    return 'logout'
```

三种方法

- pop(键)方法
- 将值设为False
- clear()方法

### Flask的配置参数

 

就是对最开始的app变量进行配置

举个例子:

```python
#配置加密字符串
app.config['SECRET_KEY']="key123"
#设置7天有效
app.config['PERMANENT_SESSION_LIFETIME']=timedelta(days=7)
```



```
{
    'DEBUG': False,  # 是否开启Debug模式
    'TESTING': False,  # 是否开启测试模式
    'PROPAGATE_EXCEPTIONS': None,  # 异常传播(是否在控制台打印LOG) 当Debug或者testing开启后,自动为True
    'PRESERVE_CONTEXT_ON_EXCEPTION': None,  # 一两句话说不清楚,一般不用它
    'SECRET_KEY': None,  # 之前遇到过,在启用Session的时候,一定要有它
    'PERMANENT_SESSION_LIFETIME': 31,  # days , Session的生命周期(天)默认31天
    'USE_X_SENDFILE': False,  # 是否弃用 x_sendfile
    'LOGGER_NAME': None,  # 日志记录器的名称
    'LOGGER_HANDLER_POLICY': 'always',
    'SERVER_NAME': None,  # 服务访问域名
    'APPLICATION_ROOT': None,  # 项目的完整路径
    'SESSION_COOKIE_NAME': 'session',  # 在cookies中存放session加密字符串的名字
    'SESSION_COOKIE_DOMAIN': None,  # 在哪个域名下会产生session记录在cookies中
    'SESSION_COOKIE_PATH': None,  # cookies的路径
    'SESSION_COOKIE_HTTPONLY': True,  # 控制 cookie 是否应被设置 httponly 的标志，
    'SESSION_COOKIE_SECURE': False,  # 控制 cookie 是否应被设置安全标志
    'SESSION_REFRESH_EACH_REQUEST': True,  # 这个标志控制永久会话如何刷新
    'MAX_CONTENT_LENGTH': None,  # 如果设置为字节数， Flask 会拒绝内容长度大于此值的请求进入，并返回一个 413 状态码
    'SEND_FILE_MAX_AGE_DEFAULT': 12,  # hours 默认缓存控制的最大期限
    'TRAP_BAD_REQUEST_ERRORS': False,
    # 如果这个值被设置为 True ，Flask不会执行 HTTP 异常的错误处理，而是像对待其它异常一样，
    # 通过异常栈让它冒泡地抛出。这对于需要找出 HTTP 异常源头的可怕调试情形是有用的。
    'TRAP_HTTP_EXCEPTIONS': False,
    # Werkzeug 处理请求中的特定数据的内部数据结构会抛出同样也是“错误的请求”异常的特殊的 key errors 。
    # 同样地，为了保持一致，许多操作可以显式地抛出 BadRequest 异常。
    # 因为在调试中，你希望准确地找出异常的原因，这个设置用于在这些情形下调试。
    # 如果这个值被设置为 True ，你只会得到常规的回溯。
    'EXPLAIN_TEMPLATE_LOADING': False,
    'PREFERRED_URL_SCHEME': 'http',  # 生成URL的时候如果没有可用的 URL 模式话将使用这个值
    'JSON_AS_ASCII': True,
    # 默认情况下 Flask 使用 ascii 编码来序列化对象。如果这个值被设置为 False ，
    # Flask不会将其编码为 ASCII，并且按原样输出，返回它的 unicode 字符串。
    # 比如 jsonfiy 会自动地采用 utf-8 来编码它然后才进行传输。
    'JSON_SORT_KEYS': True,
    #默认情况下 Flask 按照 JSON 对象的键的顺序来序来序列化它。
    # 这样做是为了确保键的顺序不会受到字典的哈希种子的影响，从而返回的值每次都是一致的，不会造成无用的额外 HTTP 缓存。
    # 你可以通过修改这个配置的值来覆盖默认的操作。但这是不被推荐的做法因为这个默认的行为可能会给你在性能的代价上带来改善。
    'JSONIFY_PRETTYPRINT_REGULAR': True,
    'JSONIFY_MIMETYPE': 'application/json',
    'TEMPLATES_AUTO_RELOAD': None,
}
```

https://www.cnblogs.com/songzhixue/p/11171458.html



### 重定向和转发

```python
@app.route('/redirect')
def a_page():
    #站外
    #return flask.redirect('http://www.cctv.com')
    #站内
    return flask.redirect(flask.url_for('b_page'))
```

那么问题来了:smirk:转发和重定向的区别是啥:



转发就是将,请求所带的数据重新向站内的路由进行转发.

重定向就是让用户用浏览器重新向目标网站重新发一遍请求

|                      | 转发          | 重定向                                                   |
| -------------------- | ------------- | -------------------------------------------------------- |
| 跳转方式             | 服务器端转发  | 客户端转发                                               |
| 客户端发送请求次数   | 1次           | 2次                                                      |
| 客户端地址栏是否改变 | 不变          | 变                                                       |
| 是否共享request域    | 共享          | 不共享（request域中的数据丢失），必须使用session传递属性 |
| 是否共享response域   | 共享          | 不共享                                                   |
| 范围                 | 网站内        | 可以跨站点                                               |
| JSP                  | URL不可带参数 | URL可带参数                                              |
| 是否隐藏路径         | 隐藏          | 不隐藏                                                   |



### 结合SQL

导入pymysql

```python
import pymysql

db = pymysql.connect(host="localhost" 	#设置ip
                     ,user="root"		#设置用户名
                     ,password="123456"	#设置密码
                     ,db="wodiu")		#设置数据库
cur = db.cursor()						#获取一个游标
cur.execute("select * from aaa")		#执行语句
db.commit()								#提交语句并执行
List=[]					
for i in cur.fetchall():				#cur.fetchall是获取所有的数据
    print(list(i))

```

> db.rollback() #可以使用这条命令,如果发生异常时进行滚回



下面时sql的查询操作

- **fetchone():** 该方法获取下一个查询结果集。结果集是一个对象
- **fetchall():** 接收全部的返回结果行.
- **rowcount:** 这是一个只读属性，并返回执行execute()方法后影响的行数。
