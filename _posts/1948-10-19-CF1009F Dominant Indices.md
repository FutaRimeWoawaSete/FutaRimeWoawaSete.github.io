以为十二篇题解里面怎么也得有启发式合并老哥，没想到真的没有。    

按理来说这道题不是归于启发式合并的经典例题里面吗？    

首先启发式合并有点小卡，如果您追求于更快的做法不妨使用树上倍增。   

其次就讲解一些此题如何树上启发式合并，很明显这道题符合我们树上启发式合并的一些基本特征，即：    

```
1. 只具备查询操作；    
2. 只和子树有关；
```

所以考虑到了 DSU  ，我们直接先扫一遍把重儿子们都找出来，然后就是常规套路了，对于一棵子树递归过程中最后查询重儿子，此时我们获取了重儿子的情况并且要计算当前情况，然后我们就少查询了一次重儿子的情况。    

时间复杂度 $O(n \log n)$ 根据启发式合并而来这里不细讲。    

唯一注意的就是要考虑怎么初始化 $0$ 的情况，这里我直接没管直接再 $add$ 让它更新了一次，其他就没什么好说的了。     

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<map>
using namespace std;
const int Len = 1e6 + 5;
int Mx,Dis,n,m,Cnt[Len],head[Len],cnt,Print[Len],dep[Len],siz[Len],son[Len],Son;
struct node
{
	int next,to;
}edge[Len << 1];
void add(int from,int to)
{
	edge[++ cnt].to = to;
	edge[cnt].next = head[from];
	head[from] = cnt;
}
void dfs1(int x,int f)
{
	dep[x] = dep[f] + 1;
	siz[x] = 1;
	int maxson = -1;
	for(int e = head[x] ; e ; e = edge[e].next)
	{
		int to = edge[e].to;
		if(to == f) continue;
		dfs1(to , x);
		siz[x] += siz[to];
		if(siz[to] > maxson) maxson = siz[to] , son[x] = to; 
	}
}
void add(int x,int f,int val,int now)
{
	Cnt[dep[x]] += val;
	if(Cnt[dep[x]] > Mx || (Cnt[dep[x]] == Mx && dep[x] -  dep[now] < Dis - dep[now])) Mx = Cnt[dep[x]] , Dis = dep[x];
	for(int e = head[x] ; e ; e = edge[e].next)
	{
		int to = edge[e].to;
		if(to == f || to == Son) continue;
		add(to , x , val , now);
	} 
}
void dfs2(int x,int f,int opt)
{
	for(int e = head[x] ; e ; e = edge[e].next)
	{
		int to = edge[e].to;
		if(to == f || to == son[x]) continue;
		dfs2(to , x , 0);
	}
	if(son[x]) dfs2(son[x] , x , 1) , Son = son[x];
	add(x , f , 1 , x) , Son = 0;
	Print[x] = Dis;
	if(!opt) add(x , f , -1 , x) , Mx = Dis = 0;
}
int main()
{
	scanf("%d",&n);
	for(int i = 1 ; i < n ; i ++)
	{
		int x,y;scanf("%d %d",&x,&y);
		add(x , y) , add(y , x);
	}
	dfs1(1 , 0);
	dfs2(1 , 0 , 1);
	for(int i = 1 ; i <= n ; i ++) printf("%d\n",Print[i] - dep[i]);
	return 0;
}
```