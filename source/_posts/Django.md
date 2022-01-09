---
title: Django
date: 2022-01-09 12:50:03
tags:
      - Python
      - Django
top_img: /img/Django.jpeg
cover: /img/Django.jpeg
---

# Django

> django-admin.py startproject HelloWorld

创建Django项目

> python manage.py runserver 0.0.0.0:8000

启动项目

## urls.py

该 Django 项目的 URL 声明; 一份由 Django 驱动的网站"目录"。

```python
from django.conf.urls import url
 
from . import views
#网页路径对应的函数解析
urlpatterns = [
    url(r'^$', views.hello),
]

```

```python
#在访问/hello时用views.hello来进行解析
from django.urls import path
 
from . import views
 
urlpatterns = [
    path('hello/', views.hello),
]
```

--------------------

> 对上文的path进行解释

### path() 函数

Django path() 可以接收四个参数，分别是两个必选参数：route、view 和两个可选参数：kwargs、name。

语法格式：

```
path(route, view, kwargs=None, name=None)
```

- route: 字符串，表示 URL 规则，与之匹配的 URL 会执行对应的第二个参数 view。
- view: 用于执行与正则表达式匹配的 URL 请求。
- kwargs: 视图使用的字典类型的参数。
- name: 用来反向获取 URL。

Django2. 0中可以使用 re_path() 方法来兼容 1.x 版本中的 **url()** 方法，一些正则表达式的规则也可以通过 re_path() 来实现 。

```
from django.urls import include, re_path

urlpatterns = [
    re_path(r'^index/$', views.index, name='index'),
    re_path(r'^bio/(?P<username>\w+)/$', views.bio, name='bio'),
    re_path(r'^weblog/', include('blog.urls')),
    ...
]
```

## templates

```
HelloWorld/
|-- HelloWorld
|   |-- __init__.py
|   |-- __init__.pyc
|   |-- settings.py
|   |-- settings.pyc
|   |-- urls.py
|   |-- urls.pyc
|   |-- views.py
|   |-- views.pyc
|   |-- wsgi.py
|   `-- wsgi.pyc
|-- manage.py
`-- templates
    `-- runoob.html
```

假设新建template文件夹后dir是这样的

然后在setting.py中设置为'DIRS': [BASE_DIR+"/templates",],

在setting文件加中我们可以看到==BASE_DIR = Path(__file__).resolve().parent.parent==这样的设置

首先新建一个runoob.html然后输入代码如下:

```html
<h1>{{ hello }}</h1>
```

## 模板用法

```
Django 模板标签
变量
模板语法：

view：｛"HTML变量名" : "views变量名"｝
HTML：｛｛变量名｝｝
```

如果要新建一个==templates==文件夹用来存储静态html然后修改==setting.py==中的数据

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR, "/templates",],       # 修改位置
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```



如果在静态文件中定义有要修改的变量,如下:修改==hello==变量

```html
<h1>{{ hello }}</h1>
```

修改views.py

```python
from django.shortcuts import render
 
def runoob(request):
    context          = {}
    context['hello'] = 'Hello World!'
    return render(request, 'runoob.html', context)
```

### 变量

html中的文件内容

```
<p>{{ name }}</p>
```

对应view.py中的内容

```python
from django.shortcuts import render

def runoob(request):
  views_name = "菜鸟教程"
  return  render(request,"runoob.html", {"name":views_name})
```

### 列表

html中的内容

```html
<p>{{ views_list }}</p>   # 取出整个列表
<p>{{ views_list.0 }}</p> # 取出列表的第一个元素
```

对应view.py中的内容

```python
from django.shortcuts import render

def runoob(request):
    views_list = ["菜鸟教程1","菜鸟教程2","菜鸟教程3"]
    return render(request, "runoob.html", {"views_list": views_list})
```

### 字典

html中的内容

```html
<p>{{ views_dict }}</p>
<p>{{ views_dict.name }}</p>
```

对应view.py中的内容

```python
from django.shortcuts import render

def runoob(request):
    views_dict = {"name":"菜鸟教程"}
    return render(request, "runoob.html", {"views_dict": views_dict})
```

### 过滤器



html中的内容

```html
{{ 变量名 | 过滤器：可选参数 }}
```

可以将过滤器的结果用来作为下一个过滤器输入:

过滤器后面可以接参数用:""来输入

default有false(0  0.0  False  0j  ""  []  ()  set()  {}  None都是false)

