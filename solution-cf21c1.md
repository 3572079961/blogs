# CF21C Stripe 2 题解

## Part 1：题意分析
题目让我们将序列划分为恰好连续的三段，使得每一段的和都相等。

也就是说，在这个序列当中找到两条分割线，使分开的三段的和相等。


------------

## Part 2：预处理
很容易联想到前缀和：
```cpp
for(int i=1;i<=n;i++) sum[i]=sum[i-1]+a[i];
```
$sum[i]$ 表示从 $1$ 到 $i$ 所有数的和。

根据题意，每一段的和就等于 $1$ 到 $n$ 所有数的和的 $\dfrac{1}{3}$，如果总和不能被三整除，就可以直接输出 $0$ ，结束程序。
```cpp
if(sum[n]%3!=0){
	printf("0");
	return 0;
}
```


------------

## Part 3：运算

因为已知每一段的和，所以可以直接枚举每一段。

枚举第一段，然后用变量 $cnt$ 来记录第一段的可能的个数（ $tot$ 就是 $1$ 到  $n$ 的和）：

```cpp
for(int i=1;i<n;i++) if(sum[i]==tot) cnt++;
```
再在枚举的同时判断前缀和是否已经达到两段的和，如果达到，便可以将第一段的答案个数 $cnt$ 累加到最终答案 $ans$ 里：
```cpp
for(int i=1;i<n;i++){
	if(sum[i]==tot*2) ans+=cnt;
	if(sum[i]==tot) cnt++;
}
```
在这里有一个小小的优化， $n$ 为分割线在此题中是不存在的，因为如果 $n$ 为分割线，则会有一段为空，这种情况肯定是不存在的，所以可以舍掉。


------------

## Part 4：代码

记得开 long long !

```cpp
#include<iostream>
#include<cstdio>
using namespace std;
long long n;
long long a[100005],sum[100005]; //1<=n<=10^5
int main(){
	scanf("%lld",&n); //开long long时，记得修改占位符 
	for(long long i=1;i<=n;i++){
		scanf("%lld",&a[i]);
		sum[i]=sum[i-1]+a[i]; //用sum数组储存前缀和 
	}
	//如果总和不能被三整除，则可以直接输出0，结束程序 
	if(sum[n]%3!=0){
		printf("0");
		return 0;
	} 
	long long tot=sum[n]/3,ans=0,cnt=0,t=0; //在主函数里定义要记得清零
	for(long long i=1;i<n;i++){ //n位置不能为分割线 
		if(sum[i]==tot*2) ans+=cnt; //判断第二条分割线，并累加答案 
		if(sum[i]==tot) cnt++; //判断第一条分割线，累计情况数 
	}
	//输出答案，结束程序 
	printf("%lld",ans); 
	return 0;
}
```
