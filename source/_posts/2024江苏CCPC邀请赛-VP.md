---
title: 2024江苏CCPC邀请赛-VP
date: 2024-08-14 19:46:31
tags: [ccpc]
categories: 
	- 比赛
	- 模拟赛
	- ccpc邀请赛
---

## 江苏CCPC-VP

## F. Download Speed Monitor

[Problem - F - Codeforces](https://codeforces.com/gym/105161/problem/F)

### 题意：

您正在从云存储下载某些内容，但下载速度却令人大失所望。盯着下载速度监视器会让你感觉舒服一些。

下载速度监视器的工作原理如下：从 $$(k-1)$$$ (秒)开始，下载速度监视器将始终显示 "正在加载"，但从 $$$k$$ (秒)开始，监视器将显示从 $$(i-k+1)$$$$ (秒)到 $$$$i$$(秒)再到 $$i$$ (秒)的平均下载速度。不过，由于监视器的尺寸较小，当平均下载速度大于或等于 $$1024$$ KiBps 时，下载速度监视器将以 MiBps 为单位显示结果。

您的下载任务将持续 $$n$$ 秒。考虑到平均速度的计算间隔为 $$k$$ ，您想知道从 $k$$$ (秒)到 $$$$n$$ (秒)，监控器上将显示什么。

Note: $$1$$$ MiBps $$$=1024$$$$ KiBps$$​.

### 题解：

签到题，双指针扫一下即可

```cpp
void slove() {
	cin>>n>>k;
	for(int i=1;i<=n;i++){
		cin>>a[i];
	}

	ll sum  = 0;
	for(int i=1,j=1;i<=n;i++)
	{
		sum += a[i];
		if(i - j >= k) sum -= a[j ++];
		if(i - j + 1 < k) continue;
		// cout<<i<< ' '<<j<< ' ' <<sum<<endl;
		double spd = double(1.0 * sum / k);
		string ad = "KiBps";
		if(spd >= 1024) spd /= 1024, ad = "MiBps";
		printf("%.6lf", spd);
		cout<<' '<<ad<<endl;
	}
}
```

## G. Download Time Monitor

[Problem - G - Codeforces](https://codeforces.com/gym/105161/problem/G)

### 题意：

您正在从云存储中下载某些内容，但下载速度却令人大失所望。您觉得只监控下载速度不足以缓解您的焦虑，于是您开始计算下载时间。

您使用的网络带宽为 $$B$$ MiBps，现在有两个大文件需要下载。第一个文件开始下载的时间为 $t_1$ 秒，大小为 $$a_1$$ MiB，而第二个文件开始下载的时间为 $$t_2$$ 秒，大小为 $$a_2$$ MiB。由于使用了最先进的拥塞控制算法，在任何给定时间内只有一个文件在下载时，下载速率将保持在 $$B$$ MiBps。当有两个文件正在下载时，两个文件的下载速率将保持恒定在 $$\frac{B}{2}$$ MiBps。当一个文件开始或完成下载时，所有文件的下载速率将立即调整到目标值。

现在，您想知道两个文件完成下载各需要多长时间。

### 题解：

出题人真是菩萨心肠，数据均小于1e5，也许是诱导模拟的track：）

这里我认为分类讨论写着更快，所以我分类讨论，其中反复交换两边的值简化代码。

注意是下载花费时间而不是总时间

这题浮点运算常数较大，开O2和解同步会比较好

```cpp
#pragma GCC optimize(2)
#define FIO std::ios::sync_with_stdio(false);std::cin.tie(0);
```

```cpp
void slove() {
	int B,t1,t2,a1,a2;
	cin>>B>>t1>>a1>>t2>>a2;

	int f = 0;

	if(t1 > t2){
		swap(t1,t2);swap(a1,a2);
		f=1;
	}

	double d = t2-t1;
	double a,b;

	if(d * B > a1){
		a = 1.0 *a1 / B;
		b = 1.0 *a2 / B;
		d=0;
	}else {
		a1 -= d * B;
		if(a1 > a2) f^= 1,swap(a1,a2);
		// cout<<a1<< ' '<<a2<<endl;
		a = 1.0 *a1 / (1.0 *B/2);
		b = 1.0 *a1 / (1.0 *B/2) + 1.0 *(a2 - a1) /B;
	}

	if(!f) printf("%.9lf %.9lf\n",a + d, b);
	else printf("%.9lf %.9lf\n",b + d,a);
}
```

## K. Number Deletion Game

[Problem - K - Codeforces](https://codeforces.com/gym/105161/problem/K)

### 题意：

爱丽丝和鲍勃正在玩一个数字删除游戏。

一开始，有 $$n$$$ 个整数 $$$a_ 1,a _ 2, \ldots ,a _n$$ 。爱丽丝和鲍勃轮流删除数字，爱丽丝先删除。每个人都可以删除**大的数字**$$x$$$ ，并选择一个比 $$$x$$**小的非负整数 $$y$$$ ，然后在数字 $$$1,2,\ldots, y$$$ 中各加一个。特别是可以选择 $$$y=0$$ ，在这种情况下不会添加任何数字。也就是说，删除 $$1$$ 时，不能添加任何数字。删除最后一个数字的人获胜。

双方都使用最优策略。确定是爱丽丝还是鲍勃获胜。

### 题解：

注意到最大值总是减少的，实际上我们是在判断最大值减少次数的奇偶性，对于一个数x而言，它可以减小最多x次。

另外题目中给出只能对最大值进行操作，即对其最大数的数量的奇偶判断。可以注意到对于当前操作的最大值我们归纳假定对于当前的最大值为奇数个时先手胜（因为显然为最大值为1是是先手胜的）。

​	那么对于最大值取$$1 \to k$$成立，试证明对k+1成立，分类讨论

	1. k+1有奇数x个，无论Bob如何操作，我们都能控制次大值的数量，即此时Alice始终控制次大值奇偶为偶
	1. k+1为偶数个，同理，Bob胜

coding

```CPP

void slove() {
	cin>>n;
	for(int i=1;i<=n;i++) cin>>a[i];

	sort(a+1,a+1+n);
	int num = -1;
	for(int i=n;i;i--)
		if(a[i] != a[n]) {
			num = n-i;
			break;
		}
	if(num == -1) num = n;
	// cout<<num<<endl;
	if(num&1) puts("Alice");
	else puts("Bob");
}
```

## I. Integer Reaction

[Problem - I - Codeforces](https://codeforces.com/gym/105161/problem/I)

###　题意：

有一个由 $$n$$ 个整数组成的序列，从左到右编号为 $$1$$ 到 $$n$$ 。这些整数有两种颜色，分别是 $$0$$ 和 $$1$$ ，每个整数正好有一种颜色。这些整数按照从 $$1$$到 $$n$$ 的编号顺序进入多集合 $$S_1$$ 。

每当一个新的整数 $$x$$ 进入 $$S_1$$ 时，你必须在 $$S_1$$ 中选择一个颜色与 $$x$$ 不同的整数 $$y$$ 与 $$x$$ 发生反应，使得 $$x$$ 和 $$y$$ 消失，反应产物 $$x+y$$ 插入另一个集合 $$S_2$$ 中。如果不存在这样的 $$y$$ ，则不会发生反应，只有 $$x$$ 会插入 $$S_1$$ 。

给定整数序列和每个整数的颜色，求处理最后一个元素后 $$S_2$$​ 中最小元素的最大可能值。

### 题解

很简单，二分答案贪心维护$$S_1$$即可

```cpp
int ck(int x){
	// cout<<x<<endl;
	multiset<int> ms;
	int c = -1;
	for(int i=1;i<=n;i++)
	{
		if(c==-1){
			ms.insert(a[i].x);
			c = a[i].y;
		}else {
			if(c == a[i].y){
				ms.insert(a[i].x);
			}else if(ms.size()){
				// cout<<x-a[i].y<<endl;
				auto it = lower_bound(all(ms), x - a[i].x);
				if(it == ms.end()) return 0;
				// if(x==7)cout<<*it<<endl;
				ms.erase(it);
			}else {
				ms.insert(a[i].x);
				c = a[i].y;
			}
		}
	}
	return 1;
};

void slove() {
	scanf("%d",&n);
	for(int i = 1; i <= n; i++)
		scanf("%d",&a[i].x);
	for(int i = 1; i <= n; i++)
		scanf("%d",&a[i].y);

	int l = 1,r = INF;
	while(l<r){
		int mid = l+r+1 >> 1;
		if(ck(mid)) l = mid;
		else r = mid - 1;
	}

	printf("%d\n",l);
}
```