```
{{my_list|first(取出第一个元素)
				|upper(转换为大写)
				|truncatewords:"参数"
				|default(设置默认值):"参数"
				|length(返回对象的长度，适用于字符串和列表。)
				|filesizeformat(以更易读的方式显示文件的大小(即'13 KB', '4.1 MB', '102 bytes'等)
				|date(根据给定格式对一个日期变量进行格式化):如"Y-m-d H:i:s"}}
				|truncatechars(如果字符串包含的字符总个数多于指定的字符数量，那么会被截断掉后面的部分):"长度"
				}}
```

对应view.py中的内容

```python
from django.shortcuts import render

def runoob(request):
    views_dict = {"name":"菜鸟教程"}
    return render(request, "runoob.html", {"views_dict": views_dict})
```

### if/else

html代码

```html
{% if condition1 %}
   ... display 1
{% elif condition2 %}
   ... display 2
{% else %}
   ... display 3
{% endif %}
```

例如:

```html
{%if num > 90 and num <= 100 %}
优秀
{% elif num > 60 and num <= 90 %}
合格
{% else %}
一边玩去～
{% endif %}
```

```python
from django.shortcuts import render

def runoob(request):
    views_num = 88
    return render(request, "runoob.html", {"num": views_num})
```

### for标签

```html
<ul>
{% for athlete in athlete_list %}
    <li>{{ athlete.name }}</li>
{% endfor %}
</ul>
```

例如:

```html
{% for i in views_list %}
{{ i }}
{% endfor %}
```

```python
from django.shortcuts import render

def runoob(request):
    views_list = ["菜鸟教程","菜鸟教程1","菜鸟教程2","菜鸟教程3",]
    return render(request, "runoob.html", {"views_list": views_list})
```

如果要引用dict类型用以下代码:

```html
{% for i,j in views_dict.items %}
{{ i }}---{{ j }}
{% endfor %}
```

```python
from django.shortcuts import render

def runoob(request):
    views_dict = {"name":"菜鸟教程","age":18}
    return render(request, "runoob.html", {"views_dict": views_dict})
```

在 {% for %} 标签里可以通过 {{forloop}} 变量获取循环序号。

- forloop.counter: 顺序获取循环序号，从 1 开始计算
- forloop.counter0: 顺序获取循环序号，从 0 开始计算
- forloop.revcounter: 倒叙获取循环序号，结尾序号为 1
- forloop.revcounter0: 倒叙获取循环序号，结尾序号为 0
- forloop.first（一般配合if标签使用）: 第一条数据返回 True，其他数据返回 False
- forloop.last（一般配合if标签使用）: 最后一条数据返回 True，其他数据返回 False

再例如对于for的使用

```html
{% for i in listvar %}
    {{ forloop.counter }}
    {{ forloop.counter0 }}
    {{ forloop.revcounter }}
    {{ forloop.revcounter0 }}
    {{ forloop.first }}
    {{ forloop.last }}
{% empty %}
{% endfor %}

```

```python
from django.shortcuts import render

def runoob(request):
     views_list = ["a", "b", "c", "d", "e"]
     return render(request, "runoob.html", {"listvar": views_list})
```

可选的 {% empty %} 从句：在循环为空的时候执行（即 in 后面的参数布尔值为 False ）

### ifequal/ifnotequal 标签

百度把

### 注释标签

```html
{# 这是一个注释 #}
```

### include 标签

```html
{% include "nav.html" %}
```

### csrf_token

百度

### 自定义标签和过滤器

百度

### 配置静态文件

百度



## 模型



1. 建立数据库的基本步骤:setting.py配置(设置user,password,host.etc)
2. 修改setting.py同目录下的__init__.py(用pymysql来代替mysqlclient)
3. 创建app(没有这个就无法使用数据库)
4. 修改model添加一个类(这个类就是建立一个表)
5. 在setting.py中添加app(用来引入这个app)
6. 修改urls.py(创建请求映射)
7. 创建响应函数(创建请求对应的函数)

在第5条建立完成后

> py manage.py migrate

来创建一个数据库

支持的数据库

- CockroachDB 

- Firebird 

- Microsoft SQL Server



### setting中的database设置:

```python
DATABASES = {     
    'default': 
    	{
            'ENGINE': 'django.db.backends.sqlite3',         
        	'NAME': 'mydatabase',     
        } 
}
#ENGINE参数可以设置如下
#django.db.backends.sqlite3 
#django.db.backends.postgresql
#django.db.backends.mysql
#django.db.backends.oracle


#NAME parameter of official description:
#official:The name of your database. If you’re using SQLite, the database will be a file on your computer; #in that case, NAME should be the full absolute path, including filename, of that file. The default value, #BASE_DIR / 'db.sqlite3', will store the file in your project directory.
```

