CF $1500$ 的 DP 好题。  

也许并不难，但是出的好。  

在阅读题目过后发现几个性质：  

1. $|s|$ 为 $2$ 的整数次方；  
 
2. 有明确的状态转移方式；  

许多对线段树等数据结构比较熟悉的人这里就基本上直接想到正解了。  

我们模仿线段树的方法写 DP，由于数列中的数都是小写字母所以我们只用维护当前区间有多少个字符 $a$ 以及当前区间的所有c-好串即可。  

具体实现时注意一下出口的赋值和 DP 初始化即可。 

然后不要乱开数组大小，这道题空间有点小紧……  

然后就是根据题意 DP 转移即可，于是我们一波结束这道题。  

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
using namespace std;
const int Len = 131077,Inf = 1e9;
char s[Len];
long long n,m,dp[Len << 2][28],sum[Len << 2][28];
int ls(int x){return x << 1;}
int rs(int x){return x << 1 | 1;} 
void DP(int p,int l,int r)
{
	for(int i = 1 ; i <= 26 ; i ++) sum[p][i] = 0,dp[p][i] = Inf;
	if(l == r)
	{
		sum[p][s[l] - 'a' + 1] = 1;
		for(int i = 1 ; i <= 26 ; i ++) dp[p][i] = 1;
		dp[p][s[l] - 'a' + 1] = 0;
		return;
	}
	int mid = (l + r) >> 1;
	DP(ls(p) , l , mid);
	DP(rs(p) , mid + 1 , r);
	int Llen = mid - l + 1 , Rlen = r - mid;
	for(int i = 1 ; i <= 26 ; i ++)
	{
		sum[p][i] = sum[ls(p)][i] + sum[rs(p)][i];
		dp[p][i] = min(Llen - sum[ls(p)][i] + dp[rs(p)][i + 1] , Rlen - sum[rs(p)][i] + dp[ls(p)][i + 1]); 
	}
}
int main()
{
	int t;
	scanf("%d",&t);
	while(t --)
	{
		scanf("%lld",&n);
		scanf("%s",s + 1);
		DP(1 , 1 , n);
		printf("%lld\n",dp[1][1]);
	}
	
	return 0;
}
```