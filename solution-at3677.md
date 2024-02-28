# AT3677 [ABC079D] Wall 题解

## Part 1 : 题目分析

题目给出了一个二维数组和 $0$ ~ $9$ 中不同数字之间变化的花费，求将这个二维数组中的所有数字都变成 $1$ 所需要的最小花费的和。

在这道题中，从把一个数字变成另一个数字时，可以使用另外一个数字作为 “跳板”，不一定要直接转化，所以很容易就可以想到最短路径。

于是这道题就简化成了:

给出一个**无向图**的[邻接矩阵](https://baike.baidu.com/item/%E9%82%BB%E6%8E%A5%E7%9F%A9%E9%98%B5/9796080?fr=aladdin)，求所有点到编号为 $1$ 的点的最短路径的长度的和。

## Part 2 ： 图的储存

题目输入的是 $0$ ~ $9$ 中的数字互相转化的花费，但是如果我们将 $0$ ~ $9$ 中的数字都看作图的点，那么这些花费便可以当作边权储存。

```cpp
for(int i=0;i<=9;i++){ 
	for(int j=0;j<=9;j++){ //i,j分别枚举0-9中的数字,也就是这条边连接的两个节点
		scanf("%d",&dis[i][j]); //dis数组储存边权
	}
}
```

## Part 3 ： 运算

题目给出的数组中有 $1$ 和 $-1$，$1$ 代表这个数字不需要转化，所以不需要加在总和上，而 $-1$ 代表这个位置没有数字，所以可以直接变成 $1$，也不需要加在总和上。

因为这道题需要求出的不止是单源最短路径，而是任意两个点之间的最短路径，所以我选择了 [Floyd 算法](https://baike.baidu.com/item/Floyd%E7%AE%97%E6%B3%95)来计算最短路径。

```cpp
void floyd(){
	for(int k=0;k<=9;k++){ //枚举每一个点
		for(int i=0;i<=9;i++){
			for(int j=0;j<=9;j++){
				if(dis[i][k]!=INF and dis[k][j]!=INF) dis[i][j]=min(dis[i][j],dis[i][k]+dis[k][j]); //松弛操作
			}
		}
	}
}
```

注意：在使用 Floyd 算法计算最短路径时，$k$ , $i$ , $j$ 这三个变量在枚举时的顺序是固定的，因为 Floyd 算法的本质其实是动态规划，而 $k$ 枚举的是中间点，也就是状态，所以必须放在最外层。

最后再用一个 $ans$ 变量来累加最短路径的和。

```cpp
int ans=0;
for(int i=1;i<=h;i++){
	for(int j=1;j<=w;j++){
		scanf("%d",&x); //在输入时在线处理
		if(abs(x)==1) continue; //如果这个点是1或-1，则不需要加在ans上
		ans+=dis[x][1]; //累加x到1的最短路径
	}
}
```

## Part 4 ： 代码

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cstring>
#include<cmath>
#define INF 0x7fffffff/2 //int最大值
using namespace std;
int h,w;
int x,dis[15][15];
void floyd(){
	for(int k=0;k<=9;k++){ //枚举每一个点
		for(int i=0;i<=9;i++){
			for(int j=0;j<=9;j++){
				if(dis[i][k]!=INF and dis[k][j]!=INF) dis[i][j]=min(dis[i][j],dis[i][k]+dis[k][j]); //松弛操作
			}
		}
	}
}
int main(){
	scanf("%d%d",&h,&w);
	for(int i=0;i<=9;i++){
		for(int j=0;j<=9;j++){ //i,j分别枚举0-9中的数字,也就是这条边连接的两个节点
			scanf("%d",&dis[i][j]); //dis数组储存边权
		}
	}
	floyd();
	int ans=0;
	for(int i=1;i<=h;i++){
		for(int j=1;j<=w;j++){
			scanf("%d",&x); //在输入时在线处理
			if(abs(x)==1) continue; //如果这个点是1或-1，则不需要加在ans上
			ans+=dis[x][1]; //累加x到1的最短路径
		}
	}
	printf("%d",ans);
	return 0;
}
```
