---
title: 2024武汉ICPC邀请赛VP
date: 2024-08-14 19:46:31
tags: [icpc]
categories: 
	- 比赛
	- 模拟赛
	- icpc邀请赛
---

## 武汉邀请赛VP

### I.[Cyclic Apple Strings](https://codeforces.com/gym/105143/problem/I)

<!--more-->

计算第一个1后的0的段数

```cpp
void slove() {
	string s;cin>>s;
	int f =0,cnt = 0;
	for(int i = 0;i < s.size(); i ++)
	{
		f|=(s[i] == '1');
		if(s[i]=='0')
		while(i+1 < s.size() && s[i+1] == '0')
			i++;
		if(f && s[i] =='0') cnt++;
	}
	cout<<cnt<<endl;
}
```

K.[Party Games](https://codeforces.com/gym/105143/problem/K)

打表可发现1，0数出现有规律，于是整数域缩减到mod4整数域。最简单的方法，模拟一下可以发现余数r为0，1时先手胜，余2，3时后手胜

```cpp
void slove() {
	cin>>n;
	int r = n % 4;
	if(r==0 || r==1) cout<<"Fluttershy\n";
	else if(r==2 || r==3) cout<<"Pinkie Pie\n";
}
```

F. [Custom-Made Clothes](https://codeforces.com/gym/105143/problem/F)

简单二分或者二分套二分(另一个二分二分第一次的下标)

注意到行与行，列与列均不相同的数字且两者不在对方的”前缀“矩阵中时无法判断两数大小，注意到$$n \le 1000$$。

考虑二分答案，我们怎么在50000次内找到它在第几大。显然的思路是二分行列下标通过先找到其在“形式上最大”的行。然后记忆化的思维，不断减少这个下标。最多减少n次，二分最多20次，因此完全是足够的。

```cpp
int query(int x,int y,int v){
	if(g[x][y]) return g[x][y] <= v;
	cout<<"?"<<x<<' '<<y<<' '<<v<<endl;
	cout.flush();
	int res ;cin>>res;
	return res;
}

int get(int x,int y,int l=1,int r=mx){
	if(!g[x][y]) return g[x][y];
	while(l<r){
		int mid=  l+r>>1;
		if(query(x,y,mid)) r=mid;
		else l = mid+1;
	}
	return g[x][y] = l;
}

void slove() {
	cin>>n>>k;
	mx = n * n;
	int l=1,r=mx;
	while(l<r){
		int mid = l+r>>1;
		int now = n;
		int cnt=0;
		for(int i=1;i<=n;i++){
			while(now && !query(now,i,mid))now--;
			cnt += now;
			if(!now) break;
		}
		if(cnt < k) r=mid;
		else l=mid+1; 
	}
	cout<<r<<endl;
}
```



B. [Countless Me](https://codeforces.com/gym/105143/problem/B)

很显然的是，操作n次等同于任意捏一个总和相同的数组。下面简单说明一下：

取最大值与顺序无关，所以，我们先对想要的数组和操作数组排序，可以发现我们使得任意较小值变大都可以从后面的一个数中拿到对应的数字，相对应的，反过来也是一样的。形象的讲，对一个数字的加减值可以看作一个完全的网络流图，每一条边都是无限权值，自然使得任意顶点变为需要的值可以通过合理的操作其他具有相反的差值顶点达到。

维护sum值，将高位的1拆分给低位。

```cpp
void slove() {
	cin>>n;
	for(int i=1;i<=n;i++) cin>>a[i];

	int sum = 0;
	for(int i=1;i<=n;i++) sum += a[i];

	int ans =0;
  for (ll i = 30; i >= 0; i--) {
      if (((1ll << i) - 1) * n >= sum) continue;
      ll num = min(n, sum / (1ll << i));
      if (num == 0) continue;
      sum -= num * (1ll << i);
      ans |= 1ll <<  i;
  }
	cout<<ans<<endl;
}
```

下面的做法是错误的，试图找到一个临界点i，将比i高的位均拆分至低位i，而不维护sum值，当低位容不下时时则出错。

hack数据

```txt
2
3 0
-------
2
7 3
```

```cpp
void slove() {
    cin>>n;
    for(int i=1;i<=n;i++) cin>>a[i];

    int sum = 0;
    for(int i=1;i<=n;i++) sum += a[i];

    int ans =0,cnt = 0;
    for(int i=31;~i;i--){
        cnt = (cnt<<1) + (sum >>i &1);
        if(cnt >= n) ans += (sum >> i &1) <<i;
    }
    cout<<ans<<endl;
}
```

D.[ICPC](https://codeforces.com/gym/105143/problem/D)

ICPC!!!，估计很多人一看到这个名字和答案求法就跑了。

但实际上，非常简单的dp，从前面跑一遍，后面跑一遍。取最大就over了。

思路也很简单，只需要折返一次（很常见，很经典），于是我们维护f数组，从前面转移一遍，从后面转移一遍。

dp的妙处就在于，你何必去想前一个状态是不是真的向相反的方向走有是不是只返回一次。实际上，即使它有这样的规律，在转移也不会体现出来，我们实际上是根据前一个状态的最优解（however只能吃一次加分是必要的）进行计算的。

```cpp
void slove() {
	cin>>n;
	for(int i=1;i<=n;i++) cin>>a[i];

	for(int i=1;i<=n;i++) pre[i] = pre[i-1] + a[i];

	for(int i=1;i<=n;i++){
		for(int j=1;j<=n<<1;j++){
			f1[i][j] = max(pre[i] - pre[max(i-j-1,0)]
				,pre[min(n,i+j)]- pre[i-1]);
		}
	}

	for(int i=1;i<=n;i++){
		for(int j=1;j<=n<<1;j++){
			f1[i][j] = max(f1[i][j],f1[i-1][j-1]);
		}
	}

	for(int i=n;i;i--) {
		for(int j=1;j<=n<<1;j++){
			f2[i][j] = max(f1[i][j],f2[i+1][j-1]);
		}
	}

	ll ans = 0;
	for(int i=1;i<=n;i++){
		ll t = 0;
		for(int j=1;j<=n<<1;j++){
			t ^= j*max(f1[i][j],f2[i][j]);
		}
		ans ^= (i+ t);
	}
	cout<<ans<<endl;
}
```

