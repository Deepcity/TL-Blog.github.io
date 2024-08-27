---
title: Xiaobai93D-E
date: 2024-08-14 19:46:31
tags: [newcoder]
categories: 
- 比赛
- 日常赛
- newcoder
---
# 小白93

## [D-幻兽帕鲁_牛客小白月赛93 (nowcoder.com)](https://ac.nowcoder.com/acm/contest/82401/D)

<!--more-->

### 题意

在幻兽帕鲁中，不同的帕鲁能干不同的工作，现在我们要对帕鲁进行分类以便他们能够更好的进行压榨。  

你有 $$2^n$$ 只帕鲁，初始给每只帕鲁一个工号，并让帕鲁按 $$[0,2^n-1]$$ 工号的顺序排成一队。  

当我们对区间 $$[l,r]$$ 的帕鲁进行操作时，我们会对该区间的帕鲁按顺序进行临时编号 $$[0,r-l]$$ ，记 $$mid = \lfloor\frac{l + r}{2}\rfloor$$，我们将临时编号为偶数和奇数的帕鲁，分别按顺序置于区间 $$[l,mid]$$ 和 $$[mid + 1,r]$$ ，并递归对这两个区间进行上述操作，直到区间长度为 $1$ 。  

现在我们对 $$[0,2^n-1]$$ 的幻兽进行一次操作，然后给你 $m$ 次询问，每次询问 $x$​ 位置的帕鲁工号是多少？

### 题解

简单的d题，感觉过的人少都是被吓得不敢去想题意了。实际上很简单，思考一下这个递归排序的实质，比较奇偶相当于比较最后一位是否为0，0则前，非零则后，然后每次递归相当于逻辑右移一位。本质上是倒着的二进制字典序。

于是，对排名向序号的反向映射可以表示为有多少个数在他前面，我们对二进制从低位枚举，i位为1则表示有(1<<(n-i-1)个帕努在他前面。不断二进制填满这个名次就好。

````cpp
void slove() {
	cin>>n>>m;
	for(int i=1;i<=m;i++){
		ll x;
		cin>>x;
		ll ans = 0;
		while(x){
			for(int i = 0; i < n; i++){
				if(x < (1ll<<(n-i-1))) continue;
				x-= (1ll<<(n-i-1));
				ans += (1ll<<i);
			}
		}
		cout<<ans<<endl;
	}
}
````



## [E-奏绝_牛客小白月赛93 (nowcoder.com)](https://ac.nowcoder.com/acm/contest/82401/E)

### **题意：**

你拥有一个黑之章和白之章构成的序列，你可以用它进行演奏。  

对于一次演奏的区间，如果这个区间的两个端点一个为黑之章，一个为白之章，那么该次演奏将会产生该区间长度的影响值，否则该次演奏影响值为 0。  

区间长度定义为左端点到右端点的距离，比如 i 到 i + 1 的距离为 1。  

对于 m 次询问，你要求出对于每次询问的区间，你在其所有子区间演奏的影响值的和，结果对  998244353 取模。

[E-奏绝_牛客小白月赛93（重现赛）](https://ac.nowcoder.com/acm/contest/82821/E)

### **题解**

**不建议写这一题，非常不建议，我看题目写出三种cpp，没一个是跟题目意思一样的，特别是对长度得判定，不注意直接就是积**

奏绝，我直接肘击！

题目的意思是，对一段区间，两个端点不同，计算长度贡献值，否则为0，计算所有子区间贡献总和。我假题了半小时。有一个很版得做法是莫队，但官方题解似乎不是，这里贴出我写的莫队算法，还是比较简单的。

```cpp
struct Node
{
	int x, y, id;
};

bool cmp(const Node& x,const Node& y){
	if(x.x/ blk != y.x /blk) return x.x/blk < y.x/blk;
	else {
		if(x.x != y.x) return x.x < y.x;
		else {
			return x.y < y.y;
		}
	}
}

void move(int x,int c)
{
	sum1 += (s[x] == '1'? x: 0) * c;
	cnt1 += (s[x] == '1') * c;
	sum2 += (s[x] == '0'? x: 0) * c;
	cnt2 += (s[x] == '0') * c;
}

void updateL(int x,int c){
	if(s[x]=='1'){
		ans += (sum2 - x * cnt2) * c% MOD;
		ans = (ans + MOD) % MOD;
	}else if(s[x]=='0'){
		ans += (sum1 - x * cnt1) * c % MOD;
		ans = (ans + MOD) % MOD;
	}
	// cout<<"!"<<x<<' '<<ans<<endl;
}
void updateR(int x,int c){
	if(s[x] == '0'){
		ans += (x * cnt1 - sum1) * c % MOD;
		ans = (ans + MOD) % MOD;
	}else if(s[x] == '1'){
		ans += (x * cnt2 - sum2) * c % MOD;
		ans = (ans + MOD) % MOD;
	}
	// cout<<"#"<<x<<' '<<ans<<endl;
}
```