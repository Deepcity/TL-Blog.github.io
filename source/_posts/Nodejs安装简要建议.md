---
title: Nodejs安装简要建议
date: 2024-08-15 10:28:48
tags: [nodejs, gcc, linux]
categories: 
	- 软件
	- 经典库
	- 前端
	- node
---
## NodeJS

### 什么是Nodejs

[Node.js — Run JavaScript Everywhere](https://nodejs.org/)这是NodeJS的官网标题，很简单的概括，他就是在任何地方都可以运行javascript。

具体的讲，如下。。

>  Node.js 就是运行在服务端的 JavaScript。 Node.js 是一个基于 Chrome JavaScript 运行时建立的一个平台。 Node.js 是一个事件驱动 I/O 服务端 JavaScript 环境，基于 Google 的 V8 引擎，V8 引擎执行 Javascript 的速度非常快，性能非常好。

### 下载时一步步涉及到的包管理

nvm——Node Version Manager

fnm——Fast Node Manager

nvm 有一个致命的缺点，就是它的自动切换版本极其麻烦，而 fnm 就没这个问题。并且 Windows 上的 [nvm](https://github.com/nvm-sh/nvm) 与 macOS 上的 [nvm](https://github.com/coreybutler/nvm-windows) 实际上并不是同一个，Windows 的只是借了 nvm 的名称，API略有不同，构建两者的语言更是完全不相同。而 fnm 在三大系统上都是同一个，这保证了 API 的一致性。

下面以fnm为例

### fnm 下载

```
curl -fsSL https://fnm.vercel.app/install | bash
```

如果安装不成功，考虑是不是服务器连不上外网，通过其他渠道下载fnm软件包并解压到对应位置，在.bashrc中添加环境变量即可。下载链接如下：

[fnm.zip](https://objects.githubusercontent.com/github-production-release-asset-2e65be/166045424/4c0a9e2a-9b22-4ebe-b026-de5e78de9351?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=releaseassetproduction%2F20240529%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240529T083324Z&X-Amz-Expires=300&X-Amz-Signature=d5e13763b9750727bd45b014d12aa7e3a893ffe2758189710d8c11d874eb9e66&X-Amz-SignedHeaders=host&actor_id=91273161&key_id=0&repo_id=166045424&response-content-disposition=attachment%3B%20filename%3Dfnm-linux.zip&response-content-type=application%2Foctet-streamobjects.githubusercontent.com/github-production-release-asset-2e65be/166045424/4c0a9e2a-9b22-4ebe-b026-de5e78de9351?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=releaseassetproduction%2F20240529%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240529T083324Z&X-Amz-Expires=300&X-Amz-Signature=d5e13763b9750727bd45b014d12aa7e3a893ffe2758189710d8c11d874eb9e66&X-Amz-SignedHeaders=host&actor_id=91273161&key_id=0&repo_id=166045424&response-content-disposition=attachment%3B%20filename%3Dfnm-linux.zip&response-content-type=application%2Foctet-stream)

### NodeJS18版本以上的gcc&make版本要求

要求gcc必须版本8以上（较流行的版本为11），make版本4以上，并且在一些环境下需要更新GLIBC（只能通过源码安装，因为涉及Linux底层动态链接库），通过下载源代码包configure（注意参数）以及make，make install即可安装，注意，此步骤为高级操作，操作前请备份快照重要文件，可能导致库文件缺失引起的ssh无法连接，编译时长30min以上，make参数采用-j𝑛(通常为处理器数目两倍)可以加速。

以下是另一个博主的详细介绍，可以参考一下，笔者在更新GLIBC时也是参考的这篇blog

[OSError: /lib64/libm.so.6: version `GLIBC_2.27' not found (required by xxx.so) ——升级GLIBC并解决系统错误 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/559791450?spm=a2c6h.12873639.article-detail.7.713765faZuvSwh)

在make过程中通常涉及

```
ld-linux-x86-64.so.2\libc.so.6
```

两个软连接的更改，在更改时会中断make程序并导致系统异常，需要重新手动连接软连接（如果你make时出错并且系统无法ls的情况下）https://zhuanlan.zhihu.com/p/559791450?spm=a2c6h.12873639.article-detail.7.713765faZuvSwh)

最后记得设置环境变量，切勿随意删除系统gcc文件。

>  许多blog在configure中设置--profix=/usr，需要自定义的用户请注意，这样会导致文件混乱。个人常用（/usr/local/soft-name）

安装NodeJS详细步骤建议参考官方：[Node.js — Download Node.js® (nodejs.org)](https://nodejs.org/en/download/package-manager)

注意前面提到的依赖项，以及官网的curlpost请求不到可能是网络因素

### NodeJS验证

```
node -v
npm -v
```

两条指令均正常即说明程序成功安装
