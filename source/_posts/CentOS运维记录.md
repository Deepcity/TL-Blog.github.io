---
title: CentOS运维记录
date: 2024-08-25 15:23:35
categories: 
- 软件
- Linux
tags: [Linux,CentOS]
---
# CentOS运维

记录我在CentOS中折腾的经验。

##  存储空间不足

在不断的使用CentOS虚拟机的过程中，docker对磁盘空间的庞大需求最终还是占满了我为学习准备的20g空间，因此，我对CentOS的磁盘开始了第一次折腾。

<!--more-->

### 名词解释

写在前面为一些缩写给出解释

1. LVM的基本组成
   1. 物理卷 (PV，Physical Volume)
      一个可供存储LVM的块设备. 如硬盘分区（MBR或GPT分区）、SAN 的硬盘、RAID 或 LUN，一个回环文件, 一个被内核映射的设备 (例如 dm-crypt)，它包含一个特殊的LVM头，它是 LVM 构建的实际硬件或存储系统。
   2. 卷组 (VG，Volume Group)
      卷组是对一个或多个物理卷的集合，并在设备文件系统中显示为 /dev/VG_NAME。
   3. 逻辑卷 (LV，Logical Volume)
      逻辑卷是可供系统使用的最终元设备，它们在卷组中创建和管理，由物理块组成，实际上就是一个虚拟分区，并显示为 /dev/VG_NAME/LV_NAME，通常在其上可以创建文件系统。
   4. 物理块 (PE，Physical Extends)
      一个卷组中最小的连续区域(默认为4 MiB)，多个物理块将被分配给一个逻辑卷。你可以把它看成物理卷的一部分，这部分可以被分配给一个逻辑卷。

1. xfs文件系统：一种自Centos7起使用的文件系统

### 常用命令阐述

1. `lsblk`该命令可以查看当前硬盘的分区状况
2. `fdisk`该命令可以启动分盘程序，常用`fdisk -l`查看磁盘情况
3. `pvdisplay`显示物理卷
4. `vgdisplay`显示虚拟卷组
5. `lvdisplay`显示逻辑卷
6. `df`显示文件系统的容量以及挂载点位，常用参数`-h`显示适当的大小
7. `pvcreate`创建物理分区
8. `vgextend`扩容vg空间
9. `lvextend` 扩容lv空间 `-L`指按照指定空间，`-l`指按照指定百分比
10. `xfs_growfs`增加 XFS 文件系统的大小，必须挂载 XFS 文件系统，并且底层设备上必须有可用空间。

### 虚拟机扩容

在Vmware虚拟机中，如果VM对硬盘预留了空间，那么可以直接二通过VMware进行扩容

![VMware](https://s2.loli.net/2024/08/25/JwhxWA1cV8niUu2.png)

但很多情况下，这个方法都是不起作用的，尤其是分配了多块硬盘的情况。

### LVM扩容

LVM是扩容时所使用的空间的格式，是Linux所特有的空间的处理方法。

1. 通过虚拟机或服务器提供商增加硬盘大小

2. 为当前硬盘新建分区，并格式化为lvm

   1. `lsblk`显示硬盘状态
   2. `fdisk [sdx]` 指定硬盘名调用fdisk系统
   3. 新建分区
   4. `t`指定`8e`lvm文件系统

3. 将新建硬盘加入vg

   1. 创建pv，`pvcreate /dev/[sdx]` 

   2. 合并到已有vg组

      1. `vgdisplay` 查看当前vg
      2. 对欲增加容量的vg使用`vgextend [VG Name] /dev/[sdx]`

   3. 扩展lv空间

      1. `lvdisplay`查看已有lv空间

      2. `df -h`查看对应挂载点空间

         > 如LV name是home，他的LV Path是/dev/centos/home。
         >
         > 假如我们想添加空间到/home中，可以在df -h的结果中看到其对应着/dev/mapper/centos-home

      3. `lvextend -l/L +xx%/+xxG /dev/centos/xxxxx`扩展空间

   4. `xfs_growfs`使新的空间可用

## 掉网络问题

见 [Linux网络设置.md](Linux网络设置/article.html) 中异常处理一章

## 经验

1. 多快照，尤其是当你修改/etc/目录下的一些配置文件，尤其是`/etc/fstab`该文件。
2. maintain模式下，小数字键盘是不起作用的，而且这时不会有提示告诉你有问题

## 参考文献

1. [Linux Centos系统 磁盘分区和文件系统管理 （深入理解）-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2424136)
1. [VMware虚拟机（centos7）容量不足调整（LVM） - 小小小光子 - 博客园 (cnblogs.com)](https://www.cnblogs.com/lab-zj/p/13474549.html)
1. [存储系列之 XFS文件系统简介 - orange-C - 博客园 (cnblogs.com)](https://www.cnblogs.com/orange-CC/p/12711078.html)
1. [Linux下的磁盘管理之LVM详解及lvm的常用磁盘操作命令_lvm命令-CSDN博客](https://blog.csdn.net/weixin_42915431/article/details/121881054)