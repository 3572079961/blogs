# CF763E Timofey and our friends animals 题解

### 思路

不修改只查询，数据范围 $10^5$，时限 $7s$，于是想到莫队。

题目保证每条边 $(u,v)$ 有 $\left |u-v  \right | \le5$，所以每个点连的边数最多 $5$，于是莫队扩展一个点便可以暴力连边。

求联通块的数量可以用并查集，复杂度多个 $\log$，总共 $\operatorname{O}(n\log (n) \sqrt n)$，在时限 $7s$ 的情况下非常合理。

~~于是基本上这题就完了。~~

### 实现细节

考虑到莫队要能加能删，然而路径压缩并查集又不好删，所以干脆直接回滚莫队，只加不删。

这里细说一下回滚莫队的细节处理：

`fa` 数组就是并查集用的数组。

令 $upd$ 为 $l$ 所在块的右端点。

- 对于 $l,r$ 在一个块中的，直接把整个块的 `fa` 数组初始化，然后暴力。

- 对于 $l,r$ 不在一个块中的，明显不能每次都把当前已经处理了 $upd+1 \sim r$ 的 `fa` 数组复制到一个新数组中再处理 $l\sim upd$，于是只能用可撤销并查集，每次直接在原数组上改，计算完后再回退到只有 $upd+1\sim r$ 的情况。

~~所以还是得可持久化。~~

### 代码

具体实现见注释。

```cpp
#include<bits/stdc++.h>
#define endl '\n'
using namespace std;
int n,K,m,Q;
int pos[100005],fa[100005],sz[100005];
vector<int> el[100005],er[100005];
struct que{int l,r,id,ans;}q[100005];
int find(int x){
	while(fa[x]!=x) x=fa[x];
	return x;
}
stack<pair<int,int>> ss;
int merge(int x,int y){
	int fx=find(x),fy=find(y);
	if(fx==fy) return 0;
	if(sz[fx]>sz[fy]) swap(fx,fy);
	ss.push({fx,fy});
	fa[fx]=fy,sz[fy]+=sz[fx];
	return 1;
}
//并查集撤销
void del(int lst){
	while(ss.size()>lst){
		int fx=ss.top().first,fy=ss.top().second;
		fa[fx]=fx,sz[fy]-=sz[fx]; ss.pop();
	}
}
bool cmp_r(que a,que b){
	if(pos[a.l]==pos[b.l]) return a.r<b.r;
	return pos[a.l]<pos[b.l];
}
bool cmp_id(que a,que b){return a.id<b.id;}
signed main(){
	ios::sync_with_stdio(0),cin.tie(0),cout.tie(0);
	cin>>n>>K>>m;
	for(int i=1;i<=m;i++){
		int x,y; cin>>x>>y;
		if(x>y) swap(x,y);
		//记录每个点向左和右连的边，少点枚举常数 
		el[x].push_back(y),er[y].push_back(x);
	}
	cin>>Q;
	for(int i=1;i<=Q;i++){
		cin>>q[i].l>>q[i].r;
		q[i].id=i;
	}
	int len=sqrt(n);
	for(int i=1;i<=n;i++) pos[i]=(i-1)/len+1;
	sort(q+1,q+Q+1,cmp_r);
	//now记录的是当前将r更新到了哪里 
	int pk=0,now=0,nowans=0;
	for(int i=1;i<=Q;i++){
		//L,R就是l所在块的左右端点 
		int id=pos[q[i].l],L=(id-1)*len+1,R=id*len;
		//l的块和前一个不一样，重置fa数组 
		if(pos[q[i].l]!=pos[q[i-1].l]) pk=0;
		if(L<=q[i].l && q[i].r<=R){ //同块，暴力 
			int cnt=0; while(ss.size()) ss.pop();
			for(int j=q[i].l;j<=q[i].r;j++) fa[j]=j,sz[j]=1;
			for(int j=q[i].l;j<=q[i].r;j++){
				cnt++;
				for(int to:er[j]){
					if(q[i].l<=to) cnt-=merge(to,j);
				}
			}
			q[i].ans=cnt;
			continue;
		}
		if(!pk){ //新块，重置fa，更新now 
			nowans=0; while(ss.size()) ss.pop();
			for(int j=1;j<=n;j++) fa[j]=j,sz[j]=1;
			for(int j=R+1;j<=q[i].r;j++){
				nowans++;
				for(int to:er[j]){
					if(R<to) nowans-=merge(to,j);
				}
			}
			pk=1,now=q[i].r+1;
		}
		//将now更新到当前的r 
		while(now<=q[i].r){
			nowans++;
			for(int to:er[now]){
				if(R<to) nowans-=merge(to,now);
			}
			now++;
		}
		int cnt=nowans,lst=ss.size();
		//加上l到R的边 
		for(int j=R;j>=q[i].l;j--){
			cnt++;
			for(int to:el[j]){
				if(to<now) cnt-=merge(j,to);
			}
		}
		q[i].ans=cnt,del(lst); //回退 
	}
	sort(q+1,q+Q+1,cmp_id);
	for(int i=1;i<=Q;i++) cout<<q[i].ans<<endl;
	return 0;
}
```

码风不好见谅 /kk