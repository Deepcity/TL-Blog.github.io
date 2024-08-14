---
title: Miracl的配置ForCentos7
date: 2024-08-14 19:55:03
tags: [信息安全, 软件, Miracl]
categories: 
	- 软件
	- 经典库
	- 信息安全
---
Miracl is Multiprecision Integer and Rational Arithmetic Cryptographic Library – the MIRACL Crypto SDK – is a C software library that is widely regarded by developers as the gold standard open source SDK for elliptic curve cryptography (ECC).

Miracl 是多精度整数和有理数算术加密库（MIRACL Crypto SDK），是一个 C 软件库，被开发人员广泛视为椭圆曲线加密 (ECC) 的黄金标准开源 SDK。也可在c++环境下通过对c库的

```cpp
extern "C"
{
     #include "miracl.h"
}
```

实现兼容。

下面是对miracl在c++环境下部署的简单步骤

第一步、联网状态下通过该命令获取压缩包，也可离线通过ftp传输

`````shell
wget https://github.com/miracl/MIRACL/archive/master.zip
`````

第二步、创建一个文件夹用来存放解压文件

```shell
mkdir miracl
```

第三步、复制并解压

```shell
cp master.zip ./miracl
cd miracl/
unzip -j -aa -L master.zip
```

第四步、验证解压并运行linux64（若32位运行linux）

```shell
ls
bash linux64
ll | grep miracl.a
```

第五步、运行官方程序

```shell
./pk-demo
```

第六步、一般情况下、复制一下文件到你的源代码目录下

````shell
cp ../miracl/miracl.a miracl.a
cp ../miracl/miracl.h miracl.h
cp ../miracl/mirdef.h mirdef.h
````

完成，注意在linux部署十分简单，但在windows环境下按照网络上的教程会爆出各种各样奇怪的错误，读者若要尝试，建议多参考官方文档，准备好比linux环境下部署多耗费许多心神的准备（ps:做好了发个blog）笔者虽然也已经配好了，静态库如下，但在一些程序内还是会报错，个人觉得是静态库制作过程中少了一些源文件并未制作，如下：

[静态库文件](https://1drv.ms/f/s!Ap-enY7ckLANgoNMIQysXRVS8LdGyQ)