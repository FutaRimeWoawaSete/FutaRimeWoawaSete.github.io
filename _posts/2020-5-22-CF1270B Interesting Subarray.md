这道题刚开始被我想复杂了，就随手胡了个性质也就是 $\min$ 和 $\max$ 都是在这个子序列的两端的，否则我们可以剔除多余的元素，然后就有手胡了棵区间加区间查 $\max$ 的线段树，然后就调了一个小时……   

调崩溃了后就果断继续口胡更深的性质：如果两两之间没有一个可以满足题目要求，题目中就没有满足题目要求的连续子序列了。   

我们就假装模拟一下，假设现在 $a$ 数组里有三个元素，$a_1 , a_2 , a_3$，如果此时 $abs(a_1 - a_2) \leq 1$ , $abs(a_2 - a_3) \leq 1$ ，那么说明这三个数里面的最大值减最小值一定小于等于 $2$ ，即证明此时所有长度为 $3$ 的子序列不合法，推广到所有形式中，进行数学归纳证明后亦可得证此时所有元素不合法。   

```cpp
#include<iostream>
#include<cstdio>
#include<cmath>
using namespace std;
int n,x[2],a,b;
int main()
{
	int T;
	scanf("%d",&T);
	while(T --)
	{
		bool flag = false;
		scanf("%d",&n);
		for(int i = 1 ; i <= n ; i ++)
		{
			scanf("%d",&x[i % 2]);
			if(i == 1) continue;
			if(abs(x[1] - x[0]) > 1)
			{
				a = i - 1 , b = i;
				flag = true;
			}
		}
		if(flag) printf("YES\n%d %d\n" , a , b);
		if(!flag) printf("NO\n");
	}
	return 0;
}
```