---
title: Anaconda简明介绍
date: 2024-08-31 16:53:55
categories:
- 软件
- 环境
- Anaconda
tags: [Anaconda, Python]
---

# Anaconda简明介绍

已然是不知道从何处了解到的anaconda了，但它默认成为了我的python编译器指定选项，于是今天来了解一下Anaconda是什么，它是干什么的。

## 包管理器

Anaconda（[官方网站](https://www.anaconda.com/download/#macos)）就是可以便捷获取包且对包能够进行管理，同时对环境可以统一管理的发行版本。Anaconda包含了conda、Python在内的超过180个科学包及其依赖项。

<!--more-->

### 特点

1. anaconda是一款开源软件
2. 其安装过程非常简单
3. 其能够以很好的性能解释R语言与Python语言
4. 其拥有丰富且免费的社区支持

### 内容

1. conda包
2. 环境管理器
3. 1000+开源库

### 与其他包管理器的区别

#### conda

conda是包及其依赖项和环境的管理工具。

▪ 适用语言：Python, R, Ruby, Lua, Scala, Java, JavaScript, C/C++, FORTRAN。

▪ 适用平台：Windows, macOS, Linux

▪ 用途：

① 快速安装、运行和升级包及其依赖项。

② 在计算机中便捷地创建、保存、加载和切换环境。

> 如果你需要的包要求不同版本的Python，你无需切换到不同的环境，因为conda同样是一个**环境管理器**。仅需要几条命令，你可以创建一个完全独立的环境来运行不同的Python版本，同时继续在你常规的环境中使用你常用的Python版本。——[Conda官方网站](https://conda.io/docs/)

▪ conda为Python项目而创造，但可适用于上述的多种语言。

▪ conda包和环境管理器包含于Anaconda的所有版本当中。

#### pip

pip是用于安装和管理软件包的包管理器。

▪ pip编写语言：Python。

▪ Python中默认安装的版本：

① Python 2.7.9及后续版本：默认安装，命令为 ***pip\***

② Python 3.4及后续版本：默认安装，命令为 ***pip3\***

▪ pip名称的由来：pip采用的是**递归缩写**进行命名的。其名字被普遍认为来源于2处：

① “Pip installs Packages”（“pip安装包”）

② “Pip installs Python”（“pip安装Python”）

#### **virtualenv**

virtualenv是用于创建一个**独立的**Python环境的工具。

▪ 解决问题：

1. 当一个程序需要使用Python 2.7版本，而另一个程序需要使用Python 3.6版本，如何同时使用这两个程序？如果将所有程序都安装在系统下的默认路径，如：***/usr/lib/python2.7/site-packages\***，当不小心升级了本不该升级的程序时，将会对其他的程序造成影响。
2. 如果想要安装程序并在程序运行时对其库或库的版本进行修改，都会导致程序的中断。
3. 在共享主机时，无法在全局 ***site-packages\*** 目录中安装包。

▪ virtualenv将会为它自己的安装目录创建一个环境，这并**不与**其他virtualenv环境共享库；同时也可以**选择性**地不连接已安装的全局库。

#### pip与conda的比较

▪ pip：

① **不一定**会展示所需其他依赖包。

② 安装包时**或许**会直接忽略依赖项而安装，仅在结果中提示错误。

▪ conda：

① 列出所需其他依赖包。

② 安装包时自动安装其依赖项。

③ 可以便捷地在包的不同版本中自由切换。

**→ 环境管理**

▪ pip：维护多个环境难度较大。

▪ conda：比较方便地在不同环境之间进行切换，环境管理较为简单。

**→ 对系统自带Python的影响**

▪ pip：在系统自带Python中包的更新/回退版本/卸载将影响其他程序。

▪ conda：不会影响系统自带Python。

**→ 适用语言**

▪ pip：仅适用于Python。

▪ conda：适用于Python, R, Ruby, Lua, Scala, Java, JavaScript, C/C++, FORTRAN。

#### conda与pip、virtualenv的关系

▪ conda**结合**了pip和virtualenv的功能。

### 总结

它是一个开源的，多语言的，多平台的，依赖检查，版本独立且云端同步的包管理工具，用于保存切换不同的编译环境与第三方库。

## 使用说明

1. 通过通过anaconda中的python解释器中的pip安装的包同样也会被anaconda所管理

2. 可视化界面：在win上可以打开anaconda-nagvitive可视化界面，查看当前环境与包

3. 云同步，需要登录，在win可视化界面右上角可以连接到anaconda云端即可实现同步

4. canda channel的配置

   默认canda channel是default，但这个代码包不全，建议使用conda-forge channel，并严格设置优先使用conda-forge，因为这不同channel的包不完全兼容。

   在安装这个这个渠道的时候无论使用什么样的方式都是可以的，比如图形化又或者命令行。

   有关命令行的配置方法放在参考文献里了．

5. 我应该使用pip安装还是使用conda安装第三方包

   随意，在网络上两种建议都有，使用pip安装比较直接，而使用conda安装可以检查依赖，但据说这这个检查环境兼容性可能有问题，因此，我的建议是先尽量使用pip，出现依赖问题用conda install

6. 他是如何切换换第三方库的？应该如何调用不同的环境

   这里有几种方式

   1. 使用它自己的命令行方式通过命令更换当前所用的环境
   2. 通过图形界面或者命令找到每个环境对应的python.exe或其他语言编译可执行程序，通过调用这个程序获取不同的第三方库。

## 参考文献

1. [Anaconda介绍、安装及使用教程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/32925500)
2. [Anaconda channel 配置笔记 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/349081344)