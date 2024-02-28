# CF1696E Placing Jinas 题解

说是求最小，实际上是一到计数题。

## 解题思路
分析一下性质，很容易就会发现每一行的 $1$ 都是连续的，所以可以把每一行直接平推。

写个暴力打个表，把第二个样例输出一下，移动的时候不把 $cnt$ 清零。

```
  1   1   1   1   1   1   1   1   1   1   1   1   1
  1   2   3   4   5   6   7   8   9  10  11  12   0
  1   3   6  10  15  21  28  36  45  10  11   0   0
  1   4  10  20  35  56  84 120 120   0   0   0   0
  1   5  15  35  70 126 210 120   0   0   0   0   0
  1   6  21  56 126 252 252   0   0   0   0   0   0
  1   7  28  84 210 462 462   0   0   0   0   0   0
  1   8  36 120 330 792   0   0   0   0   0   0   0
  1   9  45 165 330   0   0   0   0   0   0   0   0
  1  10  55   0   0   0   0   0   0   0   0   0   0
  1  11   0   0   0   0   0   0   0   0   0   0   0
```
然后就会发现把这个东西斜着看就是一个杨辉三角。

其实也很好理解，因为对于一个点 $(i,j)$ 来说，在 $(i-1,j)$ 和 $(i,j-1)$ 往后推的时候会把 $cnt$ 加到这个点，所以 $cnt_{i,j}$ 就等于 $cnt_{i-1,j}+cnt{i,j-1}$，然后就成了杨辉三角。

接下来就简单了，根据杨辉三角第 $n$ 层 $m$ 个的值是 $\binom{n-1}{m-1}$，那么转过来变成矩形的 $n$ 行 $m$ 列就是 $\binom{n+m-2}{n-1} $。

所以对于每一行，产生的答案就是杨辉三角第 $i+1$ 行前 $a_i+1$ 个数的和，其实就是第 $i+2$ 行第 $a_i$ 个。

答案就是 $\sum\limits_{i-1}^{n} \binom{i+a_i}{i+1}$。

## 代码
注意题目给的下标是从 $0$ 开始的，其实没必要改，题目怎么给就怎么用就行了。

```cpp
#include<bits/stdc++.h>
#define maxn 500005
#define int long long
#define mod 1000000007
using namespace std;
int n;
int a[maxn],fac[maxn],inv[maxn];
int power(int x,int k){
	if(!k) return 1ll;
	int res=power(x*x%mod,k>>1);
	return res*(k&1?x:1ll)%mod;
}
void init(){
	fac[0]=1; for(int i=1;i<=500000;i++) fac[i]=fac[i-1]*i%mod;
	inv[0]=1; for(int i=1;i<=500000;i++) inv[i]=power(fac[i],mod-2);
}
int C(int n,int m){return n<m?0:fac[n]*inv[m]%mod*inv[n-m]%mod;}
signed main(){
	cin>>n; init();
	for(int i=0;i<=n;i++) cin>>a[i];
	int ans=0;
	for(int i=0;i<=n;i++) ans+=C(i+a[i],i+1),ans%=mod;
	cout<<ans<<endl;
	return 0;
}
```
