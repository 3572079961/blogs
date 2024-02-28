# P1987 摇钱树 题解

## Part 1：题目分析
这道题看起来像一个背包，但不同的是每一个物品的价值每天都会减少。

先来看看普通的 01 背包。由于物品的价值不会减少，所以对于一个物品，先取还是后取没有区别，所以可以直接从 $1\sim n$ 枚举物品，然后按照剩余的背包容量舍去即可。

在这道题中，每一棵摇钱树都可以看成一个物品，它在每一天的金币数量就是它的价值（会减少）。所以总的天数（$k$）就可以看成背包容量。并且由于每一棵摇钱树只能被砍一次，所以这是一个 01 背包。

由于每一个物品的价值都会减少，所以我们可以采取一种贪心的思想，将物品每天减少的价值**从大到小**排序，再跑 01 背包即可。

这种贪心的正确性也很好证明，01 背包对物品的取舍并不受物品顺序的影响，也就是说，如果这个选择一个物品会使结果增加，那么无论它在 $1\sim n$ 的哪一个位置都不会受到影响。

## Part 2：题目求解
由于我们要将这 $n$ 个物品按照每天减少的价值排序，所以最好的选择便是结构体。
```cpp
struct node{int m,b;}t[maxn];
bool cmp(node a,node b){return a.b>b.b;} //重定义sort中的cmp函数
```
接下来便是 01 背包的过程了。

与一般的 01 背包不同的是，选择一个物品所得到的价值并不是这个物品的价值。因为当我们在第 $j$ 天选择这个物品时，这个物品已经减少了 $j-1$ 天的价值，所以得到的价值应该是这个物品的价值减去已经减少的价值。

状态转移方程应该是：

$$f_{i,j}=\max \begin{Bmatrix} f_{i-1,j}
 \\
f_{i-1,j-1}+m_i-b_i\times(j-1)
\end{Bmatrix} $$

```cpp
//滚动数组优化
for(int i=1;i<=n;i++) //枚举物品
	for(int j=k;j>0;j--) //枚举剩余背包容量
		f[j]=max(f[j],f[j-1]+t[i].m-t[i].b*(j-1)); //转移
```
在这里 $f_j$ 初始化为 $0$，所以并不需要考虑 $f_{i-1,j-1}+m_i-b_i\times(j-1)$ 小于 $0$ 的情况。

还有一点，我们不一定要把 $k$ 天全部用完，所以最后需要枚举 $f_0\sim f_k$，求出最大值。

```cpp
int ans=0;
for(int i=0;i<=k;i++) ans=max(ans,f[i]); //更新最大值
```
## Part 3：Code
多组数据，记得清零数组！

```cpp
#include<bits/stdc++.h>
#define maxn 10005
using namespace std;
struct node{int m,b;}t[maxn];
int n,k;
int f[maxn];
bool cmp(node a,node b){return a.b>b.b;} //重定义sort中的cmp函数
int main(){
	while(~scanf("%d %d",&n,&k) and n and k){
		for(int i=1;i<=n;i++) scanf("%d",&t[i].m);
		for(int i=1;i<=n;i++) scanf("%d",&t[i].b);
		sort(t+1,t+n+1,cmp); //排序
		memset(f,0,sizeof(f));
		for(int i=1;i<=n;i++) //枚举物品
			for(int j=k;j>0;j--) //枚举剩余背包容量
				f[j]=max(f[j],f[j-1]+t[i].m-t[i].b*(j-1)); //转移
		int ans=0;
		for(int i=0;i<=k;i++) ans=max(ans,f[i]); //更新最大值
		printf("%d\n",ans);
	}
	return 0;
}
```
