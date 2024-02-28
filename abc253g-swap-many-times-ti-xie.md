# [ABC253G] Swap Many Times 题解

## 思路

考虑如下的一个序列：

$$1,2,3,4,5$$

我们把 $l=1,r=2\sim5$ 的操作都执行一遍，则原序列变为：

$$5,1,2,3,4$$

可以发现 $1\sim4$ 每个数都向后移了一位，第 $5$ 个数移到了第一个位置。

再试着把 $l=2,r=3\sim5$ 的操作都执行一遍，原序列变为：

$$5,4,1,2,3$$

发现同样满足刚才的性质，把 $2\sim3$ 每个数向后移一位，$4$ 移到第 $2$ 个位置。

我们分析一下对于 $n=5,l=3,r=8$ 需要进行的操作：

$$(1,4),(1,5),(2,3),(2,4),(2,5),(3,4)$$

发现 $l=2$ 的操作是完整的，即 $r$ 取遍所有合法的数。所以不难想到一个简单的思路：把完整的操作一起处理，再进行不完整的操作即可。

## 实现

首先不难发现对于一个 $l$，合法的 $r$ 是 $l+1\sim n$，所以每一个 $l$，共有 $n-l$ 个合法的 $r$。

```cpp
//记录完整操作的前缀和，用来后续二分查找
for(int i=1;i<=n;i++) sum[i]=sum[i-1]+n-i;
```
- 找到完整操作之前与之后的位置

```cpp
int lpos=lower_bound(sum+1,sum+n+1,l)-sum;
int rpos=lower_bound(sum+1,sum+n+1,r)-sum;
```
- 处理不完整的操作

既然我们已经找到两段的 $l$ 了，而每段的个数是 $\Theta(n)$ 级别的，所以直接暴力交换就可以了。

```cpp
//前
for(int i=1;i<=sum[lpos]-l+1;i++) swap(a[lpos],a[lpos+l-sum[lpos-1]+i-1]);
//后
for(int i=1;i<=r-sum[rpos-1];i++) swap(a[rpos],a[rpos+i]);
```
但是这里有一个小问题，如果题目给出的 $l$ 和 $r$ 所对应的操作的 $l$ 是相同的，即在一个块里，两段就会出现重叠，所以需要特殊处理。

```cpp
//其实比较类似于分块算法的处理
if(sum[lpos]>=r){
	//暴力交换
	for(int i=l;i<=r;i++) swap(a[lpos],a[lpos+i-sum[lpos-1]]);
	for(int i=1;i<=n;i++) cout<<a[i]<<' '; cout<<endl;
	return 0;
}
```


- 处理完整的操作

这里发现一个问题，单个完整操作也是 $\Theta(n)$，总共 $\Theta(n^2)$，不能接受。

所以考虑把多个完整操作一起处理。

回到刚才的序列 $1\sim5$，把 $l=1\sim3$ 的完整操作都执行一遍：

$$1,2,3,4,5 \longrightarrow 5,1,2,3,4 \longrightarrow 5,4,1,2,3 \longrightarrow 5,4,3,1,2$$

可以发现进行 $l=p\sim q$ 的完整操作就是把原序列 $p\sim q-1$ 每个数向后移动 $n-q+1$ 位，然后将 $q\sim n$ 每个数倒序放进 $p\sim p+(n-q)$ 每个位置里。

$$1,2,3,4,5$$

$$?,?,?,?,? \longrightarrow ?,?,?,1,2 \longrightarrow 5,4,3,1,2$$

```cpp
//p=x,q=x+tot-1
int x=lpos+1,tot=rpos-1-lpos;
for(int i=x;i+tot<=n;i++) b[i+tot]=a[i];
for(int i=x;i<=x+tot-1;i++) b[i]=a[n-(i-x+1)+1];
for(int i=x;i<=n;i++) a[i]=b[i];
```
## 代码

注意 $l$ 和 $r$ 都是 $\Theta (n^2)$ 级别的，有可能爆 `int`，所以要开 `long long`。

```cpp
#include<bits/stdc++.h>
#define endl '\n'
#define int long long
using namespace std;
int n,l,r;
int a[200005],b[200005],sum[200005];
signed main(){
	ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n>>l>>r;
    for(int i=1;i<=n;i++) a[i]=i;
    for(int i=1;i<=n;i++) sum[i]=sum[i-1]+n-i;
    int lpos=lower_bound(sum+1,sum+n+1,l)-sum;
    int rpos=lower_bound(sum+1,sum+n+1,r)-sum;
    if(sum[lpos]>=r){
        for(int i=l;i<=r;i++) swap(a[lpos],a[lpos+i-sum[lpos-1]]);
        for(int i=1;i<=n;i++) cout<<a[i]<<' '; cout<<endl;
        return 0;
    }
    for(int i=1;i<=sum[lpos]-l+1;i++) swap(a[lpos],a[lpos+l-sum[lpos-1]+i-1]);
    int x=lpos+1,tot=rpos-1-lpos;
    for(int i=x;i+tot<=n;i++) b[i+tot]=a[i];
    for(int i=x;i<=x+tot-1;i++) b[i]=a[n-(i-x+1)+1];
    for(int i=x;i<=n;i++) a[i]=b[i];
    for(int i=1;i<=r-sum[rpos-1];i++) swap(a[rpos],a[rpos+i]);
    for(int i=1;i<=n;i++) cout<<a[i]<<' '; cout<<endl;
    return 0;
}
```
