---
title: Docker安装与网络配置
date: 2024-08-25 15:23:56
categories:
- 软件
- Docker
tags: [Docker]
---
# Docker

在部署服务器时，经常在各式各样的仓库中见到Docker部署这一方式，开始不以为然，后来发现它极高的普及率与及其方便的部署方式吸引了我的注意。

[docker](https://www.docker.com/)是一个开源的平台，主要用于开发、运输和运行应用程序。它通过使用容器（Containers）技术，使得应用程序能够在任何环境下以一致的方式运行，无论是在开发、测试，还是在生产环境中。

<!--more-->

可以说，只要你配置好了docker，所有知名的软件就都向你敞开了大门。下面，从我是用docker的角度出发，讲一下实用的docker概念与使用技巧。

1. docker与dockercompose

   Docker 和 Docker Compose 是两个密切相关的工具，但它们的功能和用途有所不同，通常一起使用来管理容器化的应用程序。

   Docker是一个基础平台，安装docker 通常讲指单纯安装这一组件，实际上，很多下载方式也都是这么做的，**但我并不推荐这种方式**，实际上就截至目前的体验，dockercompose的体验对新手是要好于docker的。

   dockercompose是docker的具体独立工具依赖 Docker，但它是一个独立的工具，需要单独安装。

2. 网络问题

   由于发展中的问题，对docker的下载时常是困难的，且下载完整且最新的docker很可能是“**有误导性**”的，因为，如果你挂了镜像而且即使更新了镜像，也无从得知下载的版本是否正确，能否运行所需的软件。

   这里还是推荐直接上机场或VPS直连通过官网方式不通过镜像来下载，虽然经济上可能有损耗，但总体来看，减少时间成本是多于增加的经济成本的。

   然而，**挂载了加速可能你也会发现**，docker的网络并不通过我们的代理，事实上，docker并不走“常见”的网络代理，在linux中，**在执行docker pull时，是由守护进程dockerd来执行。因此，代理需要配在dockerd的环境中。而这个环境，则是受systemd所管控，因此实际是systemd的配置。** 

## 安装最新Docker

若只想安装一个可用的源，截至2024/7/24，有一份教程在参考资料第6个。

### 科学上网

对于安装最新Docker，科学上网几乎是必备的，无论你使用的任何镜像（ps：除非是确保可信且不会过期的，包括国内部分知名互联网公司及大部分学校镜像都是无效或过期的）

比较知名的源有：Aliyun，清华，ustc中科大

参考文章[Linux网络设置](Linux网络设置/article.html)

### 通过yum安装

该命令可移除全部docker

```shell
sudo yum remove docker \
                docker-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-engine
```

1. 更新系统

   ```shell
   sudo yum update -y
   ```

2. 安装依赖包

   ```shell
   sudo yum install -y yum-utils \
       device-mapper-persistent-data \
       lvm2 
   ```

3. 安装

   ```shell
   yum install -y docker-ce docker-ce-cli containerd.io
   ```

4. 额外设置与验证

   ```shell
   sudo systemctl start docker
   sudo systemctl enable docker
   docker version
   ```

你可能已经发现了，即使通过以上方式下载了docker，也不一定是最新版，甚至相当可能是1.1x等超老版本，这是因为使用的yum镜像源的问题，国内很多镜像源都在许多年前“停止了维护”。但如果你的yum是国外源的话，很有可能没这个问题，也就不用看下面的内容了，查找最新版本请看docker官网（在参考资料中）

### 通过dnf进行安装

以下操作需要保证你的yum仓库状态正常，并且联通外网，如果原本`yum makecache`就无法成功，那么dnf也是救不了的

![[Supported platforms](https://docs.docker.com/desktop/install/linux-install/#supported-platforms)](https://s2.loli.net/2024/08/23/riq6ygcF9jKfEvA.png)

在docker官网里，我们可以查询到他所支持的Linux列表，而在Centos中，我们可以通过`cat /proc/version` `uname -a`,`cat /etc/os-release`分别查询到内核以及系统版本，而Centos7属于RHEL的克隆版本。因此尝试使用RHEL的安装方式，即官网描述的dnf新一代RPM库安装

1. 安装依赖

   ```shell
   yum install epel-release -y
   ```

2. 安装dnf

   ```shell
   yum install dnf -y
   ```

3. 配置dnf，与添加常用的第三方库

   ```shell
   dnf install 'dnf-command(config-manager)'
   dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
   dnf install pass
   dnf install gnome-shell-extension-appindicator # GNOME
   gnome-extensions enable appindicatorsupport@rgcjonas.gmail.com # GNOME
   sudo dnf install gnome-terminal # GNOME
   dnf install docker-ce docker-ce-cli containerd.io
   ```

4. 额外设置与验证

   ```shell
   sudo systemctl start docker
   sudo systemctl enable docker
   docker version
   ```

### AliyunECS

当服务器满足下图条件时，可通过扩展程序安装Docker

![Aliyun](https://s2.loli.net/2024/08/24/Eyqh8e6HoAnFVxr.png)

详见对参考资料中阿里云的官方文档

## Docker的网络代理

![docker与daemon在代理中的关系](https://i-blog.csdnimg.cn/blog_migrate/53472fd549666b7817c31f80e4f2413b.png)

![daemon与网络的关系](https://s2.loli.net/2024/08/24/vYtQrRgiSsyd14E.png)

事实上，daemon与client可以运行在两个主机上，在同一主机上运行时，真正与互联网进行交互的实际上是Daemon守护程序，也就是说，当我们普通的配置env进行代理的时候，我们配置的是Client与Daemon交互的代理，而配置Daemon后才能达到“端到端”的代理。

下面只阐述如何进行端到端的代理配置

在`/etc/systemd/system`该路径下寻找`docker.service.d/http-proxy.conf`,没有则新建

```conf
[Service]
Environment="HTTP_PROXY=http://IP:Port/"
Environment="HTTPS_PROXY=http://IP:Port/"
Environment="NO_PROXY=localhost,127.0.0.1,.example.com"
```

在这里，我是用的clash-for-linux的IP为127.0.0.1，Port为7890，可以通过 [Linux网络设置.md](../Linux网络设置.md)设置。 

最后重启他俩即可

```shell
systemctl daemon-reload
systemctl restart docker
```

### Dockers Container的网络代理

### 直接在容器内添加代理

这种方式如同在主机添加代理，步骤一致，但并不推荐，如果不是测试使用，最好还是保证容器的完整性。

> 但如果需要该容器处处可用，修改环境变量也是可行的

略

### 在Docker17版本以上

`Docker` 提供了一个**全局的配置**，**可以通过配置 `Docker` 客户端以自动将代理信息传递给容器**，从而让所有的容器内部都支持代理访问。

在**启动容器的用户的主目录中**创建或编辑文件 `~/.docker/config.json`

```json
{
 "proxies":
 {
   "default":
   {
     "httpProxy": "http://127.0.0.1:7890",
     "httpsProxy": "http://127.0.0.1:7890",
     "noProxy": "*.test.example.com,.example2.com,127.0.0.0/8"
   }
 }
}
```

然后重启docker两兄弟即可

```shell
systemctl daemon-reload
systemctl restart docker
```

## 参考资料

1. [Elegycloud/clash-for-linux-backup: 基于Clash Core 制作的Clash For Linux备份仓库 A Clash For Linux Backup Warehouse Based on Clash Core (github.com)](https://github.com/Elegycloud/clash-for-linux-backup)
1. [Install Docker Desktop on Linux | Docker Docs](https://docs.docker.com/desktop/install/linux-install/)
1. [Docker: Accelerated Container Application Development](https://www.docker.com/)
1. [如何在CentOS 7上安装DNF (linux-terminal.com)](https://cn.linux-terminal.com/?p=5180)
1. [安装Docker并使用_云服务器 ECS(ECS)-阿里云帮助中心 (aliyun.com)](https://help.aliyun.com/zh/ecs/use-cases/install-and-use-docker-on-a-linux-ecs-instance#298a8c6bdc193)
1. [使用国内源安装新版docker（2024.7.3） - navist2020 - 博客园 (cnblogs.com)](https://www.cnblogs.com/codenoob/p/18281992)
1. [快速设置 Docker 的三种网络代理配置_docker 代理-CSDN博客](https://blog.csdn.net/peng2hui1314/article/details/124267333)