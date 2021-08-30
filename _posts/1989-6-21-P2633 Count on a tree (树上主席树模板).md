~~其实也没有什么模板不模板可言的。~~   

主席树的一大主要思想就是把一棵树当一个树，并且运用前缀和“减”出一个区间。    

同样运用前缀和的还有树上差分，将主席树搬到树上其实也就是把我们树上差分给改成主席树，然后就完了。    

PS：这里写前缀减法的时候一定要注意这不是边差分，是点差分……不然你就会像我一样像个 SB RE 半天……    

~~滚回去做网络流了~~   

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
using namespace std;
#define int long long
const int Len = 1e5 + 5;
int cnts,n,q,dp[Len][25],rt[Len],top,lsh[Len],dep[Len],cnt,head[Len],w[Len];
struct node
{
	int next,to;
}edge[Len << 1];
void add(int from,int to){edge[++ cnt].to = to ; edge[cnt].next = head[from] ; head[from] = cnt;}
struct Node
{
	int l,r,sum;
}tree[Len * 22];
int clone(int p)
{
	top ++;
	tree[top] = tree[p];
	tree[top].sum ++;
	return top; 
}
int build(int p,int l,int r)
{
	p = ++ top;
	if(l == r) return p;
	int mid = (l + r) >> 1;
	tree[p].l = build(tree[p].l , l , mid);
	tree[p].r = build(tree[p].r , mid + 1 , r);
	return p;
}
int update(int p,int l,int r,int wh)
{
	p = clone(p);
	if(l == r) return p;
	int mid = (l + r) >> 1;
	if(wh <= mid) tree[p].l = update(tree[p].l , l , mid , wh);
	else tree[p].r = update(tree[p].r , mid + 1 , r , wh);
	return p;
}
int query(int l,int r,int u,int v,int LCA,int LLCA,int rk)
{
	if(l == r) return l;
	int mid = (l + r) >> 1 , Sum = tree[tree[u].l].sum + tree[tree[v].l].sum - tree[tree[LCA].l].sum - tree[tree[LLCA].l].sum;
	if(rk <= Sum) return query(l , mid , tree[u].l , tree[v].l , tree[LCA].l , tree[LLCA].l , rk);
	else return query(mid + 1 , r , tree[u].r , tree[v].r , tree[LCA].r , tree[LLCA].r , rk - Sum);
}
void dfs(int x,int f)
{
	dep[x] = dep[f] + 1;
	dp[x][0] = f;
	for(int i = 1 ; (1 << i) <= dep[x] ; i ++) dp[x][i] = dp[dp[x][i - 1]][i - 1];
	for(int e = head[x] ; e ; e = edge[e].next)
	{
		int to = edge[e].to;
		if(to == f) continue;
		int Ins = lower_bound(lsh + 1 , lsh + 1 + cnts , w[to]) - lsh;
		rt[to] = update(rt[x] , 1 , cnts , Ins);
		dfs(to , x);
	}
}
int Find_LCA(int x,int y)
{
	if(dep[x] < dep[y]) swap(x , y);
	for(int i = 20 ; i >= 0 ; i --) if(dep[dp[x][i]] >= dep[y]) x = dp[x][i];
	if(x == y) return x;
	for(int i = 20 ; i >= 0 ; i --)
	{
		if(dp[x][i] != dp[y][i])
		{
			x = dp[x][i];
			y = dp[y][i];
		}
	}
	return dp[x][0];
}
signed main()
{
	scanf("%lld %lld",&n,&q);
	for(int i = 1 ; i <= n ; i ++) 
	{
		scanf("%lld",&w[i]);
		lsh[i] = w[i];
	}
	sort(lsh + 1 , lsh + 1 + n);
	cnts = unique(lsh + 1 , lsh + 1 + n) - lsh - 1;
	rt[0] = build(0 , 1 , cnts);
	int Ins = lower_bound(lsh + 1 , lsh + 1 + cnts , w[1]) - lsh;
	rt[1] = update(rt[0] , 1 , cnts , Ins);
	for(int i = 1 ; i < n ; i ++)
	{
		int x,y;scanf("%lld %lld",&x,&y);
		add(x , y) , add(y , x);
	}
	dfs(1 , 0);
	int lastans = 0;
	while(q --)
	{
		int x,y,k;scanf("%lld %lld %lld",&x,&y,&k);
		x = x ^ lastans;
		int LCA = Find_LCA(x , y);
		lastans = lsh[query(1 , cnts , rt[x] , rt[y] , rt[LCA] , rt[dp[LCA][0]] , k)];
		printf("%lld\n",lastans);
	}
	return 0;
}
```