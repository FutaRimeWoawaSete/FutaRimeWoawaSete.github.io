这道题我是真的被淦了……   

首先我们正常人想法：暴力 $O(nm)$ 来一发。   

然而 T 飞不用说了。   

又观察了一会儿后，突然发现这道题有点像 KMP 。如果用 KMP 的特殊匹配模型解决是否可以解决？     

经过手动模拟后我们发现，其实确定当前匹配的状态就是相对位置，如果相对位置相同那不就匹配起了？    

然而相对位置又和一个数在一个序列的排名有关，所以我很快就想出来了一个思路：维护这个数在当前查询里的排名。    

再观察数据范围，老 $10 ^ 6$ 了。果断离散后搬树状数组大哥。    

然而此时我突然发现，我们 KMP$的匹配是需要一个阶段性，    

抽象一点来说需要“递推”上来的啊，可是我们此时维护排名需要知道当前区间的所有数啊，然后我就被卡死了。    

眼看已经想超时了，果断开了题解，看到有一篇树状数组的 TJ 还是挺开心的，终于能自己独立想对黑题算法了……    

读了前面才发现自己确实太莽了，没分析清楚，其实我们只要知道 p 数组前面有多少个数比它小就 OK 了。   

这就很有意思了，也许很多人在这里会有疑惑。    

我们以样例先举例子试试？   

```
2 1 5 3 4 
0 0 2 2 3
```
上一行为 $p$ 数组，下一行就是每个数前面有多少个数比它小。    

这里我稍微讲的好理解一点，我们单一一个元素的排名就是 $1$ ，这点不用废话吧，那么现在我们进行递推，   

我们此时得到了第 $2$ 个元素知道它前面有多少个数比它小，要么前面的数比它大要么比它小，此时序列中的相对排名我们还是可以确定。    

这时你就会发现这个递推状态就相当于此时前面的所有元素的排名我们都确定了，而此时我们新增的元素又知道它前面有多少个比它小，那么就相当于我们当前这个元素加入前面的序列排序后，它的排名就是有多少个数比它小这个值再加上 $1$ 。    

这个解释已经很详细了吧……    

再~~数学~~一点，我们可以把记录下来的每个数前面有多少个数比它小的数组成的集合，当成一个状态，它和最后组成的相对排名成一个一一对应关系。   

于是我们直接维护上述条件就行了，由于我们本来的 KMP 算法就只有 $O(n + m)$ ， 所以现在的时间复杂度就是 $O(n + m \log n)$ 。   

不得不说，这是一道 KMP 好题。    
```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
using namespace std;
const int Len = 1e6 + 5;
int n,m,tot,p[Len],s[Len],kmp[Len],cnt[Len],c[Len],lsh[Len],Print[Len];
int lowbit(int x){return x & (-x);}
void add(int x){while(x <= n) c[x] ++ , x += lowbit(x);}
void del(int x){while(x <= n) c[x] -- , x += lowbit(x);}
int query(int x)
{
	int res = 0;
	while(x)
	{
		res += c[x];
		x -= lowbit(x);
	}
	return res;
}
void Clear_p(int l,int r){for(int i = l ; i <= r ; i ++) del(p[i]);}
void Clear_s(int l,int r){for(int i = l ; i <= r ; i ++) del(s[i]);}
int main()
{
	scanf("%d %d",&m,&n);
	for(int i = 1 ; i <= m ; i ++){int x;scanf("%d",&x);p[x] = i;}
	for(int i = 1 ; i <= n ; i ++)
	{
		scanf("%d",&s[i]);
		lsh[i] = s[i];
	} 
	sort(lsh + 1 , lsh + 1 + n);
	for(int i = 1 ; i <= n ; i ++) s[i] = lower_bound(lsh + 1 , lsh + 1 + n , s[i]) - lsh;
	int j = 0;
	for(int i = 1 ; i <= m ; i ++) cnt[i] = query(p[i]) , add(p[i]);//num数组就是表示前面有多少个数小于它
	memset(c , 0 , sizeof c);
	for(int i = 2 ; i <= m ; i ++)
	{
		while(j && query(p[i]) != cnt[j + 1]){Clear_p(i - j , i - kmp[j] - 1) ; j = kmp[j];}//中间全部甩掉
		if(query(p[i]) == cnt[j + 1]){add(p[i]) ; j ++;}
		kmp[i] = j;
	}
	j = 0;
	memset(c , 0 , sizeof c);
	for(int i = 1 ; i <= n ; i ++)
	{
		while(j && query(s[i]) != cnt[j + 1]){Clear_s(i - j , i - kmp[j] - 1) ; j = kmp[j];}
		if(query(s[i]) == cnt[j + 1]){j ++ ; add(s[i]);}
		if(j == m) Print[++ tot] = i - m + 1;
	}
	printf("%d\n",tot);
	for(int i = 1 ; i <= tot ; i ++) printf("%d ",Print[i]);
	return 0;
}
```