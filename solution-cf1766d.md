# CF1766D Lucky Chains 题解

### 废话

赛时过了的，结束时大概 400 名左右，应该是能上波分的。

结果第二天被同班同学叉了 /kk，写篇题解纪念一下。

### 求解

设最终得到的两个不互质的数为 $x+k$ 和 $y+k$，也就是说 $\gcd(x+k,y+k)\neq1$。

这个时候 $x+k$ 和 $y+k$ 都是变量，不好做，怎么办呢？

假设 $p\mid (x+k), p\mid (y+k)$，根据整除的性质可知 $p\mid [(y+k)-(x+k)]\to p\mid (y-x)$。

则我们得到的性质就是 $p\mid (x+k),p\mid (y-x)$。

现在我们的目标变成了找到一个最小的 $k$，使得 $\gcd(x+k,y-x)\neq1$。

很明显 $y-x$ 是不变的，那么问题就简单多了，直接枚举 $y-x$ 的因数然后求出 $k$ 取最小值就完事了。

那 $-1$ 的情况怎么判呢？其实就是满足 $\gcd(x+k,y-x)$ 始终为 $1$，那么合理的情况就只有 $y-x=1$。

### 关于时间复杂度

我们要对 $y-x$ 分解质因数，在预处理出素数的情况下是 $\log_2$ 的复杂度，然后本来是可以只筛到 $10^4$ 的，结果我赛时脑抽直接筛完了 $10^7$，然后还 `#define int long long`，结果寄了。

其实筛到 $10^7$ 是能过的，但是不能开 `long long`。（悲伤

### 代码

千万别 `#define int long long`！！！

另外，`cin,cout` 好像有点卡常，建议加个
```cpp
#define endl '\n'
ios::sync_with_stdio(false); cin.tie(0),cout.tie(0);
```
#### ~~不完整代码~~

```cpp
void getprime(){
    //质数筛，p存质数，vst=0表示是质数
}
void solve(){
    int a,b; cin>>a>>b;
    int k=b-a;
    if(k==1) return cout<<"-1"<<endl,void();
    vector<int> h;
    for(int x:p){
        if(k%x==0) h.push_back(x);
        while(k%x==0) k/=x;
        if(!vst[k] || k==1) break;
    }
    if(k!=1) h.push_back(k);
    int ans=1e9;
    for(int x:h){
        //⌈a/x⌉*x-a 等同于 a-a%x
        int res=ceill(1.0*a/x)*x-a;
        ans=min(ans,res);
    }
    cout<<ans<<endl;
}
```
