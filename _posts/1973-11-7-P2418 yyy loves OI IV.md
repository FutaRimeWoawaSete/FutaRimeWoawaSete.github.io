感觉还是有点淦，WA 了无数发才过掉了这道题。  

这道题首先我们不难看出是一个 dp ，而且是个很经典的分组 dp ，我们只要根据题意模拟一下这个 dp 即可，橙 dp 不想细讲。  

时间复杂度 $O(n ^ 2)$ 交上去后你会不出意外地 T 掉七个点。  

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
using namespace std;
const int Len = 5e5 + 5;
int n,m,dp[Len],a[Len],sum[Len][3],num_two,num_one;
int Iabs(int x){return x >= 0 ? x : -x;}
bool check(int l,int r)
{
	num_one = sum[r][1] - sum[l - 1][1] , num_two = sum[r][2] - sum[l - 1][2];
	if(num_one == (r - l + 1) || (num_two == (r - l + 1)) || Iabs(num_one - num_two) <= m) return true;
	return false; 
}
int main()
{
	scanf("%d %d",&n,&m);
	for(int i = 1 ; i <= n ; i ++)
	{
		int x;scanf("%d",&x);
		sum[i][1] = sum[i - 1][1] + (x == 1);
		sum[i][2] = sum[i - 1][2] + (x == 2);
	}
	memset(dp , 0x3f , sizeof dp);
	dp[0] = 0;
	for(int i = 1 ; i <= n ; i ++)
	{
		for(int j = 0 ; j < i ; j ++)
			if(check(j + 1 , i)) dp[i] = min(dp[i] , dp[j] + 1);
	}
	printf("%d\n",dp[n]);
	return 0;
}
```
于是我们考虑优化这个 dp ，很明显我们可以对我们的 check 部分进行一定的优化：   

- 对于全部为 $1$ 或者全部为 $2$ 的情况直接维护即可，这里可以再开个线段树，亦或可以直接动态维护，我就没有再开线段树了。    
 
- 对于比较棘手的人数相差的绝对值控制在 $m$ 以内，我们可以发现当我们此时 dp 转移到
 
- 第 $i$ 位时，我们的右端点是确定的，所以我们只需要把我们的式子展开，即 $sum_{r,1} - sum_{r,2} + sum_{l - 1,2} - sum_{l-1,1}$ ，当前可以计算 $sum[r][1] - sum[r][2]$ ，所以我们维护后面这一坨东西打入线段树然后通过绝对值的概念计算出查询区间查询最小值即可。
  

 可能还是有点不好理解，所以这里以样例举个例子：    
```
5 1
1
1
2
2
1
```
为了方便行文，令 $sum[r][1] - sum[r][2] = calc(r)$ ，$sum[l][2] - sum[l][1] = Tot(l)$ 。  
初始化 $dp[0] = 0$ ，接着循环 $1 \sim n$：    
- 转移到第一个数，$calc(i) = 1$ ，需要查询的 $Tot()$ 区间为 $-2 \sim 0$ ；     
- 转移到第二个数，$calc(i) = 2$ ，需要查询的 $Tot()$ 区间为 $-3 \sim -1$ ；   
- 转移到第三个数，$calc(i) = 1$ ，同第一个数的查询区间；    
- 转移到第四个数，$calc(i) = 0$ ，需要查询的 $Tot()$ 区间为 $-1 \sim 1$ ；    
- 转移到第五个数，$calc(i) = 1$ ，同第一个数的查询区间。    

至于这个查询的区间涉及到负数，再~~瞪~~一下原题，我们不难发现我们此时维护的区间的值域只会在 $-n \sim n$之间，离散一下就好了。    
对于第一种第二种操作，我们开两个变量维护一下当前的连续区间是哪种数，以及区间的最小 $dp$ 值是多少，如果当前这个数已经打破了连续的区间就删除原先数据重新维护，否则就直接维护最小值即可。   
这道题讲到这里差不多已经完了，这里需要注意几个细节：    
1.线段树空间开八倍，因为你此时离散出来后有 $2n + 1$ 个数；    
2.维护一二操作时，也许你会习惯性地认为这个 $dp$ 值是严格不下降的，实际上你随手画几个样例就可以把这个结论推翻，所以一定要维护区间最小值。   
3.由于 我们计算出来的查询区间有可能会跑出 $-n \sim n$ ，所以查询之前缩一下这个区间控制在 $-n \sim n$ 之间，不然有可能越界。   

附代码，帮助一下调不过这道题的同志们，其实题不难，细节多，评个紫题也说得过去。    
```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
//单独维护全是一种人的情况
//我们把我们的减法展开一下，发现我们只要维护一下相对差值就行了，+1的话线段树里面维护的值就需要-1，所以我们只需要开一个2 * n的线段树，然后离散一下查询即可。 
using namespace std;
const int Len = 5e5 + 5;
int num_c[Len],n,m,dp[Len],a[Len],sum[Len][3],num_two,num_one,now_c[Len];
int ans[Len << 3];
int Iabs(int x){return x >= 0 ? x : -x;}
int deC(int num){return num + n + 1;}
int ls(int x){return x << 1;}
int rs(int x){return x << 1 | 1;}
void push_up(int x){ans[x] = min(ans[ls(x)] , ans[rs(x)]);}
void build(int p,int l,int r)
{
	ans[p] = n + 1;
	if(l == r) return;
	int mid = (l + r) >> 1;
	build(ls(p) , l , mid);
	build(rs(p) , mid + 1 , r);
}
void update(int p,int l,int r,int idx,int w)
{
	if(l == r) 
	{
		ans[p] = min(ans[p] , w);
		return;
	}
	int mid = (l + r) >> 1;
	if(idx <= mid) update(ls(p) , l , mid , idx , w);
	if(idx > mid) update(rs(p) , mid + 1 , r , idx , w);
	push_up(p);
}
int query(int p,int l,int r,int nl,int nr)
{
	if(nl <= l && nr >= r) return ans[p];
	int mid = (l + r) >> 1;int res = n + 1;
	if(nl <= mid) res = min(res , query(ls(p) , l , mid , nl , nr));
	if(nr > mid) res = min(res , query(rs(p) , mid + 1 , r , nl , nr));
	return res;
}
int main()
{
	scanf("%d %d",&n,&m);
	int lim = (n << 1) + 1;
	for(int i = 1 ; i <= n ; i ++)
	{
		scanf("%d",&a[i]);
		sum[i][1] = sum[i - 1][1] + (a[i] == 1);
		sum[i][2] = sum[i - 1][2] + (a[i] == 2);
	}
	for(int i = 0 ; i <= n ; i ++) num_c[i] = sum[i][2] - sum[i][1];
	build(1 , 1 , lim);
	memset(dp , 0x3f , sizeof dp);
	dp[0] = 0;
	update(1 , 1 , lim , deC(num_c[0]) , dp[0]);
	int lst = 0 , preans = 0;
	for(int i = 1 ; i <= n ; i ++)
	{	
		int nums = sum[i][1] - sum[i][2];
		int l = -m - nums , r = m - nums;
		l = deC(max(l , -n)) , r = deC(min(r , n));
		if(lst != a[i])
		{
			lst = a[i];
			preans = dp[i - 1];
		}
		dp[i] = min(preans , query(1 , 1 , lim , l , r)) + 1;
		update(1 , 1 , lim , deC(num_c[i]) , dp[i]);
		preans = min(preans , dp[i]);
		//printf("%d %d %d %d\n",lst,lstidx,preans,dp[i]);
	}
	printf("%d\n",dp[n]);
	return 0;
}
```