如果没有使用SQLite 作为数据库就要设置USER,PASSWORD,HOST.For more details,for example

```python
DATABASES = {     
    'default': {         
        'ENGINE': 'django.db.backends.mysql',    # 数据库引擎         
        'NAME': 'runoob', # 数据库名称         
        'HOST': '127.0.0.1', # 数据库地址，本机 ip 地址 127.0.0.1          
        'PORT': 3306, # 端口          
        'USER': 'root',  # 数据库用户名         
        'PASSWORD': '123456', # 数据库密码  
    }
}
```



### 接下来需要告诉Django链接的mysql数据库

```python
# 在与 settings.py 同级目录下的 __init__.py 中引入模块和进行配置 
import pymysql 
pymysql.version_info = (1, 4, 13, "final", 0)
pymysql.install_as_MySQLdb()
```

在使用模型之前必须建立一个app



### 建立app

```
django-admin.py startapp TestModel
```



### 在models.py中添加一个表(类)

```python
from django.db import models   
	class Test(models.Model):    
        name = models.CharField(max_length=20)
```



### 然后在setting.py中修改INSTALLED_APPS中添加TestModel如:

```python
INSTALLED_APPS = (     
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'TestModel',               # 添加此项 
)
```

然后建立对应的表

```
$ python3 manage.py migrate   # 创建表结构  
$ python3 manage.py makemigrations TestModel  # 让 Django 知道我们在我们的模型有一些变更 
$ python3 manage.py migrate TestModel   # 创建表结构
```



### 添加请求响应解析在urls.py中

```python
path('testdb/', testdb.testdb),
```



### 实现映射

新建一个testdb.py里面写一个testdb函数



### for Test DATABASE

 the test databases are destroyed when all the tests have been executed.

执行完所有测试数据数据库将被销毁

使用test --keepdb保留到下次运行,如果不存在数据库将先创建任何迁移都将被应用

if a test run is forcefully interrupted

下次运行会请求是否保留上次中断的数据库,test --noinput .option to suppress that prompt and automatically destroy the database. 

所有的test数据库前面都会加上_test

The following is an introduction to creating a database 

the tests will use an in-memory database by default (i.e., the database will be created in memory, bypassing the filesystem entirely!). The TEST dictionary in DATABASES offers a number of settings to configure your test database. For example, if you want to use a different database name, specify NAME in the TEST dictionary for any given database in DATABASES.

对于用别的方式创建的数据库还需要提供user用户创建的权限

要对测试数据库的字符编码进行细粒度控制，请使用CHARSET TEST选项。 如果您使用的是MySQL，还可以使用COLLATION选项来控制测试数据库使用的特定排序规则。 有关这些及其他高级设置的详细信息，请参见设置文档。



### 对数据的操作

添加

1.方法一

```python
from app import models
#导入数据库模板
def add(request):
    book = models.Book(title = "菜鸟教程",price=30,public="菜鸟出版社",pub_date="2008-8-8")
    #Book为models属于models.Model的子类,Book构造里面传递所有创建数据添加的值
    book.save()
    #对数据进行存储
```

2.方法二

```python
from app import models
#导入数据库模板
def add(request):
    models.Book.objects.create(title = "菜鸟教程",price=30,public="菜鸟出版社",pub_date="2008-8-8")
    #使用Book.objects.create来创建一个新的数据
```



查询

```python
def testdb(request):     
    # 初始化     
	response = ""     
    response1 = ""               
    # 通过objects这个模型管理器的all()获得所有数据行，相当于SQL中的SELECT * FROM 
    list = Test.objects.all()             
    # filter相当于SQL中的WHERE，可设置条件过滤结果     
    response2 = Test.objects.filter(id=1)           
    # 获取单个对象     
    response3 = Test.objects.get(id=1)           
    # 限制返回的数据 相当于 SQL 中的 OFFSET 0 LIMIT 2;     		
    Test.objects.order_by('name')[0:2]          
    #数据排序     		
    Test.objects.order_by("id")          
    # 上面的方法可以连锁使用     
    Test.objects.filter(name="runoob").order_by("id")         
    # 输出所有数据     
    for var in list:         
        response1 += var.name + " "     
    	response = response1     
    #这里如果想要取出表中的某一行或者某一列用(这里讲一下多组数据)
    #由于all()返回的是一个可迭代对象所以用for每个都取出来然后用i.列名就可以查找输出每一个数据了(返回一个str类型)
   	for i in list:         
        print(i.id,end='')         
        print(" "+i.name)
   	return HttpResponse("<p>" + response + "</p>")
```

更新可以用updata或者用

