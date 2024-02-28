# P4819 [中山市选]杀人游戏 题解

upd（2022/2/9）：经提醒，发现了一处代码错误，并进行了修改
## Part 1 ：题目分析
本题让我们求的是警察查证到杀手的最大概率。

假设他的查证对象一共有 $n$ 个，因为每一个人是杀手概率是相同的，所以警察每查证一个人，他查证到杀手的概率就会减去 $\dfrac{1}{n}$，如果不考虑人互相认识的话，总概率就是 $0$。

根据题意，有一些人认识其他人，所以只要警察查证一个人，则他认识的人就都不用查证了。

由于在本题中有环的存在，就是在一个集合中，所有人都互相认识，那么警察只需要查证其中的一个人，这整个集合中的所有人都会被查证。

由此我们便可以采用 Tarjan 算法来进行找环及缩点操作。

## Part 2 ：题目求解
Tarjan 缩点的部分不用多说，与正常的缩点没有什么区别。

只是在找到一个环 (```dfn[x]==low[x]```) 时，要将环上的所有点都染上色，并且用一个 ```sum``` 数组来储存这个强连通分量中点的个数。

也就是

```cpp
c[y]=cnt; sum[cnt]++; //cnt就是这个强连通分量的编号
```
所以整个 Tarjan 函数就写出来了。

```cpp
void tarjan(int x){
	dfn[x]=low[x]=++num;
	s.push(x); in[x]=1;
	for(int i=h[x];i;i=t[i].next){
		int y=t[i].to;
		if(!dfn[y]){
			tarjan(y);
			low[x]=min(low[x],low[y]);
		}else if(in[y]) low[x]=min(low[x],dfn[y]);
	}
	if(dfn[x]==low[x]){
		cnt++;
		int y;
		do{
			y=s.top(); s.pop(); in[y]=0;
			c[y]=cnt; sum[cnt]++;
		}while(x!=y);
	}
}
```
当缩完点后，我们就可以将新图建出来了。
```cpp
//感谢@lsroi的提醒，这里vst里面不能直接写t[i].to
void clear(int x){for(int i=h[x];i;i=t[i].next) vst[c[t[i].to]]=0;}
//下面这段代码在main函数中
for(int x=1;x<=n;x++){ //枚举每一个点
	for(int i=h[x];i;i=t[i].next){ //枚举每条出边
		int y=t[i].to;
		if(c[x]==c[y] or vst[c[y]]) continue; //如果相连的两个点在同一个强连通分量中，或者x所在的强连通分量与y所在的强连通分量已经建过边，则需要忽略这条边
		vst[c[y]]=1; //将y所在的强连通分量标记为已建过边
		addc(c[x],c[y]); //建边
		rd[c[y]]++; //统计每个强连通分量的入度
	}
	clear(x); //清空vst数组
}
```
这里用一个 ```vst``` 数组记录当前的 $x$ 是否已经与 $y$ 所在的强连通分量建过边，如果已经有边，就直接忽略，起到了去掉重边的作用。

接下来就是累加所有不被其他人认识的人了。

例如下面这种情况

