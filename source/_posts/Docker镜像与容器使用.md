---
title: Docker镜像与容器使用
date: 2024-08-25 17:16:26
categories:
- 软件
- Docker
tags: [Docker]
---

# Docker Image && container

## 基础命令

1. `docker stats` 该命令可以查看docker contianer对各种资源的占用量
2. `docker image ls -a` 查看image状况 -a表示所有的
3. `docker container ls -a` 查看container状况 -a表示查看所有的
4. `docker container start/stop [container_id]`启停对应的container
5. `docker container/image rm [container_id]/[image_id]`移除镜像或容器。
6. `docker commit sharelatex sharelatex:version_tag` 提交容器为镜像

<!--more-->

## Image与Container的关系

Container可以commit为Image

Image也可以run为Container

### 什么是docker image

Docker Image（Docker镜像）是用于创建Docker容器的只读模板。它包含了运行应用程序所需的所有内容，包括代码、依赖项、库、环境变量和配置文件。Docker镜像是容器的“蓝图”，容器是镜像的运行实例。

1. Docker Image 是只读的

2. Docker Image的存储是分层的

   ![DockerImages](https://s2.loli.net/2024/08/25/eZ5XUC1zKHLRfJh.png)

   对如图的1,2,4镜像，通过tag显然可以发现他们的顺序是2-3-4，即总共占用8g而非16g

### 什么是Docker Container

Docker Container（Docker容器）是一个独立运行的应用程序实例，它基于Docker镜像启动，并包含了运行应用所需的所有依赖、配置和环境。Docker容器是轻量级的、可移植的，它们在隔离的环境中运行，确保应用程序的行为在任何系统中都是一致的。

1. Container是共享主机的操作系统内核的（但是同样可以使用不同的操作系统）
2. Container之间是隔离的
3. Container是独立的，并不基于除主机以外的东西
4. Container是可以短时（瞬时）启动和关闭的

### 如何启动Container

#### 通过Docker源，网络下载启动容器

一般来讲，我们可以通过

```shell
docker run -d -p [HostIP]:[HostPort]:[DockerPort] --name [ContainerName] [SoftwareName]:[version]
```

启动一个特定的容器并将docker的某一个开放端口映射到主机上，其中-d使得容器的输出并不同步到主机上

#### 通过本地Image仓库启动容器

```
docker run -d -p [HostIP]:[HostPort]:[DockerPort] <image_name>:<tag>
```

### 如何将Container提交为Image

相当简单

直接调用一条命令即可`docker commit sharelatex sharelatex:version_tag`即可