```python
from django.http import HttpResponse   
from TestModel.models import Test   
# 数据库操作 
def testdb(request):     
    # 修改其中一个id=1的name字段，再save，相当于SQL中的UPDATE     
    test1 = Test.objects.get(id=1)     
    test1.name = 'Google'     
    test1.save()          
    # 另外一种方式     
    #Test.objects.filter(id=1).update(name='Google')          
    # 修改所有的列     
    # Test.objects.all().update(name='Google')          
    return HttpResponse("<p>修改成功</p>")
```

删除数据

```python
from django.http import HttpResponse   
from TestModel.models import Test   
# 数据库操作 
def testdb(request):     
    # 删除id=1的数据     
    test1 = Test.objects.get(id=1)     
    test1.delete()          
    # 另外一种方式     
    # Test.objects.filter(id=1).delete()          
    # 删除所有数据     
    # Test.objects.all().delete()          
    return HttpResponse("<p>删除成功</p>")
```

### 单表

### 多表

### 聚合查询

## 表单&视图&路由

- GET方法

- POST方法

```
GET实现方式
默认使用方式为GET,HttpResponse,render,redirect
POST
用表单实现{% csrf_token %}这句话是用来保持链接的,目前只能用网页提交(一般POST由系统提交的意义不打)

<form action="/search-post" method="post">         
	{% csrf_token %}         
	<input type="text" name="q">         
	<input type="submit" value="Submit">     
</form>
```

-------------------------------------------------------------

对于在views.py(视图)中的映射方法

方法中的request(数据类型为 QueryDict)解读:

可以使用==对象.方法==的方式来获取request的值

- GET如:request.GET.get("name")
- POST如:request.POST.get("name")
- body如:request.body
- path如:request.path
- method如:request.method

HttpResponse 对象的形式:HttpResponse()、render()、redirect()

---------------------------

urls.py中

在Django1.1.x 版本用url()函数(正则和自动填充)

Django 2.2.x 之后的版本分解为path()、re_path()

正则分组传递参数

1.无名分组

```
urlpatterns = [
	path('admin/', admin.site.urls),
    re_path("^index/([0-9]{4})/$", views.index), 
] 
```

```python
from django.shortcuts 
import HttpResponse  
def index(request，year):     
    print(year) # 一个形参代表路径中一个分组的内容，按顺序匹配     
    return HttpResponse('菜鸟教程')
```

2.有名分组

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    re_path("^index/(?P[0-9]{4})/(?P[0-9]{2})/$", views.index),
]
```

```python
from django.shortcuts import HttpResponse
def index(request, year, month):
    print(year,month) # 一个形参代表路径中一个分组的内容，按关键字对应匹配
    return HttpResponse('菜鸟教程')
```

路由分发

当创建多个app时需要调用的

```python
from django.contrib import admin
from django.urls import path,include # 从 django.urls 引入 include
urlpatterns = [
    path('admin/', admin.site.urls),
    path("app01/", include("app01.urls")),
    path("app02/", include("app02.urls")),
]
```

反向解析

```
path("login1/", views.login, name="login")
```

这样就将login可以和前面的url绑定起来,直接使用login来进行重定向跳转这样以后修改url就不用修改很多源文件了

在views.py中如果要跳转要用

```
return redirect(reverse("login"))
```

如果要提交表单到页面，利用 {% url "路由别名" %} 反向解析如:

```html
<form action="{% url 'login' %}" method="post"> 
```

带有参数的反向解析

- 无名

```
re_path(r"^login/(?P[0-9]{4})/$", views.login, name="login")
```

- views.py中

```python
return redirect(reverse("login",args=(10,)))
```



- 有名

```python
re_path(r"^login/(?P<year>[0-9]{2})/&",views.login,name="login")
```

- views.py中 

```python
return redirect(reverse("login",kwargs={"year":3333})
```



对于存在app的urls.py中的反向解析:

```python
path("app01/", include(("app01.urls","app01")))  
path("app01/", include(("app02.urls","app02")))
```

然后在app01和app02文件夹下的urls.py分别写入

在外部的views.py可以采用如下方式来

```
return redirect(reverse("app名称:路由别名"))
```

在html可以采用:

```html
{% url "app名称：路由别名" %}
```

数据库全字段显示

```
from django.forms.models import model_to_dict
from Book_info.models import Book
    allbook = Book.objects.all()
    for i in allbook:
        print(model_to_dict(i))
```

```
from django.forms.models import model_to_dict
from Book_info.models import Book
    allbook = Book.objects.all()
    for i in allbook:
        print(model_to_dict(i))
```
