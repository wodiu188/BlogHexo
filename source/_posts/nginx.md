---
title: nginx
date: 2022-01-17 08:47:09
tags:
      - 后端
      - Nginx
categories:
description: Nginx,中间件服务器
keywords:
top_img: /img/nginx.png
cover: /img/nginx.png
---

# Nginx

https://www.w3cschool.cn/nginx/sd361pdz.html这篇文章我觉得介绍的很好

nginx会有一个master进程和多个worker进程(一般由cpu的核数决定)，master通过外界接受的新号来管理worker进程(比如:kill -HUP pid这句话是告诉nginx需要重启,master收到新号后会重新加载配置文件然后创建新的worker替代掉老的worker)，而worker进程是和用户进行交流的==我们可以关闭master和后台然后进行调试==。

master如何管理worker呢？

首先master先建立listen socket链接，然后再fork多个worker进程，所有 worker 进程的 listenfd 会在新连接到来时变得可读之后所有的worker回去争抢读事件权。==所以一个请求只在一个worker中处理==，采用有着如下好处：

- 一个worker罢工后不会影响到别的
- 一个worker是一个请求处理所以省掉了上线文切换的资源浪费

nginx和apache比较：

- nginx是采用异步非阻塞处理的方式，所以可以处理成千上万的请求。
- apache是一个请求独占一个工作线程，线程带来的内存占用非常大，线程的上下文切换带来的 cpu 开销很大
- nginx不会创建大量无用的工作进程
- 异步非阻塞：假如一个请求接收，分析，读取数据。然后发现IO阻塞他不会继续等待的而是把cpu让出去。不需要创建线程，每个请求占用的内存也很少，没有上下文切换，事件处理非常的轻量级。并发数再多也不会导致无谓的资源浪费（上下文切换）。更多的并发数，只是会占用更多的内存而已。 我之前有对连接数进行过测试，在 24G 内存的机器上，处理的并发请求数达到过 200 万。现在的网络服务器基本都采用这种方式，这也是nginx性能高效的主要原因。



> 上面那篇文章还有好多，但是搬运也可能搬不好所以这里就不继续贴了
