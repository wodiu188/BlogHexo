---
title: docker
date: 2022-02-05 11:08:09
tags:
      - 虚拟机
categories:
      - 靶场
      - docker
      - vulhub
description:
keywords:
---

# Docker



#### centos安装

一键安装

```bash
curl -sSL https://get.daocloud.io/docker | sh
```

卸载以前的docker

>**sudo** **yum remove** docker \
>   docker-client \
>   docker-client-latest \
>   docker-common \
>   docker-latest \
>   docker-latest-logrotate \
>   docker-logrotate \
>   docker-engine

安装仓库

>**sudo** **yum install** -y yum-utils \
> device-mapper-persistent-data \
> lvm2



设置仓库源

阿里的

>**sudo** yum-config-manager \
>--add-repo \
>http:**//**mirrors.aliyun.com**/**docker-ce**/**linux**/**centos**/**docker-ce.repo

清华大学的

>**sudo** yum-config-manager \
>--add-repo \
>https:**//**mirrors.tuna.tsinghua.edu.cn**/**docker-ce**/**linux**/**centos**/**docker-ce.repo



安装最新版本的 Docker Engine-Community 和 containerd

```bash
sudo yum install docker-ce docker-ce-cli containerd.io
```

启动docker

```bash
systemctl start docker
```





#### Windows安装

首先，docker和VMware不兼容，我们要相互切换就要使用命令开启或者关闭

>1.使用管理员身份打开powershell
>2.运行命令：
>bcdedit /set hypervisorlaunchtype off（关闭docker）
>
>
>
>如果想重新开启：
>bcdedit /set hypervisorlaunchtype auto(开启docker)

![image-20220205104855807](..\..\public\img\Java-1.png)

开启Hyper-V(用docker的话),如果要使用VMware的话就要关闭Hyper-V







### 常用命令

- run [指令] 镜像:版本 执行命令(绝对路径)
  - -i:允许对容器标准输入进行交互
  
  - -t:在新容器内指定一个终端或者伪终端
  
  - -d:后台运行
  
  - --name {名称}:用来指定容器的名称
  
  - -P:将容器的内部端口映射到外部主机上
  
  - >PORTS
    >0.0.0.0:32769->5000/tcp
    >
    >这里的意思是将主机的32769映射到docker的5000端口，如果我们想要访问可以直接访问主机的32769端口
  
  - -p [[绑定的ip:]主机端口]:[docker端口[/[tcp|udp]]:设置docker的映射端口与主机端口的映射
  
    - 如下
  
    - >127.0.0.1:1111:3000/tcp
      >
      >127.0.0.1:1111:3000(这样就是默认tcp)
      >
      >1111:3000/tcp(默认绑定的ip为0.0.0.0)
  
  - --network {网络名称}:将运行的程序加入到网络中
  
    - >首先使用docker network ls来查看所有的网络
  
  - -rm:退出时自动清除容器内部的文件系统
  
  - -h {hostname}:设置主机的hostname
  
  - --dns [dns的ip地址]:设置dns的IP地址
  
  - --dns-search:设置dns的搜索域
  
- ctrl+D或者exit可以退出镜像

- ps:查看运行的镜像

  - ![image-20220205125336762](..\..\public\img\docker-1.png)

- logs {容器的id或者容器的名称}:查看镜像的日志

  - ![image-20220205125731891](..\..\public\img\docker-2.png)

- stop {容器的id或者容器名称}:用来停止容器

- stats :docker的任务管理器

  - -a:查看所有的

- pull {镜像}:下载镜像

- start {容器名称或者容器id}:启动一个已经停止的容器

- restart {容器名称或者容器id}:用来重启容器

- attach {容器名称或者容器id}:用来进入后台的容器(如果退出的话容器也退出)

- exec {容器名称或者容器id}:用来进入后台的容器(退出的话容器继续后台运行)

- export {容器名称或者容器id} > 位置:用来导出容器

- import {容器位置} {容器名称:版本}:导入容器

  - ![image-20220205131048139](..\..\public\img\docker-3.png)

- remove -f {容器名称或者容器id}:删除容器

- port {容器名称或者容器id} [端口号]:查看容器的映射关系

  - > docker port adoring_stonebraker 5000
    >
    > 查看 adoring_stonebraker的5000的端口映射

- top {容器名称或者容器id}:容器的任务管理器

- inspect {容器名称或者容器id}:查看容器的底层信息

==容器部分==

- images:显示所有的容器
  - ![image-20220205134517220](..\..\public\img\docker-4.png)
  - 如果要运行aaa可以使用这条命令 docker run aaa:v1或者使用image id也可以 docker run {镜像id}

- search:搜索镜像
  - ![image-20220205135103800](..\..\public\img\docker-5.png)
- commit {容器的id} 镜像名称:版本号:更新镜像
  - -m:描述信息
  - -a:作者
  - 当我们对镜像做了修改后可以使用commit进行更新
  - ![image-20220205140450696](..\..\public\img\docker-6.png)

- tag image_id 镜像名称:镜像版本
  - ![image-20220205140646834](..\..\public\img\docker-7.png)
  - 使用上面的这种方法进行修改镜像
- ==build:创建一个docker镜像(暂时用不到就不做记录了)==



docker网络

- network 
  - create:创建网络
  - ls:列出网络
- login:登录
- logout:登出
- push {docker账户名/镜像名称:镜像版本}:推送镜像到docker库



### dockerfile

编辑一个dockerfile用来执行(类似于脚本文件)

文件内容如下

>FROM ubuntu
>
>RUN echo "123"

FROM代表导入的镜像

RUN后接运行的命令



==**注意**:Dockerfile 的指令每执行一次都会在 docker 上新建一层。所以过多无意义的层,会造成镜像膨胀过大。例如:==

所以可以使用\来链接多行

>FROM centos
>RUN **yum** -y **install** **wget** \
>  **&&** **wget** -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \
>  **&&** **tar** -xvf redis.tar.gz

下面是dockerfile的命令就像FROM和RUN一样

- COPY 源路径 目标路径:从上下文复制到容器指定路径
- ADD和COPY差不多不过,add在运行tar类型文件会自动解压到目标路径
- CMD:docker run的时候默认使用的命令
- ENTRYPOINT :和CMD差不多但是不会被覆盖,如果想要覆盖要使用 --entrypoint
- ENV :设置环境变量
- ARG :设置环境变量,仅在build过程中有效
- VOLUME :没看懂
- EXPOSE :设置默认的端口映射
- WORKDIR :设置默认的工作路径
- USER {用户名}[:用户组]:指定使用该用户运行
- HEALTHCHECK :用来设置docker来监控时调用的命令
- LABEL :设置元数据
- ONBUILD :没看懂



### Compose

Compose 是用于定义和运行多容器Docker应用程序的工具。借助于Compose，用户可以使用 YML 文件来配置应用程序需要的所有服务，然后使用==命令从 YML 文件配置中创建并启动所有服务==。简单来说，Docker-compose可以非常方便地创建比较复杂的容器。

### VULHUB

项目都存储在GitHub上面，搜索相应的并且下载下来只需要下载相应的文件即可，然后运行Docker-compose即可

到该目录下面运行cmd命令，然后使用docker-compose up -d命令来拉取并运行

![image-20220205165638767](..\..\public\img\docker-8.png)



