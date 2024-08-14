---
title: RSA涉及算法与数论知识
date: 2024-08-14 19:51:58
tags: [信息安全, 数学]
categories:
	- 信息安全
	- 加密算法
	- 数学
---
**RSA** (**Rivest–Shamir–Adleman**) is a [public-key cryptosystem](https://en.wikipedia.org/wiki/Public-key_cryptography), one of the oldest widely used for secure data transmission.

RSA（Rivest–Shamir–Adleman）是一种公钥密码系统，是最古老且广泛用于安全数据传输的系统之一。它是一种非对称公钥-私钥密码系统。

## 基础数论知识纲要

传送门：[MH3210 - NTU - Number Theory - Studocu](https://www.studocu.com/sg/course/nanyang-technological-university/number-theory/3031934)

知乎blog：[基础数论学习笔记（1）- Divisibility 整除 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/635332658)

讲义：[NanYang Technological University MH1300ANDMH3210](https://1drv.ms/f/s!Ap-enY7ckLANgoNHotzq3SRTafp4pg) 

## 概念定义

**费马小定理**：选一个**素数p**，再选一个和p不成倍数关系的整数β，必然满足**β的p次幂**和**β**对p同余。公式如下。
$$
\beta^p \equiv \beta (modp)
$$
**二次剩余**： 取定 $$𝑎\perp 𝑝$$, 假若存在着 $x$使得
$$
x^2 \equiv a (modp)
$$

则称$$a$$是$$modp$$的特殊剩余，否则则是$$modp$$的二次非剩余

**欧拉准则**：元素β是**模奇素数p**的平方剩余的充要条件是，β的(p-1)/2次幂和1对p同余。即以下公式
$$
\beta ^ {(p-1)/2} \equiv 1 (mod p) | p \equiv 1 (mod2)
$$

> 欧拉准则证明：
> 设$$r^2 = \beta$$则有 $$r^{p-1} \equiv 1 (modp)$$,利用费马小定理即可得证。

**勒让德符号**：$$(\frac{a}{p})$$ 若$$a$$ 是$$modp$$的平方剩余 则 $$(\frac{a}{p}) = 1$$ ，若不是，则$$(\frac{a}{p})= -1$$ 若$$a$$和$$p$$是整除关系则$$(\frac{a}{p})=0$$ ,如下列公式
$$
(\frac{a}{p}) = 
\begin{cases}
1,\quad x^2 \equiv a(modp)\\
0, \quad a \equiv 0 (modp) \\
-1, \quad x^2 \not\equiv a(modp)
\end{cases}
\tag{1}
$$
Solovay-Strassen算法：若n是一个素数，那么勒让德符号$$(\frac{β}{n})\equiv β^{(n-1)/2}mod n$$。

> Strassen算法证明：根据欧拉准则**β的(n-1)/2次方和1对n不同余**，根据费马小定理，**β的n-1次方**和1对n同余，根据平方差公式，$$β^{n-1}-1=(β^{(n-1)/2}-1)(β^{(n-1)/2}+1)$$ 。由于$（β^{(n-1)/2}-1）$ 无法被n整除，所以$$（β^{(n-1)/2}+1）$$ 必然能被n整除，进而得到$$β^{(n-1)/2}\equiv-1  (mod n)$$。

**该命题的逆命题是不成立的**

**二次互反律**：$$\mathbb Z_p^\times\to\mathbb Z_2$$​ 

**Miller-Rabin算法**：

输入待测试大数$$n$$，对$$n-1$$不断地进行除$$2$$操作，直到得到一个**奇数t**。

于是这可以写成 $$n-1 = 2^s \cdot t$$ 。显然的，待测试大数$n$肯定是个奇数（废话，是偶数还测个锤子了），那么$$n-1$$肯定是个偶数，故$$s\not=0$$。

选择**随机种子$$a$$**，$$a＜n$$且与$$n$$互素。（这个也很容易做到，如果随便一下子就测出来不互素，那就不用测了）

先设$$m=t$$。计算 $$b \equiv a^t(modn)$$ ：

- 情况①：当 $$m=2^s \cdot t = n -1$$时，停机，输出“n是一个合数”。

- 情况②：当 $$b \equiv -1 (modn)$$ 时，停机，换一个随机种子a再次进行测试。

- 情况③：当$$b \equiv -1(modn)$$不成立时，重新设$b$为$$b²(mod n)$$，$$m$$为$$2m$$​；继续循环，直到得到情况①或情况②为止。

## 计算方法

**快速幂**：通过二进制的快速幂优化

**计算勒让德符号$$(\frac{\beta}{n})$$​** ：二次互反

**大整数除法中对小除数的优化**：移位计算小除数的商

**大整数除法中对大除数的优化**：二分查找对每次计算试商的优化