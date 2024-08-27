---
title: Xiaobai94
date: 2024-08-14 19:46:31
tags: [newcoder]
categories: 
- 比赛
- 日常赛
- newcoder
---

## [B-小苯的好数组_牛客小白月赛94 (nowcoder.com)](https://ac.nowcoder.com/acm/contest/82957/B)

<!--more-->

### 题意

大白熊给了小苯一个长度为 $n$ 的数组 $a$，这次他希望小苯从数组中选择一个**子序列**（下方备注有定义解释），满足这个子序列构成的数组是一个“好数组”。  

大白熊定义好数组是：如果一个数组按升序排序后和原来**不完全相同**，则其是一个好数组。例如 $[3,2,2]$ 升序排序后是 $[2, 2, 3]$，和原来不完全相同，因此**是**一个好数组，而 $[1,2,2]$ **不是**一个好数组。

  

小苯想知道，如果想要使得选择的子序列构成一个“好数组”，最长可以选多长的子序列？

### 题解

误入签到，答案只有0和n两种，很好判断

```cpp
void slove() {
	vector<int> ve;
	cin>>n;
	for(int i=1;i<=n;i++) {
		int x;cin>>x;
		ve.pb(x);
	}
	int ls = 0,flag = 0;
	for(int v:ve){
		if(v < ls)flag = 1;
		ls = max(v,ls);
	}
	if(flag) cout<<n<<endl;
	else cout<<0<<endl;
}
```

## [C-小苯的数字合并_牛客小白月赛94 (nowcoder.com)](https://ac.nowcoder.com/acm/contest/82957/C)

### 题意

大白熊给了小苯一个长度为 $$n$$ 的数组 $$a$$，小苯想要**最大化** $$a$$ 的极差。

具体的，小苯可以做如下操作任意次（前提是数组至少有两个数字）：  

  

$\bullet$ 选择一个正整数 $$i \ (1 \leq i <n)$$，接着将 $$a_i$$ 与 $$a_{i+1}$$ 合并为一个数字，结果为二者的和。

（即：将 $$a_i$$ 变为 $$a_i + a_{i+1}$$，然后删去 $$a_{i+1}$$，当然操作完后 $a$ 的长度也会减一。）


小苯想知道他最大能将数组极差变为多少呢，请你帮帮他吧。

### 题解

注意到无法将数字变小，因此保留最小值是最优的，我们枚举最小值，计算前缀后缀和即可

```cpp
void slove() {
	cin>>n;
	for(int i=1;i<=n;i++) cin>>a[i];

	for(int i=1;i<=n;i++)
		pre[i] = pre[i-1] + a[i];

	for(int i=n;i;i--)
		rep[i] = rep[i+1] + a[i];

	int ans = 0;
	for(int i=1;i<=n;i++)
	{
		int mx = max(pre[i-1] ,rep[i+1]);
		ans = max(ans,mx - a[i]);
	}
	cout<<ans<<endl;
}
```

## [D-小苯的排列构造_牛客小白月赛94 (nowcoder.com)](https://ac.nowcoder.com/acm/contest/82957/D)

### 题意

格格有一个长度为 $$n$$ 的排列 $$p$$，但她不记得 $$p$$ 具体的样子，她只记得数组 $$a$$。  
其中：$$a_i = gcd(p_1, p_2,...,p_i)$$，也就是说，$$a_i$$ 表示排列 $$p$$ 中前 $$i$$ 个数字的最大公约数。  


现在，她希望小苯将排列 $$p$$ 复原出来，请你帮帮他吧。

（但有可能无解，这意味着格格给出的 $$a$$ 数组可能是不正确的，此时输出 $$-1$$ 即可。）

### 题解

公约数只会不断变小或保持不变，排列的前缀最大公约数最多会有log(2e5)个非1的数，即不到30个数。并且满足这个数组非严格递减的。

如果整个数组除1外无相同数字，显然我们直接对1以前的数字按原样赋值即可，第一个1赋值1，随后任意。

如果整个数组对1存在相同数字，显然直接对第一个原样赋值也是可行的，随后赋值该数的倍数即可

注意由于最多只有log(2e5)个数是非1的，因此很容易想到整体的复杂度是不大于n*log(n)的，对于枚举倍数的过程可能存在更好的优化，这里就不提及了。

