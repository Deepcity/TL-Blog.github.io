---
title: Linux网络设置
date: 2024-08-25 15:23:20
categories:
- 软件
- Linux
tags: [Linux, 网络]
---
# Linux的网络配置

在我反复鼓捣Linux的过程中，时常遇到Linux在网络配置上的问题，有个互联网笑话是“老钟是最擅长计算机网络的”。既然如此，我也有了一个想法——记录下我在Linux网络配置中所遭遇的坑。硬件主要集中于LinuxECS服务器与VMware虚拟机，软件版本则是以CentOS7，Ubuntu18.04为主。

## 基础网络命令

1. `ifconfig`最基本最常用的命令，用于查看本地网卡的信息，一般查看值为IP或者本地物理地址
2. `ping` 简单的测试网络丢包率，有时`curl`命令对网站的检测更为准确，因为两者对于代理的检测并不完全一致
3. `netstat`用于查看本地端口的状态信息，一般用于查看当前那些端口是开放的，那些端口是被某个特定应用监控的，一般搭配`grep`使用
4. `traceroute`针对特定目标地址的报文转发追踪，基于IP报文中的生存时间TTL实现，一般用于查看对特定网络服务是否联通，也可用于查看本机某个端口是否对外界开放，最经典的例子就是mail端口25，这个端口国内很少开放
5. `whois` 查看域名建立时间，有效期等，一般用于查看网站的归属信息以及ssl证书的有效期
6. `host`查看有关目的地址的信息，既可以通过IP查看域名，也可以通过域名查看IP
7. `ifdown` `ifup`,这两个命令用于对特定网卡的关启
8. `nmtui` 通过调用一个内置的网卡配置图形化界面配置网卡
9. `systemctl start/stop/enable/restart NetworkManager`这是一个特殊的通过systemctl控制网络服务以实现对网络进行开关的命令，常用于对`/etc/NetworkManager/NetworkManager.conf `该文件进行修改后的重新配置网络
10. `env | grep -E 'http_proxy|https_proxy'`这条命令比较特殊，一般用于查看当前的代理

<!--more-->

## 如何科学上网

### Windows平台

对于Windows平台，该平台有大量用户基础，并且生态良好，很容易找到代理上网的软件以及节点。（初接触者千万区分这两者，代理是代理也称机场，软件是软件）。两者一个收费最好，一个完全免费。

#### 推荐软件

V2ray、ClashForWindows等

### Linux平台

推荐clash-for-linux，一个个人自用的备份库

[Elegycloud/clash-for-linux-backup: 基于Clash Core 制作的Clash For Linux备份仓库 A Clash For Linux Backup Warehouse Based on Clash Core (github.com)](https://github.com/Elegycloud/clash-for-linux-backup)

根据md文档操作即可

注：

1. 这样的操作一般用于加速对github的下载
2. 配置其他的服务代理一般需要特别配置，因为很多服务实际上是通过守护进程来进行网络通信的

#### 常用的软件配置

##### yum

注意对yum文件的禁用修改方式，不要使用rm，而是加文件名后缀禁用文件

1. 全局修改

   修改/etc/yum.conf文件即可，在结尾添加你的本地代理网址

   ```
   proxy=http://IP:Port
   // 在本文中为
   proxy=http://127.0.0.1：7890
   ```

2. 对特定的仓库启用代理

   比如`CentOS-Base.repo`中有三个仓库：base、updates和extras。我们只想给base仓库设置代理，则只需要在对应的仓库后面添加一行，如下：

   ```
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   # 下一行为新增,效果为base仓库走代理
   proxy=http://ip:port
   # 下注释一行为base不走代理
   # proxy=_none_
   #released updates 
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://mirror.centos.org/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://mirror.centos.org/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-
   gpg/RPM-GPG-KEY-CentOS-7
   ```

##### docker

按照需求修改文件即可，详见

 [Docker.md](Docker安装与网络配置/article.html) 

### 无广告，个人自用机场

[Just My Socks官网](https://justmysocks.app/)

## 异常修复

**重装系统或恢复快照**

在花式乱搞后，虚拟机（服务器）的网络很容易会出现问题，比如，完全看不到虚拟机的网络选项，它有网卡但是却根本不获取ip！

![Error](https://s2.loli.net/2024/08/24/uRIr2VMaYvtkFhH.png)

这种情况一般都是 network这个网卡服务出现了问题

1. 找到错误

   查找虚拟机设置或者通过其他命令与文件查找到错误之后，通过`systemctl restart network`验证该服务是否出错

2. 排除关联

   通过

   ```
   systemctl stop NetworkManager
   systemctl disable NetworkManager
   systemctl start network.service
   ```

   成功启动服务，确定network服务启动失败与NetworkManager之间的关联

   但可以观察到，此时问题仍未解决，虚拟机仍无法上网，但是通过`ifconfig ens33`（ens33为网卡名）可以发现dhcp正常工作，更进一步的，我们从虚拟机ping主机也是可以ping通的

3. 尝试解决上网问题

   每种vm切换连接方式都解决不了问题，虚拟机仍无法上网，但可以发现，每次启动NetworkManager服务，就会导致虚拟机失去IP从而无法通信

可见NetworkManager与network之间发生了一些冲突，导致两个服务相互影响使得网络处于要么能分配ip但上不了网，要么根本没ip的局面。

![xe](https://s2.loli.net/2024/08/24/1K8cfE5atGrgeAY.png)

尝试查看错误信息可以发现其中奥秘。可以发现这一切都与一个名叫lo的本地环回接口有关，它被设置为不受管理。

在我询问chatgpt后，我得到了一种解决方式

**这种方式使得NetworkManager与network服务同时进行，可以访问网络，但无网络连接图标**（虚拟机环境）

具体方式为

1. 确保 `lo` 接口没有被 NetworkManager 管理。`/etc/NetworkManager/NetworkManager.conf` 文件中修改配置

   ```csharp
   [main]
   plugins=keyfile
   
   [keyfile]
   unmanaged-devices=interface-name:lo
   ```

2. 清除可能的网络配置缓存

   ```csharp
   sudo ip addr flush dev ens33
   sudo ip route flush table main
   ```

3. 重启服务

   ```csharp
   sudo systemctl restart NetworkManager
   sudo systemctl restart network.service
   ```

   

~~对于虚拟机的网络显示问题，目前还未找到好的解决方式，网络上博主表示直接重装系统即可。。。~~ 找到方法了， 直接挂起然后启动就好，很神奇，重启解决不了，挂起能解决。。。

> 网络管理器(NetworManager)是检测网络、自动连接网络的程序。无论是无线还是有线连接，它都可以令您轻松管理。对于无线网络,网络管理器优先连接已知的网络并可以自动切换到最可靠的无线网络。利用网络管理器的程序可以自由切换在线和离线模式。网络管理器会相对无线网络优先选择有线网络，支持 VPN。网络管理器最初由 Redhat 公司开发，现在由 GNOME 管理。
>
> NetworkManager由一个管理系统网络连接、并且将其状态通过D-BUS（是一个提供简单的应用程序 互相通讯的途径的自由软件项目，它是作为freedesktoporg项目的一部分来开发的。）进行报告的后台服务，以及一个允许用户管理网络连接的客户端程序。

## 参考文献

1. [为yum源配置代理 | linux (gitbook.io)](https://pshizhsysu.gitbook.io/linux/yum/wei-yum-yuan-pei-zhi-dai-li)
2. [解决centos7网卡启动失败解决（亲测有效！！） - 王飞侠 - 博客园 (cnblogs.com)](https://www.cnblogs.com/wangfx/p/17293472.html)