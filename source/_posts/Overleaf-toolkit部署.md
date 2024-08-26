---
title: Overleaf-toolkit部署
date: 2024-08-25 17:22:58
categories: 
- 软件
- latex
- package
tags: [latex, overleaf]
---

# Overleaf-toolkit部署

> 在写latex的过程中，第一个使用的在线latex编辑器就是overleaf，但是在使用过程中受到诸多限制，遂欲购买overleafpro，但发现其学生价也要500yuan/year，实在是财力不足，但overleaf是开源的，要花钱的只是服务器算力以及各种模板，于是就有了部署Overleaf-toolkit的想法

Overleaf是一个在线多用户协作的Latex编辑网站，其采用了如同markdown的“所见即所得”思想，采用及时更新编译的方式辅助编辑latex文件，同时通过项目结构约束文件的排布。是一个是十分学术且普遍应用的latex编辑网站。

<!--more-->

## 安装前置条件

1. 一台性能至少为2h/2g的服务器（cpu2h空闲且性能较强且剩余内存至少1.5g），个人使用也可以是一台虚拟机。
2. 并且至少保留10g的内存余量
3. 最好有个域名。

![Usage](https://s2.loli.net/2024/08/25/Gc4RtmuqsK6lQM8.png)

上图为容器运行后各个模块对资源的占用量，值得注意的是在本机中，配置好完整的texlatex与中文字集，对硬盘占用量达到了十个g，并且本机cpu为i7-11800h


## 安装过程

以下部分可能省略部分操作在参考资料中可以找到

### 安装overleaf-toolkit

这一步十分简单，按照overleafgithub官方库中给的教程即可，唯一需要注意的点如下：

1. 修改overleaf.rc文件需要关闭所有相关服务并bin/up重新生成docker，慎重第一次生成
2. 默认占用80端口，但是如果服务器配置了nginx并且希望方向代理，则需要更改overleaf.rc文件
   - `OVERLEAF_PORT` 指的是运行 Overleaf 的容器要选择曝露的端口，默认是 80 端口但如果有要使用 Nginx 反向代理的需求的话则需要自选一个端口（不常用的就行）；
   - `SIBLING_CONTAINERS_ENABLED` 这个配置真的巨坑，默认的话是 `true` 但如果没有购买官方的 Server Pro 的话请直接修改成 `false`，因为这个功能很大程度上依赖于官方提供给 Server Pro 用户的镜像，如果不调到 `false` 的话极有可能出现编译失败的情况；
3. 站点标题在variables.env中

## 一些基础配置

### 安装完整版texLive

[官方升级TexLive文档](https://github.com/overleaf/toolkit/blob/master/doc/ce-upgrading-texlive.md?spm=a2c6h.12873639.article-detail.14.5a336945UmSMix&file=ce-upgrading-texlive.md)

根据官方文档升级即可，注意version很有可能是不同的，overleaf官方每隔半年就会更新一次，因此，需要修改部分命令，如下

```
docker commit sharelatex sharelatex/sharelatex:[version]-with-texlive-full
echo [version]-with-texlive-full > config/version
```

### 新增中文字体

我们可以通过导入我们自己电脑中的中文字体做到这一步

1. 如何将自己电脑中的winfonts.tar.gz生成出来

   1. ![字体文件夹](https://s2.loli.net/2024/08/24/iWBOjIt7LwRvDSe.png)

      找到上图的这个文件夹，复制走想要的字体（绝大部分中文字体），然后压缩为winfonts.tar.gz(任意格式)

   2. ftp传到linux主机中就好

2. 继续参照参考资料2进入docker操作

   1. 安装必要的包

      ```
      docker exec -it share
      apt update
      apt install -y latex-cjk-all texlive-lang-chinese texlive-lang-english
      apt install -y xfonts-wqy
      ```

   2. 将所有字体文件移动到/usr/share/fonts/winfonts中并执行

      ```
      mkfontscale
      mkfontdir
      fc-cache -fv
      ```


## 修复XeLatex

```shell
docker exec -it sharelatex /bin/bash/
apt update
apt install -y texlive-xetex texlive-latex-extra texlive-science
```

### 刻录镜像

```
cat config/version # 该命令会得到[version]
docker commit sharelatex sharelatex/sharelatex:[version]-with-texlive-full
echo [version]-with-texlive-full > config/version
```

注意在编写中文文档时需要调整至XeLaTex，并且调用Ctex包

## 参考资料

1. [overleaf/toolkit (github.com)](https://github.com/overleaf/toolkit)
2. [Linux 快速搭建 Overleaf 5.0 附中文字体及完整 TexLive 安装教程（2024最新版）-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/1572606)
3. [搭建和使用overleaf服务器 | Tnnidm-Blog](https://www.tnnidm.com/build-and-use-overleaf-server/)
4. [toolkit/doc/ce-upgrading-texlive.md at master · overleaf/toolkit (github.com)](https://github.com/overleaf/toolkit/blob/master/doc/ce-upgrading-texlive.md?spm=a2c6h.12873639.article-detail.14.5a336945UmSMix&file=ce-upgrading-texlive.md)
5. [Docker部署ShareLaTeX并简单配置中文环境 | YXN's Blog (yxnchen.github.io)](https://yxnchen.github.io/technique/Docker部署ShareLaTeX并简单配置中文环境/#准备工作)
6. [CTeX 宏集手册 (ibiblio.org)](https://mirrors.ibiblio.org/CTAN/language/chinese/ctex/ctex.pdf)

