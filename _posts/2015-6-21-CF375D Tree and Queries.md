~~今天刚学了 DSU 所以就写一篇 DSU 吧。~~  

此题我们首先转换一下概念，因为我们要求的为子树中出现次数大于等于 $k$ 的颜色个数，相当于就是求子树中出现次数至少为 $k$ 的颜色个数对吧。  

所以我们定义两个数组：  

```
sum[x] 表示在当前DFS统计中颜色出现次数至少为 x 的颜色种类数。  
cnt[x] 表示在当前DFS中颜色 x 的出现次数。  
```

于是 Add 函数仿照莫队的颜色统计，需要注意数组清零时一定要先减 sum 再减 cnt 。  


Add 函数就长这样：  

```cpp
void Add(int x,int f,int val)
{
	if(val == -1)
	{
		sum[cnt[col[x]]] += val;
		cnt[col[x]] += val;
	}
	else 
	{
		cnt[col[x]] += val;
		sum[cnt[col[x]]] += val;
	}
	for(int e = head[x] ; e ; e = edge[e].next)
	{
		int to = edge[e].to;
		if(to == Son || to == f) continue;
		Add(to , x , val);
	}
}
```
dfs1 和 dfs2 函数就照常写就好了。  

剩下的就是DSU板子了，代码如下：  

```cpp
#include<iostream>
#include<cstdio>
#include<cmath>
#include<cstring>
#include<vector>
using namespace std;
const int Len = 1e5 + 5;
int n,m,cnts,head[Len],cnt[Len],son[Len],siz[Len],ans[Len],sum[Len],col[Len],Son;
struct node
{
	int next,to;
}edge[Len << 1];
void add(int from,int to)
{
	edge[++ cnts].to = to;
	edge[cnts].next = head[from];
	head[from] = cnts;
}
struct Node
{
	int k,idx;
};
vector<Node> G[Len];
void Add(int x,int f,int val)
{
	if(val == -1)
	{
		sum[cnt[col[x]]] += val;
		cnt[col[x]] += val;
	}
	else 
	{
		cnt[col[x]] += val;
		sum[cnt[col[x]]] += val;
	}
	//printf("%d %d %d %d\n",x,col[x],cnt[col[x]],sum[cnt[col[x]]]); 
	for(int e = head[x] ; e ; e = edge[e].next)
	{
		int to = edge[e].to;
		if(to == Son || to == f) continue;
		Add(to , x , val);
	}
}
void dfs1(int x,int f)
{
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
void dfs2(int x,int f,int opt)
{
	for(int e = head[x] ; e ; e = edge[e].next)
	{
		int to = edge[e].to;
		if(to == f || to == son[x]) continue;
		dfs2(to , x , 0); 
	}
	if(son[x]) dfs2(son[x] , x , 1) , Son = son[x];
	Add(x , f , 1) , Son = 0;
	for(int e = 0 ; e < G[x].size() ; e ++) ans[G[x][e].idx] = sum[G[x][e].k];
	if(!opt) Add(x , f , -1);
}
int main()
{
	scanf("%d %d",&n,&m);
	for(int i = 1 ; i <= n ; i ++) scanf("%d",&col[i]);
	for(int i = 1 ; i < n ; i ++)
	{
		int x,y;
		scanf("%d %d",&x,&y);
		add(x , y) , add(y , x);
	}
	for(int i = 1 ; i <= m ; i ++)
	{
		int x,y;
		scanf("%d %d",&x,&y);
		Node opt;
		opt.k = y , opt.idx = i;
		G[x].push_back(opt);
	}
	dfs1(1 , 0);
	dfs2(1 , 0 , 1);
	for(int i = 1 ; i <= m ; i ++) printf("%d\n",ans[i]);
	return 0;
}
```