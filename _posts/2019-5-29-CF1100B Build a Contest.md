这道题感觉大家的做法都有点复杂呢……  

这道题其实不必被蓝标签给吓到，仔细思考过后我们发现维护两个东西就行了，即 $tot_i$ 表示难度为 $i$ 的题目出现了几次，而 $cnt_i$ 表示出现了 $i$ 次的难度题目种数。  

题目说每当所有难度的题目都出现了 $1$ 次就输出 $1$ 并且所有 $tot - 1$，   

也就是说我们只要当前所有难度的题目应该都至少出现了几次，直接模拟就好了：  

```cpp
#include<iostream>
#include<cstdio>
#include<cmath>
#include<algorithm>
#include<cstring>
using namespace std;
const int Len = 2e5 + 5;
int cnt[Len],n,m,tot[Len],a[Len],idx;//idx表示当前所有难度的题目需要出现多少次
int main()
{
	scanf("%d %d",&m,&n);
	for(int i = 1 ; i <= n; i ++) scanf("%d",&a[i]);
	idx = 1;
	for(int i = 1 ; i <= n; i ++)
	{
		tot[a[i]] ++;
		cnt[tot[a[i]]] ++;//为当前出现了tot[a[i]]次的种数+1
		if(cnt[idx] == m) //如果所有难度的题目都出现了
		{
			printf("1");
			idx ++;
		}
		else printf("0");
	}
	return 0;
}
```