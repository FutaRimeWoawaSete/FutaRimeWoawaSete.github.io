个人感觉这道题还是有点稍难的。  

主要是往离线这方面想……  

首先我们发现这道题直接查询很难处理，于是刚开始我就卡在怎么查询，然后想了一会儿……  

不久就发现当我们知道一个点它距祖先的距离不就可以算出来他要查询的祖先节点是谁了吗？  

于是这道题就变得简单起来了。  

开一个 $cnt_x$ 数组记录一下在当前 DFS 查询中深度为 $x$ 的节点有多少个。  

add 函数就没什么好参考的了：  

```cpp
void Add(int x,int val)
{
	cnt[dep[x]] += val;
	for(int e = head[x] ; e ; e = edge[e].next)
	{
		int to = edge[e].to;
		if(to == Son) continue;
		Add(to , val);
	}
}
```
dfs1 就在中间跑一下倍增 LCA，其余的也是板子。

因为之前的工作都做好了， dfs2 也可以直接跑。  

所以这道题只要离线把每个询问~~爬树~~处理一下即可，也不是特别恶心。

由于原图是森林，我们就顺便预处理一波所有的 $root$ 就可以了。（找 $fa$ 为 $0$ 的点）

于是这道题就完了……  

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<vector>
using namespace std;
const int Len = 1e5 + 5;
int n,m,head[Len],cnts,cnt[Len << 2],dep[Len],siz[Len],son[Len],dp[Len][30],Son,ans[Len];
bool vis[Len];
vector<int> root;
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
	int depth,idx;
};
vector<Node> G[Len];
void Add(int x,int val)
{
	cnt[dep[x]] += val;
	for(int e = head[x] ; e ; e = edge[e].next)
	{
		int to = edge[e].to;
		if(to == Son) continue;
		Add(to , val);
	}
}
void dfs1(int x,int f)
{
	dep[x] = dep[f] + 1;
	siz[x] = 1;
	dp[x][0] =  f;
	vis[x] = true;
	int maxson = -1;
	for(int i = 1 ; (1 << i) <= dep[x] ; i ++) dp[x][i] = dp[dp[x][i - 1]][i - 1];
	for(int e = head[x] ; e ; e = edge[e].next)
	{
		int to = edge[e].to;
		dfs1(to , x);
		siz[x] += siz[to];
		if(maxson < siz[to]) maxson = siz[to] , son[x] = to;
	}
}
int Find(int x,int k)//爬树 
{
	for(int i = 20 ; i >= 0 ; i --) if((1 << i) <= k) k -= (1 << i) , x = dp[x][i];
	return x;
}
void dfs2(int x,int f,int opt)
{
	for(int e = head[x] ; e ; e = edge[e].next)
	{
		int to = edge[e].to;
		if(to == son[x]) continue;
		dfs2(to , x , 0);
	}	
	if(son[x]) dfs2(son[x] , x , 1) , Son = son[x];
	Add(x , 1) , Son = 0;
	for(int e = 0 ; e < G[x].size() ; e ++) ans[G[x][e].idx] = cnt[dep[x] + G[x][e].depth] - 1;
	if(!opt) Add(x , -1);
} 
int main()
{
	scanf("%d",&n);
	for(int i = 1 ; i <= n ; i ++)
	{
		int x;
		scanf("%d",&x);
		if(x == 0) root.push_back(i);
		else add(x , i);
	}
	for(int i = 0 ; i < root.size() ; i ++) dfs1(root[i] , 0);
	scanf("%d",&m);
	for(int i = 1 ; i <= m ; i ++)
	{
		int x,y;
		scanf("%d %d",&x,&y);
		int Fa = Find(x , y);
		if(Fa == 0) ans[i] = 0;
		else 
		{
			Node opt;
			opt.depth = y , opt.idx = i;
			G[Fa].push_back(opt);
		}
	}
	for(int i = 0 ; i < root.size() ; i ++) dfs2(root[i] , 0 , 0);
	for(int i = 1 ; i <= m ; i ++) printf("%d ",ans[i]);
	return 0;
}
```