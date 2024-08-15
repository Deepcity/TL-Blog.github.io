---
title: 2024河南CCPC邀请赛-VP
date: 2024-08-14 19:46:31
tags: [ccpc]
categories: 
	- 比赛
	- 模拟赛
	- ccpc邀请赛
---

## 河南CCPC-VP

[Dashboard - 2024 National Invitational of CCPC (Zhengzhou), 2024 CCPC Henan Provincial Collegiate Programming Contest - Codeforces](https://codeforces.com/gym/105158)

## J. [排列与合数](https://codeforces.com/gym/105158/problem/J)

<!--more-->

### 题意：

​	小A在2023年河南省CCPC大学生程序设计竞赛的赛场上遇到了一道名为“排列与质数”的题目。 与大多数选手一样，小A并没能在赛场上解决这个棘手的题目。比赛结束后，小A想到了一个与之相关 的题目：排列与合数，可是小A仍然没有能力解决。这个名为“排列与合数”的题目是这样的： 给定一个有且仅有5 位，且各个数位互不相同的十进制正整数n。你可以重新排列n的各个数位， 但需要保证重新排列得到的整数n′ 没有前导零。请问重新排列数位得到的n′ 能否为合数？若能为合数， 请求出一个满足条件的n′。 例如，当n=12345 时，任意排列得到的n′ 均是合数，因此可以任意取n′。当n=13579时，可以 重新排列数位得到合数n′=97531=7×13933。 一个正整数是合数，当且仅当它可以分解为两个不小于2的整数的乘积。 现在，小A带着他的题目来到赛场上求助。你能帮助小A解决这个题目吗？

### 题解：

​	签到，有多种解法，比较快的是next_permutation()与埃氏筛，如下，如果你有更快的欢迎提出

```cpp
void slove() {
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		string s;cin>>s;
		sort(all(s));
		bool flag= 0;
		do{
			int t = 0;
			for(int i=0;i<5;i++)
				t = t * 10 + (s[i] - '0');
			if(t <=10000) continue;
			int f = 1;
			for(int i=2;i*i<=t;i++)
				if(t % i == 0){
					f=0;break;
				}
			if(!f) {cout<<t<<endl;flag =1;break;}
		}while(next_permutation(all(s)));
		if(!flag) cout<<-1<<endl;
	}
}
```

## B.[扫雷 1](https://codeforces.com/gym/105158/problem/B)

### 题意：

​	 T0xel 喜欢玩扫雷，但是他玩的扫雷游戏有名为“地雷探测器”的特殊道具。 具体来说，T0xel 会进行n轮扫雷。每轮扫雷开始之前，T0xel会获得1枚扫雷币。扫雷币在每轮扫 雷结束后不会回收，可以保留至下一轮扫雷。T0xel知道，在第i轮（1≤i≤n）扫雷中，花费ci 枚扫 雷币可以购买一个地雷探测器，清除地图中的一个雷。地雷探测器在一轮扫雷中可以购买任意次。 现在T0xel 想知道，在这n轮扫雷中最多能购买多少个地雷探测器呢？

### 题解

注意到最多只会买n个地雷，并在遇到最小当前最小价格时，应尽可能的买，所以只需要维护一下当前未遍历到的最小值即可。

```cpp
void slove() {
	cin>>n;
	for(int i=1;i<=n;i++){
		cin>>a[i].x;
		c[i] = a[i].x;
		a[i].y = i;
	}

	sort(a+1,a+1+n);

	int flag = 0;
	int now = 0, ans = 0;
	for(int i=1;i<=n;i++){
		now ++;
		while(a[flag].y < i) flag++;
		if(a[flag].y == i){
			ans += now / c[i];
			now = now % c[i];
		}
	}

	cout<<ans<<endl;
}
```

## F.[优秀字符串](https://codeforces.com/gym/105158/problem/F)

### 题意：

小A认为，一个字符串S是优秀字符串，当且仅当：

- S 的长度|S|恰好为5
- S 的第三个字符与第五个字符相同
- S 的前四个字符互不相同。 

例如henan 是优秀字符串，但query、problem、queue 不是，因为： • query 的第三个字符为e，而第五个字符为y； • problem 的长度不为 5； • queue 的前四个字符中u出现了两次。 现在，小A有n个仅包含英文字母与数字的字符串S1,S2,...,Sn，请你帮小A求出这些字符串中 优秀字符串的数量

### 题解

签到，不做阐述

```cpp
void slove() {
	cin>>n;int cnt = 0;
	for(int i=1;i<=n;i++){
	string s;cin>>s;
		if(s.size()!=5){
			continue;
		}else {
			set<char> S;
			for(int i=0;i<4;i++)S.insert(s[i]);

			if(S.size()!=4){
			continue;
			}else {
				if(s[2] != s[4]){
					continue;
				}else {
					cnt ++;
				}
			}
		}
	}
	cout<<cnt<<endl;
}
```

## M. [有效算法](https://codeforces.com/gym/105158/problem/M)

### 题意：

给出长度为n的正整数序列{an}和{bn}。对于每个ai（1≤i≤n），进行恰好一次以下操作：

- 将ai 变成满足|ai−x|≤k×bi 的任意整数x。 

请你求出最小的非负整数k，使得存在至少一种方法使得操作后序列{an}所有数都相等。

### 题解：

分情况讨论绝对值，即可得出以下两个不等式组
$$
a_i \le x \le a_i + k * b_i\\
a_i - k * b_i \le x \le a_i
$$
考虑整个a数组最后有可能变为的一致数是什么，显然这个数的绝对值一定是小于a数组中极值的。我们设mx为a数组中最大的数，mn为a数组中最小的数

考虑暴力一下，枚举的mn到mx为x，发现
$$
ans = max_{x=mn}^{mx}\{max_{i=1}^{n}\{ceil(abs(a_i - x)/b_i)\}\}
$$
但这样做有1e9*3e5的时间复杂度，考虑优化，似乎不具备什么特性。

转换一下思维，注意到对于k答案是具有单调性的，并且对不同大小的k，问题变成了区间覆盖问题，即，能否找到一个最小的k值，使得被覆盖的点至少有一个权值为n（被覆盖n次）。

如果我们二分k值，应该如何找到一个至多O(nlogn)时间复杂度以内的算法使得能找出是否存在这样的点。

我们可以在O(n)的时间复杂度内求出覆盖的区间左右端点，离散前缀和就能做到找出权值为n的点。

注意可能有极端情况$$a_i$$​均相等

神秘的是，这题及其卡常，O(nlognlog(1e9))居然常数稍微大一点就过不了，不能用map反向映射一遍。可能是cf编译器不一样？除了这一点还是很好的。如果你tle6了不要纠结。。

下面是code

```cpp
int check(int x){
	L.clear(),R.clear(),ves.clear();
	for(int i=1;i<=n;i++)
	{
		L.push_back(a[i].x - x * a[i].y);
		R.push_back(a[i].x + x * a[i].y);
	}
	// cout<<mx<<' '<<mn<<endl;
	int l = mn, r = mx;
	for(int i =0; i < n; i++){
		// cout<< L[i]<<endl;
		l = max(L[i],l);
		r = min(r,R[i]);
	}
	// cout<<x<< ' ' <<l<< ' ' <<r<<endl;
	if(l<=r) return 1;
	else return 0;
}

void slove() {
	cin>>n;
	for(int i=1;i<=n;i++){
		cin>>a[i].x;
		mx=max(a[i].x,mx);
		mn=min(a[i].x,mn);
	}
	for(int i=1;i<=n;i++){
		cin>>a[i].y;
	}

	// for(int i=1;i<=n;i++) cout<<a[i].x<<'-'<<a[i].y<<endl;

	ll l = 0, r = mx-mn;
	while(l<r){
		int mid = l+r>>1;
		// cout<<mid<<endl;
		if(check(mid)) r = mid;
		else l = mid + 1;
	}
	cout<<r<<endl;
}

```

## H.[随机栈](https://codeforces.com/gym/105158/problem/H)

### 题意：

​	Toxel 获得了一个随机的 “栈”。这个栈可被视为一个 .多 .重 .集 S，从一个非空的随机栈 S 中取出一个 元素时，有可能从中取出任何一个元素，其中每个元素被取出的概率是相等的。取出该元素后，该元素会 从集合中删除。以{1,2,2} 为例，有 1 3 的概率取出1，使得集合变为{2,2}，有 2 3 的概率取出2，使得集 合变为{1,2}。每次取出元素的事件相互独立。 Toxel 正在对这个集合做一些操作。集合初始时为空，它总共进行了2n次操作，其中n次操作为插 入，n次操作为取出。现在，Toxel告诉了你它操作的顺序以及每次插入的数，且保证每次取出时，集合 非空。Toxel 想知道，如果把每次取出的数排成一个序列，那么这个序列递增的概率是多少？这里，递增 的严格定义是：取出数列的每一项（除最后一项）.小.于.等 .于它的后一项。 由于答案可能不是整数，为了方便计算，你只需要求出这个值对998244353取模的结果。

### 题解：

题目保证了插入n次，也就是说Toxel取出数的数列在数值的序上是固定的，处理一下数据，维护一下当前要取的数值就好。

code

```cpp
ll qmi(ll a,ll b,ll mod = MOD){
	ll res = 1;
	for(;b;b>>=1){
		if(b&1) res = res * a % mod;
		a = a * a % mod;
	}
	return res;
}

ll inv(ll x){return qmi(x,MOD-2);}

void init(){}

void slove() {
	cin>>n;
	vector<int> a,ords;
	for(int i=1;i<=(n<<1);i++){
		int op;
		cin>>op;
		if(~op) a.pb(op);
		ords.pb(op);
	}
	sort(all(a));

	int flag= 0;
	int ans = 1;
	for(auto op : ords){
		if(op==-1){
			// cout<<cnt[a[flag]]<< ' '<<sum<<endl;
			ans *= cnt[a[flag]] * inv(sum) % MOD;
			ans %= MOD;
			cnt[a[flag]]--;
			flag++;
			sum--;
		}else {
			cnt[op] ++;sum ++;
		}
	}

	cout<<ans % MOD<<endl;
}
```

待续。。。。笔者吃饭去了

## L. [Toxel 与 PCPC II](https://codeforces.com/gym/105158/problem/L)

### 题面：

​	 Toxel 正在参加 PCPC（Pokémon Center Programming Contest）比赛。它写的一段代码中有不少 bug，正在调试。这份代码总共有n行，而且经验丰富的Toxel已经知道了其中m行代码有bug，并锁 定了这m行的具体位置。但是Toxel还需要进行一些调试以了解错误的具体细节并修复它们。 Toxel 会进行多次调试。每次调试时，Toxel 可以任选一个 i，使得程序从第 1 行开始，顺序运行完 第i行后退出。Toxel 可以通过这i行代码运行的一些输出结果来进行debug。运行这i行代码总共需要 i 秒。接下来，Toxel 会一次性地debug这i行代码，并修复所有这i行中的所有bug。bug数量越多，修 复所需的时间也越多。设这i行代码中现存的bug数量为x，那么Toxel需要x4 秒来debug并完成修 复。修复后，这i行代码中将不再存在任何bug。 PCPC 的赛场争分夺秒。请你帮Toxel 计算一下，它最短需要多少秒才能完成debug，修复整个代 码中的所有漏洞？

### 题解：

考虑对任意的一段前缀，增添一个bug，我们假设原最后一个bug与新增的bug之间有距离为d = j - i，则花费差为
$$
\Delta = (x+1)^4 + j  - (x^4 + i + j + 1) \\ 
\Delta = (x+1)^4 - x^4 - i - 1
$$
注意到$$i+1$$是常数，但对$$(x+1) ^4- x^4$$ 这个值可能随着我们前面先debug一段而减少。

让我们贪心的考虑，存在前缀，如果我们有$$(x+1)^4 - x^4 \le i+1$$ ，则我们一定会向后拓展吗？如果不扩展，我们首先损失了$$\Delta$$，我们在后续有相较于不存在j下标的bug的盈利点吗？显然多出一个bug不会使开销变小。。

实现时发现，对于前面的多向后扩展会使得后面本应扩展的数无法拓展

```cpp
for(int i=1;i<=m;i++){
		int s = 1;
		while(i < m && a[i] + 1 >= qmi((s+1),4) - qmi(s,4) ){
			s ++;
			i ++;
		}

		ans += (a[i] + qmi(s,4));
		cout<<i<<' ' <<ans<<nline;
	}
}
```

该代码在样例

```cpp
20 20
 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
```

计算答案为

```cpp
224
```

但实际确是221。这说明在相同情况下更加倾向于在后面多拓展因为根据上面的公式，实际上我们是在用$$(x+1)^4 - x^4$$去换$$i+1$$的成本量，相同情况下当然希望后面的扩展越多越好，此时$$i$$更大，相对于分开计算具有更优的贡献，因此我们更改为由最后的向前拓展。

但还是wa， 考虑贪心解法是不是错的离谱。于是注意到性质^4，简单计算可以发现，这个次方级别在不到20就会超出int上限。这说明枚举扩展点数并转移状态是可行的，于是进行dp

根据上面的公式，状态表示为
$$
f_i 前i个bug修改所需最小代价
$$
状态转移表示为
$$
f_i = max_{j=min\{1\}}^{j\{|qmi(j,4)- qmi(j-1,4) <= a[i-j] + 1 \&\& i-j+1 > 0\}}\{f_i,f_{i-j} + qmi(j,4) + a[i]\}
$$
暴力为何是错的，待证。但确实可以从上面的推导中看出一点np问题的影子。。。

coding。。。

```cpp
void slove() {
	cin>>n>>m;
	for(int i =1;i<=m;i++) cin>>a[i];

	memset(f,0x3f,sizeof f);
	f[0] = 0;

	int ans = 0;
	for(int i=1;i<=m;i++){
		f[i] = f[i-1] + a[i] + 1;
		// cout<<"#"<<i<< ' ' <<f[i]<<endl; 
		for(int j=2;qmi(j,4)- qmi(j-1,4) <= a[i-j+1] + 1 && i-j+1 > 0; j++)
		{

			f[i] = min(f[i-j] + qmi(j,4) + a[i],f[i]);
			// cout<<i<<' '<<j<< ' ' << f[i] <<endl;
		}
	}
	cout<<f[m]<<endl;
}
```

## K. [树上问题](https://codeforces.com/gym/105158/problem/K)

### 题意:

​	378QAQ 有一棵由 n 个节点组成的无根树，节点编号从1到n，每个节点有一个正整数点权。 378QAQ 认为一个节点是美丽节点，当且仅当该节点作为根时，对于除根节点以外的所有节点，其 点权都.不 .小 .于其父亲节点的点权的 1 2。 请你计算出有多少个节点是美丽节点。

### 题解：

​	若：
$$
a >= b/2
$$
​	则：
$$
2*a >= b \rightarrow \{a/2<=b<=2*a\}
$$
​	即，如果要满足一条边上的a,b顶点可以互换，两个数必须满足上述公式。若不满足，则大数在下，小数在上。

​	考虑更换顶点对点关系的影响：

1. 原根节点与新的根节点之间一定是父子关系
2. 更换根节点仅更改了原根到新根路径上的节点的父子关系。

​	于是我们想到，首先对边进行处理，对于每个不能颠倒的边，我们指出哪个顶点是父节点，对于能颠倒的边，我们指出哪个顶点是父节点。

​	显然我们的答案就是对于这些不能逆转的节点所组成的由父节点向子节点路径中起始点为根构成的子树大小。存在多个起始点答案为0。

​	一个解是，缩点成块，缩点后有且仅有一个根节点否则答案为0，答案就是根节点的子树大小。

coding

```cpp
int tarjan(int u){
    dfn[u] = low[u] = ++timestamp;
    stk[++top] = u, istk[u] = 1;
    for(int v: e[u]){
        if(!dfn[v]){
            tarjan(v);
            low[u] = min(low[u],low[v]);
        }else if(istk[v]){
            low[u] = min(low[u],dfn[v]);
        }
    }

    if(low[u] == dfn[u]){
        int p;
        scou++;
        do{
            p = stk[top--];
            istk[p] = 0;
            scnt[scou]++;
            id[p] = scou;
        }while(p!=u);
    }
}

void slove() {
	cin>>n;

    scou = top = timestamp = 0;
    for(int i=1;i<=n;i++) e[i].clear(),dfn[i] = low[i] = istk[i] = id[i] = scnt[i] = 0;

    for(int i=1;i<=n;i++) cin>>w[i];

    for(int i=1;i<=n-1;i++)
    {
        int a,b;cin>>a>>b;
        // cout<<a<<' '<<b<<endl;
        if(ceil(1.0*w[a]/2) <= w[b] && w[b] <= (w[a] * 2)){
            // cout<<a<< ' '<<b<<endl;
            e[a].pb(b);
            e[b].pb(a);
        }else {
            if(w[a] > w[b]) swap(a,b);
            e[a].pb(b);
            // cout<<"###"<<a<< ' '<< b<<endl;
        }
    }
    // cout<<nline;

    for(int i=1;i<=n;i++) {
        if(!dfn[i]){
            tarjan(i);
        }
    }

    for(int i=1;i<=scou;i++) din[i] = 0;

    multiset<ull> rcd;
    for(int i=1;i<=n;i++) {
        // cout<<"---"<<i<< ' ' <<e[i].size()<<endl;
        for(auto j: e[i]){
            int a = id[i], b = id[j];
            // cout<<i<<' ' << a<<endl;
            // cout<<j<<' ' << b<<endl;
            if(a != b && !rcd.count(a * 100000 + b)){
                rcd.insert(a * 100000+b);
                din[b] ++;
            }
        }
    }

    // for(int i=1;i<=n;i++){
    //     cout<<id[i]<<' ';
    // }
    // cout<<nline;

    // for(int i=1;i<=scou;i++){
    //     cout<<scnt[i]<<' '<<din[i]<<endl;
    // }
    // cout<<endl;

    int cnt =0,ans=0;
    for(int i=1;i<=scou;i++){
        if(!din[i] && !ans) ans = scnt[i];
        else if(!din[i] && ans){
            cout<<0<<endl;
            return ;
        }
    }
    cout<<ans<<endl;
    return ;
}
```



​	
