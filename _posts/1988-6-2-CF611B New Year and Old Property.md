此题看到所求和数位有关，果断考虑 数位 DP 。  

经过一系列思考，我们需要在 DP 里面记录数位(老套路了)， $0 , 1$ 的个数，  

至于为什么要记录 $1$ 的个数，是因为我们在统计时要是把前导 $0$ 给算进去了我们 $数位DP$ 就~~直接起飞了~~对吧。  

所以这里我们要记录一下 $1$ 的个数，看看这一步统计的 $0$ 是前导 $0$ 还是数位 $0$ 。  

不过这里我们为了防止以后遇到类似的题需要维护其他东西时数位 $DP$装不下了，我们可以考虑优化这两维。  

由于 $0$ 的个数大于了 $1$ 的话肯定就没贡献，所以这里我们的这一维大小可以设置为 $2$ 而另一位只用记录有没有出现过 $1$ 即可，数组大小就缩成 $1$ 了。  

一道还算可以的题，能打数位 $DP$ 为什么要熬费心思去找规律呢对吧。  

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
using namespace std;
long long dp[105][3][2],n,m;
int digit[105],le;
long long dfs(int len,int sum,int srt,bool up)
{
	if(!len) return sum == 1;
	if(sum > 1) return 0;
	if(!up && dp[len][sum][srt] != -1) return dp[len][sum][srt];
	int maxlen = (up == 0) ? 1 : digit[len];
	long long res = 0;
	for(int i = 0 ; i <= maxlen ; i ++)
	{
		if(i == 0) 
		{
			if(srt > 0) res += dfs(len - 1 , sum + 1 , 1 , up && (i == maxlen));
			else res += dfs(len - 1 , sum , 0 , up && (i == maxlen));
		}
		else res += dfs(len - 1 , sum , 1 , up && (i == maxlen)); 
	}
	if(!up) dp[len][sum][srt] = res;
	return res;
}
long long Solve(long long x)
{
	le = 0;
	while(x) digit[++ le] = x % 2 , x >>= 1;
	return dfs(le , 0 , 0 , 1);
}
int main()
{
	scanf("%lld %lld",&n,&m);
	memset(dp , -1 , sizeof dp);
	printf("%lld\n",Solve(m) - Solve(n - 1));
	return 0;
}
```