```cpp
void slove() {
	cin>>n;

	int f = 1;
	for(int i = 1;i<=n;i++){
		cin>>a[i];
		if(i > 1 && a[i-1] % a[i] != 0) f = 0;
	}

	vector<int> ves;
	for(int i=1;i<=n;i++){
		int c = 1, s = a[i];
		while(i < n && a[i+1] == a[i])i ++,c ++;
		int t = s;
		while(c && t <= n) {
			while(st[t]) t+=s;
			if(t > n) break;
			ves.push_back(t);
			st[t] = 1;
			// cout<<t<<endl;
			t+=s,c--;
		}
		if(c) {f = 0;break;}
	}

	if(!f) {cout<<-1<<endl; return ;}

	for(int v: ves) cout<<v<<' ';
		cout<<endl;

	return ;
}
```

## [E-小苯的01背包（easy）_牛客小白月赛94 (nowcoder.com)](https://ac.nowcoder.com/acm/contest/82957/E)

##[F-小苯的01背包（hard）_牛客小白月赛94 (nowcoder.com)](https://ac.nowcoder.com/acm/contest/82957/F)

### 题意

**注：此版本为本题的easy（简单版），与hard（困难版）唯一的不同之处只有数据范围。**  


小苯有一个容量为 $k$ 的背包，现在有 $n$ 个物品，每个物品有一个体积 $v$ 和价值 $w$，他想知道在体积不超过 $k$ 的前提下，他最多能装价值为多少的物品。

本问题中，物品的总体积定义为所装物品的体积的 $\&$（按位与），总价值也定义为所装物品的价值的 $\&$（按位与）。

（如果不选物品，则价值为 0，所占体积也为 0。）

### 题解

按位与显然需要从高到低位考虑，需要体积小而整体物品的价值高，容易想到，较少的物品容易有较高的价格。（并的最大值是当前物品中的最大值）

比如对于二进制下物品价值

```
101101
110010
101011
110110
```

不考虑体积，最优解是拿4号物品，我们假定4号物品体积恰好大于整体体积，而1，2，3都可以降低体积到合适的大小，即，2，4为最优解，整体的解法考虑为，选出尽量少的物品使得价值高而体积小于背包容量。

构造一个dp，对于前i个物品在体积为j下要求的最大价值，这要求维护出当前的数字，对于hard1e9的时间空间是不够的。easy版就直接跑$$O(n\times 4e3)$$即可，注意初始状态为(1<<14)-1，但是这样跑出来是过93%，哪里有问题？

一个可能的原因是0表示非法的同时也表示价值为0，想了半个小时，也没想到是为什么，要是有数据就好了。

官方题解是枚举答案，然后根据答案来选数，观察是否可以体积小于k

```cpp
void slove() {
	cin>>n>>k;

	for(int i=1;i<=n;i++) cin>>v[i]>>w[i];

	int ans = 0;
    for (int i = 0; i <= 2000; i++) {
        int V = (1 << 20) - 1;
        for (int j = 1; j <= n; j++) {
            if ((i & w[j]) == i) {
                V &= v[j];
            }
        }
        if (V <= k)
            ans = max(ans, i);
    }
    cout<<ans<<endl;
}
```

不得不说这个写法更简洁也更正确

hard显然我们需要优化枚举答案，只需要按位枚举即可，我们取当前位为1的取得越多，体积越小，因此，我们优先多选择当前价值为为1的使得体积尽可能的小。如果当前位取1，那么我们就抛弃所有当前位位0的值。就这样。

```cpp
void slove() {
	int i, j, k;
     
    cin >> n >> k;
     
    for (i = 1; i <= n; i++) {
        cin >> w[i] >> v[i];
        q[0].push({w[i], v[i]});
    }
    int ans = 0;
    for (i = 30; i >= 0; i--) {
        int ww = 0x7fffffff;
        int now = q[0].size();
        for (j = 0; j < now; j++) {
            auto x = q[0].front();
            q[0].pop();
            if ((x.y >> i) & 1) {
                q[1].push(x);
                ww &= x.x;
            } else {
                q[0].push(x);
            }
        }
         
        if (ww <= k) {
            ans |= (1 << i);
            while (!q[0].empty()) {
                q[0].pop();
            }
        }
        while (!q[1].empty()) {
            q[0].push(q[1].front());
            q[1].pop();
        }
    }
    cout << ans << endl;
}
```

