# 前言
其实没做出来这道题还是有点难过的，考场上的一道题，

已经想到了离线倒序处理，并且发现了如果更新了答案那么这个值就要被包含在新正方形内这个性质，    

接着就发现寻找新正方形及其困难，想到用线段树维护一些信息，也全被自己 Hack 或者说太难实现就放弃了，    

最后很遗憾地打了个暴力上去甚至连 $O(n^2k)$ 都不是。  

# 步入正题    
其实如果做过[最大正方形](https://www.luogu.com.cn/problem/P1387)并且能完全理解其原理的话，这道题垒个 $2h$ 怎么都做得出来，可惜的是我不仅忘了这道题并且那时做题也是没弄清楚原理，这都是过去知识掌握不牢的后果啊。    

先讲一下黄题水平的 $O(n^2k)$ 暴力吧，直接每次加点的时候跑个最大正方形就行了。    

```cpp
void DP()
{
	for(int i = 1 ; i <= n ; i ++)
		for(int j = 1 ; j <= m ; j ++)
			if(mp[i][j])
			{
				dp[i][j] = min(dp[i - 1][j - 1] , min(dp[i - 1][j] , dp[i][j - 1])) + 1;
				//Print[k] = max(Print[k] , dp[i][j]);
			}
}
```

这里粗略证明一下这个 DP 式。如果此时我们枚举到了 $dp_{i,j}$ 的话，我们考虑 $dp_{i - 1 , j - 1}$ 的信息，发现就是多了一行一列而已。

而我们只要知道此时向上和向左最多能往外扩展多少就行了，这时再和我们的 $dp_{i - 1 , j - 1}$ 的大小取个 ```min``` 就是我们当前最大能扩展到哪里的值即 $dp_{i,j}$ 值。

而且你会发现 $dp_{i - 1 , j - 1}$ 也制约着当前行列的最远扩展，由于本来就是取  $\min$ 值，所以我们直接把 $dp_{i - 1, j}$ 和 $dp_{i , j - 1}$ 值拿来用就好了，更详细的证明就参考[最大正方形](https://www.luogu.com.cn/problem/P1387)吧。    

接着我们考虑优化一下，我们发现直接来的话未免太难了，所以我们倒序处理，并且得到了如前言所述的性质。     

我们此时发现，如果我们能通过枚举当前的行区间然后在 $O(m)$ 以下的时间复杂度进行区间查询判断区间查询是否合法，那么 $O(nq \log m)$ 的时间复杂度是完全可以接受的。     

然后就得想到这道题一个比较关键的东西：   

- $up_{i,j}$ 维护点 $(i , j)$ 以上能扩散的最远距离。   
- $down_{i,j}$ 维护点 $(i , j)$ 以下能扩散的最远距离。    

很显然的是我们都可以通过 $O(n ^ 2)$ 预处理出这两组信息，并且可以在每次加点时 $O(n)$ 暴力修改。    

那么我们岂不是只要知道当前区间里所有 $up$ 的最小值和 $down$ 的最小值就知道当前这个区间可以扩出来的最大值了啊，这玩意儿显然线段树啊，由于答案单调递增我们每次就看前一个答案在当前是否能增大就好了啊。    

到此整道题的关键思路就完了，其实在我看来最关键的就是要想到 $up$ 与 $down$ ， 欸思考力还是欠佳……    
```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
using namespace std;
const int Len = 2005,Inf = 1e9;
int n,m,k,qx[Len],qy[Len],Print[Len],dp[Len][Len],minu,mind,mp[Len][Len],up[Len][Len],down[Len][Len],ansu[Len << 2],ansd[Len << 2];
char s[Len];
int ls(int x){return x << 1;}
int rs(int x){return x << 1 | 1;}
void push_up(int x){ansu[x] = min(ansu[ls(x)] , ansu[rs(x)]) , ansd[x] = min(ansd[ls(x)] , ansd[rs(x)]);}
void build(int p,int l,int r,int h)
{
	if(l == r)
	{
		ansu[p] = up[h][l];
		ansd[p] = down[h][l];
		return;
	}
	int mid = (l + r) >> 1;
	build(ls(p) , l , mid , h);
	build(rs(p) , mid + 1 , r , h);
	push_up(p);
}
void Fmin(int p,int l,int r,int nl,int nr)
{
	if(nl <= l && nr >= r)
	{
		minu = min(minu , ansu[p]);
		mind = min(mind , ansd[p]);
		return;
	}
	int mid = (l + r) >> 1;
	if(nl <= mid) Fmin(ls(p) , l , mid , nl , nr);
	if(nr > mid) Fmin(rs(p) , mid + 1 , r , nl , nr);
}
void DP()
{
	for(int i = 1 ; i <= n ; i ++)
		for(int j = 1 ; j <= m ; j ++)
			if(mp[i][j])
			{
				dp[i][j] = min(dp[i - 1][j - 1] , min(dp[i - 1][j] , dp[i][j - 1])) + 1;
				Print[k] = max(Print[k] , dp[i][j]);
			}
}
bool check(int Sec,int nowy)
{
	if(Sec > n || Sec > m) return 0;
	for(int i = max(1 , nowy - Sec + 1) ; i <= m - Sec + 1 ; i ++)
	{
		minu = mind = Inf;Fmin(1 , 1 , m , i , i + Sec - 1);
		if(minu + mind - 1 >= Sec) return 1;
	}
	return 0;
}
int main()
{
	scanf("%d %d %d",&n,&m,&k);
	for(int i = 1 ; i <= n ; i ++)
	{
		scanf("%s",s + 1);
		for(int j = 1 ; j <= m ; j ++) mp[i][j] = (s[j] == '.');
	}
	for(int i = 1 ; i <= k ; i ++)
	{
		scanf("%d %d",&qx[i],&qy[i]);
		mp[qx[i]][qy[i]] = 0;
	}
	for(int i = 1 ; i <= n ; i ++)
		for(int j = 1 ; j <= m ; j ++)
		{
			if(mp[i][j]) up[i][j] = up[i - 1][j] + 1;
			else up[i][j] = 0;
		}
	for(int i = n ; i >= 1 ; i --)
		for(int j = 1 ; j <= m ; j ++)
		{
			if(mp[i][j]) down[i][j] = down[i + 1][j] + 1;
			else down[i][j] = 0;
		}
	DP();
	int res = Print[k];
	for(int i = k ; i >= 1 ; i --)
	{
		mp[qx[i]][qy[i]] = 1 , Print[i] = res;
		up[qx[i]][qy[i]] = up[qx[i] - 1][qy[i]] + 1;
		for(int j = qx[i] + 1 ; j <= n ; j ++) 
		{
			if(!mp[j][qy[i]]) break;
			up[j][qy[i]] += up[qx[i]][qy[i]];
		}
		down[qx[i]][qy[i]] = down[qx[i] + 1][qy[i]] + 1;
		for(int j = qx[i] - 1 ; j >= 1 ; j --)
		{
			if(!mp[j][qy[i]]) break;
			down[j][qy[i]] += down[qx[i]][qy[i]];
		}
		build(1 , 1 , m , qx[i]);
		while(check(res + 1 , qy[i])) res ++;
	}
	for(int i = 1 ; i <= k ; i ++) printf("%d\n",Print[i]);
	return 0;
}
```