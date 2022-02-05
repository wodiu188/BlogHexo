---
title: Python-进程！线程！！协程！！！
date: 2022-01-13 19:43:41
tags:
      - Python
      - 理论
categories:
description: 众所周知!!!!,多任务是大部分语言都有的功能,这里我们用Python作为例子来讲解
keywords:
      - 线程
      - 进程
      - 协程
      - WSGI
      - Python的web底层
top_img: /img/py进程线程协程.jpg
cover: /img/py进程线程协程.jpg
---

# 多任务

### 进程

一个程序可以有多个进程,但是每个进程至少有一个线程,在多核CPU下每个核可以操作一个进程,这样就可以达到进程的并发



python在Linux/Unix可是直接使用foke()

> 注意了别的函数一般是调用一次返回一次结果,而这个是调用一次返回两次结果

```python
import os

print('Process (%s) start...' % os.getpid())
# Only works on Unix/Linux/Mac:
pid = os.fork()
if pid == 0:
    print('I am child process (%s) and my parent is %s.' % (os.getpid(), os.getppid()))
else:
    print('I (%s) just created a child process (%s).' % (os.getpid(), pid))
```

运行结果如下

>Process (876) start...
>I (876) just created a child process (877).
>I am child process (877) and my parent is 876.



而在Windows下需要使用multiprocessing来

```python
from multiprocessing import Process
import os

# 子进程要执行的代码
def run_proc(name):
    print('Run child process %s (%s)...' % (name, os.getpid()))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Process(target=run_proc, args=('test',))
    print('Child process will start.')
    p.start()
    p.join()
    print('Child process end.')
```

需要一个运行的函数

或者使用进程池

```python
from multiprocessing import Pool
import os, time, random

def long_time_task(name):
    print('Run task %s (%s)...' % (name, os.getpid()))
    start = time.time()
    time.sleep(random.random() * 3)
    end = time.time()
    print('Task %s runs %0.2f seconds.' % (name, (end - start)))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Pool(4)
    for i in range(5):
        p.apply_async(long_time_task, args=(i,))
    print('Waiting for all subprocesses done...')
    p.close()
    p.join()
    print('All subprocesses done.')
```



进程的输入输出管道

首先是输出管道subprocess

```python
import subprocess

print('$ nslookup www.python.org')
r = subprocess.call(['nslookup', 'www.python.org'])
print('Exit code:', r)
```

这个作用是开启一个子进程然后执行 nslookup www.python.org的意思



```python
import subprocess

print('$ nslookup')
# subprocess.PIPE的意思是代表这些管道要开放
p = subprocess.Popen(['nslookup'], stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
output, err = p.communicate(b'set q=mx\npython.org\nexit\n')
print(output.decode('utf-8'))
print('Exit code:', p.returncode)
```

开启一个进程,关于执行nslookup的并且开启输入,输出,错误管道给变量p

在用p的communicate来进行输入命令最后返回输出和错误结果





```$ nslookup
默认服务器:  UnKnown
Address:  192.168.10.254

> > 服务器:  UnKnown
Address:  192.168.10.254

python.org	MX preference = 50, mail exchanger = mail.python.org
> 
```

运行结果上面是:



在这里进程间的通信可以使用queue来进行



> 在Unix/Linux下，`multiprocessing`模块封装了`fork()`调用，使我们不需要关注`fork()`的细节。由于Windows没有`fork`调用，因此，`multiprocessing`需要“模拟”出`fork`的效果，父进程所有Python对象都必须通过pickle序列化再传到子进程去，所以，如果`multiprocessing`在Windows下调用失败了，要先考虑是不是pickle失败了。

### 线程

是操作系统能够管理的最小运算单位,由于一个进程只能丢给一个核心处理。所以就算多线程也是最高的速度也是单核并发(但是在CPU空闲情况下是可以并行的)



普通的多线程

```python
import time, threading

# 新线程执行的代码:
def newPrint():
    print(threading.current_thread(),"--------------------")
    time.sleep(1)
threadList=[]
for i in range(1000000):
    t = threading.Thread(target=newPrint,name=i)
    t.start()
    threadList.append(t)

for j in threadList:
    j.join()

```



用Lock来进行加锁

```python
import time, threading

balance =0
lock = threading.Lock()
# 新线程执行的代码:
def newPrint():
    global balance
    lock.acquire()
    balance += 1
    balance -= 3
    balance += 1
    lock.release()
    print(balance)
threadList=[]
for i in range(10000):
    t = threading.Thread(target=newPrint,name=i)
    t.start()
    threadList.append(t)

for j in threadList:
    j.join()
print("-----结束------")
```

防止balance变量被意外修改



当我们需要为每个线程创建一个变量时可以使用dict还可以使用



ThreadLocal

```python
import threading
    
# 创建全局ThreadLocal对象:
local_school = threading.local()

def process_student():
    # 获取当前线程关联的student:
    std = local_school.student
    print('Hello, %s (in %s)' % (std, threading.current_thread().name))

def process_thread(name):
    # 绑定ThreadLocal的student:
    local_school.student = name
    process_student()

t1 = threading.Thread(target= process_thread, args=('Alice',), name='Thread-A')
t2 = threading.Thread(target= process_thread, args=('Bob',), name='Thread-B')
t1.start()
t2.start()
t1.join()
t2.join()
```



一个`ThreadLocal`变量虽然是全局变量，但每个线程都只能读写自己线程的独立副本，互不干扰。`ThreadLocal`解决了参数在一个线程中各个函数之间互相传递的问题。



> `ThreadLocal`最常用的地方就是为每个线程绑定一个数据库连接，HTTP请求，用户身份信息等，这样一个线程的所有调用到的处理函数都可以非常方便地访问这些资源。



### 协程

协程是单进程单线程的超越函数的调度机制，它通过一定的调度手段进行调度。3.协程不是未来（反驳赖勇浩）。协程是很早之前就有的。很早之前，windows就有纤程的概念，Linux不太确定。但是它一直作为小众的API而存在。协程不是被操作系统内核所管理，而完全是由程序所控制（也就是在用户态执行），这样带来的好处是性能大幅度的提升，因为不会像线程切换那样消耗资源。协程不是进程也不是线程，而是**一个特殊的函数**，这个函数可以在某个地方挂起，并且可以重新在挂起处外继续运行。所以说，协程与进程、线程相比并不是一个维度的概念。  (一定是串行处理)










​	
