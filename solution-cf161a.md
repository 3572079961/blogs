# CF161A Dress'em in Vests! 题解

## Part 1：题目分析
题目的意思是让我们匹配最多的 $b_i$，最优的情况就是把每一个可以匹配的 $a_i$ 都匹配它最小的 $b_i$，这样就可以将更多的 $b_i$ 匹配上，且不影响之前的匹配。

## Part 2：题目求解
按照 Part 1 的思路，我们应该将 $a$ 数组和 $b$ 数组排序，这样才能保证每一个 $a_i$ 往后的第一个能匹配上的 $b_i$ 是最小的。

但题目给出的 $a$ 数组和 $b$ 数组是单调递增的，所以不需要我们自己排序。

接下来便是进行计算了。

我使用了两个下标指针 $pa$ 和 $pb$，分别表示当前匹配到的 $a$ 数组和 $b$ 数组的位置。

类似于单调队列的方法，每次找到一个 $a_{pa}$，就应该把不能匹配它的 $b_{pb}$ 扔掉。

```cpp
while(b[pb]<a[pa]-x) pb++;
```

现在相当于我们已经找到了一个合适的 $pa$ 和 $pb$，如果它们可以匹配，就应该将它们记录下来。

我用了一个结构体来记录答案。

```cpp
if(pa<=n && pb<=m && b[pb]>=a[pa]-x && b[pb]<=a[pa]+y){ //可以匹配
    ans[++cnt]=(node){pa,pb}; //记录答案
    pb++; //将b数组的指针向后移一位，表示这个b[pb]已经匹配过了
}
```
最后，不管这个 $a_{pa}$ 有没有匹配上，都要把 $pa$ 向后移一位，否则就会影响已经匹配的 $pa$。
```cpp
pa++;
```
## Part 3：Code
```cpp
#include<bits/stdc++.h>
#define maxn 100005
using namespace std;
struct node{int x,y;}ans[maxn];
int n,m,cnt;
int x,y;
int a[maxn],b[maxn];
int main(){
    scanf("%d %d %d %d",&n,&m,&x,&y);
    for(int i=1;i<=n;i++) scanf("%d",a+i);
    for(int i=1;i<=m;i++) scanf("%d",b+i);
    int pa=1,pb=1;
    while(pa<=n && pb<=m){
        while(b[pb]<a[pa]-x) pb++;
        if(pa<=n && pb<=m && b[pb]>=a[pa]-x && b[pb]<=a[pa]+y){ //可以匹配
            ans[++cnt]=(node){pa,pb}; //记录答案
            pb++; //将b数组的指针向后移一位，表示这个b[pb]已经匹配过了
        }
        pa++;
    }
    printf("%d\n",cnt);
    for(int i=1;i<=cnt;i++) printf("%d %d\n",ans[i].x,ans[i].y);
    getchar(); getchar();
    return 0;
}
```
