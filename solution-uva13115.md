# UVA13115  Sudoku 题解

## Part 1 : 题目分析

题目的大概意思是让我们验证一个数独是不是正确的,很容易就可以想到非常暴力的模拟算法.

根据题目描述,一个正确的数独共有三个条件:

$1$ . 每一行的数均不相同.

$2$ . 每一列的数均不相同.

$3$ . 每一个边长为 $\sqrt{n}$ 的小正方形中的数等均不相同.

题目让我们验证的是数的不同,所以正常来说应该乖乖按照题意用桶进行比较.

但是,有没有更简单的方法呢 ?

根据题意,这个数独中出现的数字都是 $1$ ~ $n$ 中的,也就是说,如果这个数独是正确的,那么每一行,每一列,每一个边长为 $\sqrt{n}$ 的小正方形中的数的和都是相同的一个数.

而且这个数也是可以求出来的,也就是 $1,2,3,4,...,n$ 的和.

那么这个问题就被简化成了验证每一行,每一列,每一个边长为 $\sqrt{n}$ 的小正方形中的数的和是否都等于 $1$ ~ $n$ 的和.

## Part 2 : 预处理

预处理就是把 $1$ ~ $n$ 的和求出来.

```cpp
int t=0; //用t来储存1-n的和
for(int i=1;i<=n;i++) t+=i; //循环累加
```
## Part 3 : 运算

分别对每一行,每一列,每一个边长为 $\sqrt{n}$ 的小正方形中的数进行累加,并判断是否等于 $1$ ~ $n$ 的和.

这里我用了一个 ``` goto ``` 来进行程序的跳转,如果有不懂的可以点击[这里](https://www.runoob.com/cplusplus/cpp-goto-statement.html)来详细了解.

每一行:

```cpp
for(int i=1;i<=n;i++){
	int sum=0; //用sum来储存累加的值
	for(int j=1;j<=n;j++) sum+=a[i][j]; //循环累加
	if(sum!=t){ //如果当前累加的值不等于1-n的和,则输出no,并跳转到结尾处,不继续执行后面的判断
		printf("no\n");
		goto jump;
	}
}
```

每一列的方法与每一行相同:

```cpp
for(int i=1;i<=n;i++){
	int sum=0;
	for(int j=1;j<=n;j++) sum+=a[j][i]; //这里的下标需要反过来
	if(sum!=t){
		printf("no\n");
		goto jump;
	}
}
```

接下来就是每一个边长为 $\sqrt{n}$ 的小正方形了:

用二重循环枚举每一个边长为 $\sqrt{n}$ 的小正方形:

```cpp
for(int i=1;i<=n;i=i+sqrt(n)) //注意这里不是i++
	for(int j=1;j<=n;j=j+sqrt(n)) //这里也一样
```

里面再用二重循环累加小正方形中的数的和:

```cpp
int sum=0;
for(int x=i;x<=i+sqrt(n)-1;x++){ //注意不能从i枚举到i+sqrt(n),因为x=i+sqrt(n)的那一行属于另一个小正方形
	for(int y=j;y<=j+sqrt(n)-1;y++){ //这里也一样
		sum+=a[x][y];
	}
}
```

最后再判断:

```cpp
if(sum!=t){
	printf("no\n");
	goto jump;
} //注意这一部分也应该放在枚举小正方形的二重循环里的
```

如果上述三个条件都满足,那么这就是一个正确的数独:

```cpp
printf("yes\n"); //记得换行
```

最后再把前面的 ``` jump ``` 在后面声明一下.

```cpp
jump:continue;
```

## Part 4 : 代码

记得加上多组数据的循环

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#include<cstring>
using namespace std;
int n,t;
int a[100][100];
int main(){
	scanf("%d",&t);
	while(t--){
		scanf("%d",&n);
		int t=0; //用t来储存1-n的和
		for(int i=1;i<=n;i++) t+=i; //循环累加
		for(int i=1;i<=n;i++){
			for(int j=1;j<=n;j++){
				scanf("%d",&a[i][j]);
			}
		}
		for(int i=1;i<=n;i++){
			int sum=0; //用sum来储存累加的值
			for(int j=1;j<=n;j++) sum+=a[i][j]; //循环累加
			if(sum!=t){ //如果当前累加的值不等于1-n的和,则输出no,并跳转到结尾处,不继续执行后面的判断
				printf("no\n");
				goto jump;
			}
		}
		for(int i=1;i<=n;i++){
			int sum=0;
			for(int j=1;j<=n;j++) sum+=a[j][i]; //这里的下标需要反过来
			if(sum!=t){
				printf("no\n");
				goto jump;
			}
		}
		for(int i=1;i<=n;i=i+sqrt(n)){ //注意这里不是i++
			for(int j=1;j<=n;j=j+sqrt(n)){ //这里也一样
				int sum=0;
				for(int x=i;x<=i+sqrt(n)-1;x++){ //注意不能从i枚举到i+sqrt(n),因为x=i+sqrt(n)的那一行属于另一个小正方形
					for(int y=j;y<=j+sqrt(n)-1;y++){ //这里也一样
						sum+=a[x][y];
					}
				}
				if(sum!=t){
					printf("no\n");
					goto jump;
				} //注意这一部分也应该放在枚举小正方形的二重循环里的
			}
		}
		printf("yes\n"); //记得换行
		jump:continue;
	}
	return 0;
}
```
