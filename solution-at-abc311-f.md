# [ABC311F] Yet Another Grid Task 题解

朴素的题目。

### 分析

根据题意，我们可以把原矩阵一些白格子涂黑来使它合法，然后在这个合法的矩阵上面再涂黑一些格子来构造新的合法矩阵，令这个合法的矩阵为 $a$。

设 $h_i=\sum_{j=1}^{n}[a_{j,i}=1],(i\in[1,m])$，即 $i$ 这一列上黑点的数量，容易发现在这一列上，所有的黑点都是从下到上连续的，那么我们要做的就是通过确定每一列填完后的黑点数量（高度）来计数。

根据合法矩阵的性质，容易发现对于相邻的两列 $x,y$，它们填完的高度 $h\rq_x,h\rq_y$ 一定满足 $h\rq_x\le h\rq_y+1$，考虑 dp，设 $f_{i,j}$ 表示第 $i$ 列填完后的高度是 $j$，前 $i$ 列的合法方案数，于是可以写出朴素的转移：

$$f_{i,j}\longleftarrow \sum_{k=0}^{j+1} f_{i-1,k}$$

其中 $i\in[1,m],j\in[a_i,n]$，因为你不能删掉黑点。

答案即为 $\sum_{i=h_m}^{n} f_{m,i}$。

然后很明显这个可以前缀和优化，于是就完了，时间复杂度 $\operatorname{O}(nm)$。

### 代码

```cpp
#include<bits/stdc++.h>
#define endl '\n'
#define int long long
using namespace std;
const int mod=998244353;
int n,m;
int a[2005][2005],h[2005],vst[2005][2005],f[2005][2005],g[2005][2005];
void col(int x,int y){
	a[x][y]=vst[x][y]=1;
	if(x+1<=n && !vst[x+1][y]) col(x+1,y);
	if(x+1<=n && y+1<=m && !vst[x+1][y+1]) col(x+1,y+1);
}
signed main(){
	ios::sync_with_stdio(0),cin.tie(0),cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<=n;i++){
		for(int j=1;j<=m;j++){
			char c; cin>>c;
			a[i][j]=(c=='#');
		}
	}
	for(int i=1;i<=n;i++){
		for(int j=1;j<=m;j++){
			//将原矩阵补全为合法 
			if(a[i][j] && !vst[i][j]) vst[i][j]=1,col(i,j);
		}
	}
	for(int i=1;i<=n;i++){
		//统计每一列的高度h[i] 
		for(int j=1;j<=m;j++) if(a[i][j]) h[j]++;
	}
	f[0][0]=g[0][0]=1;
	for(int i=1;i<=n;i++) g[0][i]=(g[0][i-1]+f[0][i])%mod;
	for(int i=1;i<=m;i++){
		for(int j=h[i];j<=n;j++) f[i][j]=g[i-1][min(j+1,n)];
		g[i][0]=f[i][0];
		//前缀和优化 
		for(int j=1;j<=n;j++) g[i][j]=(g[i][j-1]+f[i][j])%mod;
	}
	int ans=0;
	for(int i=h[m];i<=n;i++) ans=(ans+f[m][i])%mod;
	cout<<ans<<endl;
	return 0;
}
```