~~既然不能交审核就少写点~~  

整体二分板子练习题。  

离散化的时候记得把后面修改后的数也全都离散进去，把数组大小给开到 $n + m$ 大小。  

然后就是询问的 $id$ 需要单独再开 $tot$ 记录。  

修改的时候顺便要赋值。  

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
using namespace std;
const int Len = 2e5 + 5;
int len,n,m,c[Len],a[Len],lsh[Len],cnts,cnt,ans[Len],tot;
struct Query
{
	int l,r,k,opt,id;
}q[Len << 1] , q1[Len << 1] , q2[Len << 1];
int lowbit(int x){return x & (-x);}
void add(int x,int v){while(x <= n) c[x] += v , x += lowbit(x);}
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
void Solve(int l,int r,int L,int R)
{
	if(L > R || l > r) return;
	if(l == r) 
	{
		for(int i = L ; i <= R ; i ++) if(q[i].opt == 2) ans[q[i].id] = lsh[l];
		return;
	}
	int mid = (l + r) >> 1 , cnt1 = 0 , cnt2 = 0;
	for(int i = L ; i <= R ; i ++)
	{
		if(q[i].opt == 1)
		{
			if(q[i].l <= lsh[mid]) q1[++ cnt1] = q[i] , add(q[i].id , q[i].r);
			else q2[++ cnt2] = q[i];
		}
		else
		{
			int tmp = query(q[i].r) - query(q[i].l - 1);
			if(q[i].k <= tmp) q1[++ cnt1] = q[i];
			else q[i].k -= tmp , q2[++ cnt2] = q[i]; 
		}
	}
	for(int i = 1 ; i <= cnt1 ; i ++) if(q1[i].opt == 1) add(q1[i].id , -q1[i].r);
	for(int i = 1 ; i <= cnt1 ; i ++) q[L + i - 1] = q1[i];
	for(int i = 1 ; i <= cnt2 ; i ++) q[L + cnt1 + i - 1] = q2[i];
	Solve(l , mid , L , L + cnt1 - 1);
	Solve(mid + 1 , r , L + cnt1 , R);
}
char s[2];
int main()
{
	scanf("%d %d",&n,&m);
	len = n;
	for(int i = 1 ; i <= n ; i ++)
	{
		scanf("%d",&a[i]);
		lsh[i] = a[i];
		q[++ cnt] = (Query){a[i] , 1 , 0 , 1 , i};
	}
	for(int i = 1 ; i <= m ; i ++)
	{
		scanf("%s",s);
		int l,r,k;
		if(s[0] == 'Q')
		{
			scanf("%d %d %d",&l,&r,&k);
			q[++ cnt] = (Query){l , r , k , 2 , ++ tot};
		}
		else
		{
			scanf("%d %d",&l,&k);
			lsh[++ len] = k;
			q[++ cnt] = (Query){a[l] , -1 , 0 , 1 , l};
			q[++ cnt] = (Query){a[l] = k , 1 , 0 , 1 , l};
		}
	}
	sort(lsh + 1 , lsh + 1 + len);
	Solve(1 , len , 1 , cnt);
	for(int i = 1 ; i <= tot ; i ++) printf("%d\n",ans[i]);
	return 0;
}
```