表面看起来是一道计数题，实际是一道 DP 题。

我们首先设 $dp_{i,j}$ 表示长度为 $i$ 并且以 $j$ 结尾的严格上升子序列的个数，    

我们经过一定的推导后可以得到一个 DP 转移式：    

- $ dp_{i,j} = \sum_{k = 1}^{j - 1}dp_{i-1,k}$ 其中 $a_k < a_j$   
- 
照着这个 $DP$ 转移式写上去我们发现这是一个 $O(n ^ 2m)$ 的 DP 很明显我们过不掉这道题。   

这时我们考虑如何优化这个 DP 转移式，毕竟 DP 式推出来了但是时间复杂度过不掉的话基本都是需要优化的，我们发现我们可以先离散所有 $a_i$ 然后用线段树维护前面的 $dp_{i,j - 1}$ 。    

不过这里需要注意的是，我们由于只能取 $\sum_{k = 1}^{j - 1}$，所以我们必须一个个加。    

也就是这样：   

```cpp
for(int i = 2 ; i <= m ; i ++)
{	
	for(int j = 1 ; j <= n ; j ++)
	{
		add(a[j] , dp[i - 1][j]);
		if(a[j] != 1) dp[i][j] = query(a[j] - 1);
		if(i == m) Ans += dp[i][j] , Ans %= mod;	
	}
}
```
~~不过由于线段树还是大常数~~，在本人亲测线段树会被卡常后就换成了树状数组来卡这道题，不得不说这道题还是有点卡……   

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
#include<cmath>
using namespace std;
const int Len = 1e3 + 5;
const long long mod = 1e9 + 7;
int cnt,n,m,cnts;
long long c[Len],a[Len],Ans,dp[Len][Len],lsh[Len];
int lowbit(int x){return x & (-x);}
void Clear(){for(int i = 1 ; i <= cnts ; i ++) c[i] = 0;}
void add(int x,long long d){for( ; x <= cnts ; x += lowbit(x)) c[x] += d , c[x] %= mod;}
long long query(int x){long long res = 0;for( ; x ; x -= lowbit(x)) res += c[x] , res %= mod;return res;}
int main()
{
	int T;
	scanf("%d",&T);
	while(T --)
	{
		cnt ++;
		Ans = 0;
		scanf("%d %d",&n,&m);
		for(int i = 1 ; i <= n ; i ++) 
		{
			scanf("%lld",&a[i]);
			lsh[i] = a[i];
			dp[1][i] = 1;
		}
		sort(lsh + 1 , lsh + 1 + n);
		cnts = unique(lsh + 1 , lsh + 1 + n) - lsh - 1;
		for(int i = 1 ; i <= n ; i ++) a[i] = lower_bound(lsh + 1 , lsh + 1 + cnts , a[i]) - lsh; 
		if(m == 1) Ans = n;
		else
		{
			Clear();
			for(int i = 2 ; i <= m ; i ++)
			{	
				for(int j = 1 ; j <= n ; j ++)
				{
					add(a[j] , dp[i - 1][j]);
					if(a[j] != 1) dp[i][j] = query(a[j] - 1);
					if(i == m) Ans += dp[i][j] , Ans %= mod;	
				}
				Clear();
			}
			
		}
		for(int i = 2 ; i <= m ; i ++)
			for(int j = 1 ; j <= n ; j ++) dp[i][j] = 0;
		Clear();
		printf("Case #%d: %lld\n",cnt,Ans); 
	}
	return 0;
}
```