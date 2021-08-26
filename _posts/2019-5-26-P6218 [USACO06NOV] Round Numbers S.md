此题拿到手发现与一个数的二进制数位有关，于是考虑数位 DP​。 

首先思考 DP 的状态定义，首要的肯定是一个数的位数，接着就应该是一个数的 $0,1$ 出现次数了。  

这里为了不出现冗杂的 DP 维度，我们不妨将第二维定义为： $0$ 的出现次数减去 $1$ 的出现次数。  

于是我们的 DP 状态定义就出来了：  

$dp_{i,j}$ 表示的是在数位为 $i$ 的情况下 $0$ 的出现次数减去 $1$ 的的出现次数为 $j$ 的数字个数。  

于是我们就可以愉快地开始 DP，我之前也是习惯打循环的数位 DP，但是后来发现这种 DP 很难调而且对于我这种蒟蒻而言还很难打，在这里也推荐大家打记忆化搜索的版本，  

等数位 DP 练熟后再多写循环的版本吧。  

不过这里有个比较坑的地方，那就是对于一个数的二进制形式进行统计时，如果我们直接在记忆化搜索里面莽着搜，我们就很有可能会计入前导零导致我们 WA 掉。  

这里我也愚笨地给 DP 数组多开了一维，表示此时出现了过 $1$ 没，换言之就是表示标记我们是否可以开始统计数字 $0$ 。  

具体见代码：  
```cpp

#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
using namespace std;
long long dp[55][55][2],n,m;//dp状态定义见文章内 
int digit[55],len;
long long dfs(int len , int s , bool ort , bool up)
{
	if(!len) return s >= 0;//如果0的个数大于等于1的个数就返回1 
	if(!up && dp[len][s][ort] != -1) return dp[len][s][ort];//如果这里已经被搜过了 
	long long res = 0;
	int maxlen = (up == 0) ? 1 : digit[len];
	for(int i = 0 ; i <= maxlen ; i ++)
	{
		if(i == 0)
		{
			if(ort == 1) res += dfs(len - 1 , s + 1 , 1 , up && (i == maxlen));//如果此时前面已经出现过1即这时的0可以开始被统计了 
			else res += dfs(len - 1 , s , 0 , up && (i == maxlen));//否则我们就不能管当前的0，也就是当前的0不做贡献 
		}
		else res += dfs(len - 1 , s - 1 , 1 , up && (i == maxlen));//出现1的话就必然计入贡献咯 
	}
	if(!up) dp[len][s][ort] = res;
	return res;
}
long long Solve(long long op)
{
	len = 0;
	while(op) digit[++ len] = op % 2 , op >>= 1;
	return dfs(len , 0 , 0 , 1);
}
int main()
{
	memset(dp , -1 , sizeof dp);
	scanf("%lld %lld",&n,&m);
	printf("%lld\n",Solve(m) - Solve(n - 1));
	return 0;
} 
//还是有点小复杂请谅解
```