![](https://cdn.luogu.com.cn/upload/image_hosting/7i8azmxc.png)

它缩完点后就可以变成

![](https://cdn.luogu.com.cn/upload/image_hosting/obugrl1z.png)

相当于 $1,2,3$，$4$，$5,6,7$，分别组成三个强连通分量。

那么我们只需要在 $1,2,3$ 这个强连通分量中随便找一个人进行查证就可以知道所有人的身份。

又因为选择一个人进行查证的概率是 $\dfrac{1}{n}$，所以最终的答案就是 $\dfrac{1}{n}$，也就是 $0.142857$。

不难发现，刚才那组数据中，我们统计的是入度为 $0$ 的强连通分量的个数，因为这些强连通分量中的人都没有除这个强连通分量以外的人认识，所以肯定需要警察找一个人进行查证。

但是，这样一来，就存在了一个问题，如果我们把其他所有人都查证完了，那么剩下的一个人就必定是杀手。

例如下面这个缩完点的图

![](https://cdn.luogu.com.cn/upload/image_hosting/58t1b1b4.png)

这个图中入度为 $0$ 的强连通分量有两个，但是经过检验可以发现，只要警察在 $1,2,3$ 中查证一个人，那么 $1,2,3,4,5,8,6,9,10$ 这些人的身份就全部知道了，那么最后一个点 $7$ 就没有必要查证了。

当然，这种情况只出现在最后一个强连通分量中只有一个点时。

由此，我们就可以增加一个判断条件。

当某一个强连通分量的入度为 $0$，只有一个人，并且他认识的所有人都有其他人认识时，这个人就不需要查证了。

```cpp
bool check(int x) {
	if (rd[x] or sum[x]!=1) return false; //入度为0并且只有一个点才继续判断
	for(int i=hc[x];i;i=tc[i].next) if(rd[tc[i].to]==1) return false; //必须要有其他人认识
	return true;
}
```
这样，我们就统计除了需要查证的人的个数。
```cpp
int ans=0;
for(int i=1;i<=cnt;i++) if(!rd[i]) ans++; //统计入度为0的强连通分量的个数
for(int i=1;i<=cnt;i++){
	if(check(i)){ //符合条件
		ans--;
		break; //这种情况只存在一次，所以如果找到了，就需要break
	}
}
```
最后的概率便是总点数减去需要查证的人的个数的差除以总点数的值。就是 $(n-ans)\div n$。
## Part 3 ：Code

```cpp
#include<bits/stdc++.h>
#define maxn 300005
using namespace std;
struct node{int to,next;};
node t[maxn*2],tc[maxn*2];
int n,m;
int tot,totc,num,cnt;
int h[maxn],hc[maxn],dfn[maxn],low[maxn],in[maxn],c[maxn],sum[maxn],vst[maxn],f[maxn],rd[maxn];
stack<int> s;
inline int read(){ //读入优化
	register int x=0;
	register char c=getchar();
	while(c<'0' or c>'9') c=getchar();
	while(c>='0' and c<='9') x=(x<<1)+(x<<3)+c-'0',c=getchar();
	return x;
}
inline void add(int x,int y){
	t[++tot].to=y;
	t[tot].next=h[x];
	h[x]=tot;
}
inline void addc(int x,int y){ //建新图
	tc[++totc].to=y;
	tc[totc].next=hc[x];
	hc[x]=totc;
}
void tarjan(int x){
	dfn[x]=low[x]=++num;
	s.push(x); in[x]=1;
	for(int i=h[x];i;i=t[i].next){
		int y=t[i].to;
		if(!dfn[y]){
			tarjan(y);
			low[x]=min(low[x],low[y]);
		}else if(in[y]) low[x]=min(low[x],dfn[y]);
	}
	if(dfn[x]==low[x]){
		cnt++;
		int y;
		do{
			y=s.top(); s.pop(); in[y]=0;
			c[y]=cnt; sum[cnt]++; //cnt就是这个强连通分量的编号
		}while(x!=y);
	}
}
//感谢@lsroi的提醒，这里vst里面不能直接写t[i].to
void clear(int x){for(int i=h[x];i;i=t[i].next) vst[c[t[i].to]]=0;}
bool check(int x){
	if (rd[x] or sum[x]!=1) return false; //入度为0并且只有一个点才继续判断
	for(int i=hc[x];i;i=tc[i].next) if(rd[tc[i].to]==1) return false; //必须要有其他人认识
	return true;
}
int main(){
	int x,y;
	n=read(); m=read();
	for(int i=1;i<=m;i++){
		x=read(); y=read();
		add(x,y);
	}
	for(int i=1;i<=n;i++) if(!dfn[i]) tarjan(i);
	for(int x=1;x<=n;x++){ //枚举每一个点
		for(int i=h[x];i;i=t[i].next){ //枚举每条出边
			int y=t[i].to;
			if(c[x]==c[y] or vst[c[y]]) continue; //如果相连的两个点在同一个强连通分量中，或者x所在的强连通分量与y所在的强连通分量已经建过边，则需要忽略这条边
			vst[c[y]]=1; //将y所在的强连通分量标记为已建过边
			addc(c[x],c[y]); //建边
			rd[c[y]]++; //统计每个强连通分量的入度
		}
		clear(x); //清空vst数组
	}
	int ans=0;
	for(int i=1;i<=cnt;i++) if(!rd[i]) ans++; //统计入度为0的强连通分量的个数
	for(int i=1;i<=cnt;i++){
		if(check(i)){ //符合条件
			ans--;
			break; //这种情况只存在一次，所以如果找到了，就需要break
		}
	}
	printf("%.6lf",double(double(n-ans)/double(n))); //将int转为double，保留小数
	return 0;
}
```
