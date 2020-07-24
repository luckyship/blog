---
title: Docker
tags:
  - 解决方案
copyright: true
comments: true
date: 2020-06-01 16:31:45
categories: 工具
photos:
---

Docker，是一款现在最流行的 软件容器平台，提供了软件运行时所依赖的环境。

## 物理机
硬件环境，真实的 计算机实体，包含了例如物理内存，硬盘等等硬件；

## 虚拟机:
在物理机上 模拟出一套硬件环境和操作系统，应用软件可以运行于其中，并且毫无感知，是一套隔离的完整环境。本质上，它只是物理机上的一份运行文件。

## 为什么需要虚拟机？

### 环境配置与迁移
在软件开发和运行中，环境依赖一直是一个很头疼的难题，比如你想运行 node 应用，那至少环境得安装 node 吧，而且不同版本，不同系统都会影响运行。解决的办法就是我们的包装包中直接包含运行环境的安装，让同一份环境可以快速复制到任意一台物理机上。

### 资源利用率与隔离
通过硬件模拟，并包含一套完整的操作系统，应用可以独立运行在虚拟机中，与外界隔离。并且可以在同一台物理机上，开启多个不同的虚拟机启动服务，即一台服务器，提供多套服务，且资源完全相互隔离，互不影响。不仅能更好提高资源利用率率，降低成本，而且也有利于服务的稳定性。

### 传统虚拟机的缺点

#### 资源占用大
由于虚拟机是模拟出一套完整系统，包含众多系统级别的文件和库，运行也需要占用一部分资源，单单启动一个空的虚拟机，可能就要占用 100+MB 的内存了。
 
#### 启动缓慢
同样是由于完整系统，在启动过程中就需要运行各种系统应用和步骤，也就是跟我们平时启动电脑一样的耗时。

#### 冗余步骤多
系统有许多内置的系统操作，例如用户登录，系统检查等等，有些场景其实我们要的只是一个隔离的环境，其实也就是说，虚拟机对部分需求痛点来说，其实是有点过重的。

## Linux 容器
Linux 中的一项虚拟化技术，称为 Linux 容器技术(LXC)。它在进程层面模拟出一套隔离的环境配置，但并没有模拟硬件和完整的操作系统。因此它完全规避了传统虚拟机的缺点，在启动速度，资源利用上远远优于虚拟机；

---
<!--more-->

## Docker

Docker 就是基于 Linux 容器的一种上层封装，提供了更为简单易用的 API 用于操作 Docker，属于一种`容器解决方案`。
基本概念: 在 Docker 中，有三个核心的概念:

### 镜像 (Image):

1. 从原理上说，镜像属于一种`root 文件系统`，包含了一些系统文件和环境配置等，可以将其理解成一套`最小操作系统`。为了让镜像轻量化和可移植，Docker 采用了`Union FS 的分层存储模式`。将文件系统分成一层一层的结构，逐步从底层往上层构建，每层文件都可以进行继承和定制。这里从前端的角度来理解:`镜像就类似于代码中的 class，可以通过继承与上层封装进行复用`。
2. 从外层系统看来，一个镜像就是一个 Image`二进制文件`，可以任意迁移，删除，添加；

### 容器 (Container):

1. 镜像是一份静态文件系统，无法进行运行时操作，就如class，如果我们不进行实例化时，便无法进行操作和使用。因此`容器可以理解成镜像的实例`，即`new 镜像()`，这样我们便可以创建、修改、操作容器；一旦创建后，就可以简单理解成一个轻量级的操作系统，可以在内部进行各种操作，例如运行 node 应用，拉取 git 等；
2. 基于镜像的分层结构，容器是`以镜像为基础底层`，在上面封装了一层`容器的存储层`；

- 存储空间的生命周期与容器一致；
- 该层存储层会随着容器的销毁而销毁；
- 尽量避免往容器层写入数据；

3. 容器中的数据的持久化管理主要由两种方式:

- 数据卷 (Volume): 一种可以在多个容器间共享的特殊目录，其处于容器外层，并不会随着容器销毁而删除；
- 挂载主机目录: 直接将一个主机目录挂载到容器中进行写入；

### 仓库 (Repository):

1. 为了便于镜像的使用，Docker 提供了类似于 git 的仓库机制，在仓库中包含着各种各样版本的镜像。官方服务是 Docker Hub；
2. 可以快速地从仓库中拉取各种类型的镜像，也可以基于某些镜像进行自定义，甚至发布到仓库供社区使用；

### 安装
- linux
> curl -sSL https://get.docker.com/ | sh或者wget -qO- https://get.docker.com/ | sh

- Mac
https://download.docker.com/mac/stable/Docker.dmg

- windows
https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe

### 第一个 docker 项目
接下来我们搭建一个能够托管静态文件的 Nginx 服务器，容器运行程序，而容器哪来的呢？容器是镜像创建出来的。那镜像又是哪来的呢？

镜像是通过一个 Dockerfile 打包来的，它非常像我们前端的package.json文件。

```
Dockerfile: 类似于“package.json”
 |
 V
Image: 类似于“Win7纯净版.rar”
 |
 V
Container: 一个完整操作系统
```

#### 创建文件
```
hello-docker
  |____index.html
  |____Dockerfile
```
- index.html
```html
<h1>Hello docker</h1>
```

- Dockerfile
```
FROM nginx

COPY ./index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

#### 打包镜像
```bash
cd hello-docker/ # 进入刚刚的目录
docker image build ./ -t hello-docker:1.0.0 # 打包镜像
```
此时遇到问题`Docker 安装后 报 Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running? `

```bash
systemctl daemon-reload
sudo service docker restart
sudo service docker status (should see active (running))
```
然后重新运行 `docker image build ./ -t hello-docker:1.0.0` 命令。这条命令的意思是基于路径./（当前路径）打包一个镜像，镜像的名字是hello-docker，版本号是1.0.0。该命令会自动寻找
Dockerfile来打包出一个镜像。

解读下Dockerfile的内容：
- FROM nginx：基于哪个镜像
- COPY ./index.html /usr/share/nginx/html/index.html：将宿主机中的./index.html文件复制进容器里的/usr/share/nginx/html/index.html
- EXPOSE 80：容器对外暴露80端口

> 注意！Docker 中的选项（Options）放的位置非常有讲究，docker —help image和docker image —help是完全不同的命令

#### 配置国内镜像源
在 Linux 环境下，我们可以通过修改 /etc/docker/daemon.json ( 如果文件不存在，你可以直接创建它 ) 这个 Docker 服务的配置文件达到效果。
```
{
    "registry-mirrors": [
        "https://registry.docker-cn.com"
    ]
}
```

然后重启 docker 让配置生效
```bash
sudo systemctl restart docker
```

通过 docker info 来查阅当前注册的镜像源列表，验证我们配置的镜像源是否生效
```bash
sudo docker info
```

#### 运行容器
```bash
docker container create -p 9000:80 hello-docker:1.0.0 # 根据镜像创建容器
docker container start xxx # xxx 为上一条命令运行得到的结果
```

然后在浏览器打开ip:9000，就能刚刚自己写的index.html内容。
