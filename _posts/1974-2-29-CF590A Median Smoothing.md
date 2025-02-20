考试时的一道原题，其实还是不难。    

不知道为什么自己写的这么长，可能想得有点复杂。  

首先在考场上就想这么一个问题：可否进行 $O(n \times ans)$ 的算法进行暴力模拟。    

手玩了一组，发现 $ans$ 可以很大，所以卡一个 $O(n ^ 2)$ 不成问题，放弃暴力模拟。    

这时再仔细~~手玩~~发现一个很重要的性质：如果相邻有多个数是一个数，那么这么多个数都不会变了。    

这点从原题就可以很简单地得知，

两个数都是 $0$ 那么该位生成的数就是 $0$ ，如果两个数都是 $1$ 那么该位生成的数就是 $1$。  

于是我们可以先将连续的的数字隔开分块，如果相邻两个数不一样就要划开分块，这里需要特判开头结尾是因为开头块结尾块自己就相当于是不变的数字，不需要依赖别的数字。

一定要记住特判开头结尾(考场上因为这个还差点打挂了)。 

为了方便说明，我们把块内数字固定的块称为具有性质 $A$ ，否则成为具有性质 $B$ 。   
接着我们又发现两个性质：
```
两两具有性质 A 的块之间夹杂的一定都是一些具有性质 B 的块或者不夹杂任何块。    
每经过一次操作后，两两具有性质 A 的块就向中间扩散一个单位，
因为具有性质 B 的块都是单一元素，
所以这里可以理解一个单位就相对于原数列中的一个单位。    
```
于是我们就直接暴力扫一遍求两两 $A$ 块之间至多需要多少次才能扩散完，然后再暴力模拟一遍生成的过程即可。    
```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
using namespace std;
const int Len = 1e6 + 5;
int a[Len],n,belong[Len],Whi[Len],cnt,flag[Len],to[Len],ans;//原数列，这个块里有多少个元素，这个块里的元素都是什么，块的个数，这个块带有性质A还是性质B，这个A块的下一个A块的下标 
int main()
{
	//freopen("darkside.in","r",stdin);
	//freopen("darkside.out","w",stdout);
	scanf("%d",&n);
	for(int i = 1 ; i <= n ; i ++) scanf("%d",&a[i]);
	if(n <= 2) 
	{
		printf("0\n");
		for(int i = 1 ; i <= n ; i ++) printf("%d ",a[i]);
		return 0;
	}
	belong[1] = 1;
	Whi[1] = a[1];
	cnt = 1;
	for(int i = 2 ; i < n ; i ++)
	{
		if(a[i] != a[i - 1])
		{
			cnt ++;
			belong[cnt] ++;
			Whi[cnt] = a[i];
		}
		else belong[cnt] ++;
	}
	if(a[n] == a[n - 1]) belong[cnt] ++;
	else 
	{
		cnt ++;
		belong[cnt] ++;
		Whi[cnt] = a[n];
	}//以上请结合数组定义 
	flag[1] = flag[cnt] = 1;//1就表示带A性质，0就表示B性质 
	for(int i = 2 ; i < cnt ; i ++)
	{
		if(belong[i] == 1) flag[i] = 0;
		else flag[i] = 1;//标记是具有A性质还是B性质 
	}
	int lasti = 1;//上一个A块 
	for(int i = 2 ; i <= cnt ; i ++)
	{
		if(flag[i]) 
		{
			to[lasti] = i;
			int num = i - lasti - 1;
			if(num % 2 == 1) ans = max(ans , num / 2 + 1);//因为两个块都要扩散，所以直接用两两之间的块的长度除以2向上取整 
			else ans = max(ans , num / 2);
			lasti = i;
		}
	}
	to[cnt] = cnt + 1;//特判，不然下面就直接死循环了 
	printf("%d\n",ans);
	int i = 1;
	while(i <= cnt)
	{
		int To = to[i] , num = To - i - 1;
		for(int j = 1 ; j <= belong[i] ; j ++) printf("%d ",Whi[i]);//展开 
		if(num % 2 == 1) for(int j = 1 ; j <= num ; j ++) printf("%d ",Whi[i]);//如果相距奇数个单位的话说明两个块都是一个元素，因为两两块之间的元素是相异的 
		else
		{
			for(int j = 1 ; j <= num / 2 ; j ++) printf("%d ",Whi[i]);
			for(int j = 1 ; j <= num / 2 ; j ++) printf("%d ",Whi[To]);
		}
		i = To;
	}
	return 0;
}
```