# 玄学，赐予我力量！

upd 2023/10/29 我爱 CCF ！！！！！

CSP-S 2023 T3 如果 4 操作询问的是单走一个基本类型，并且恰好没在范围内导致 ERR，那我就萎了。

```cpp
inline void get_u(){
	int pos; cin>>pos;
	int id=upper_bound(p+1,p+ntot+1,(SUB){pos,0})-p-1;
	if(id<1||id>ntot) return GG();
	int tt=p[id].tt;
	if(tt<=4){
		//调大样例的时候写的特判忘记删了，刚好没判是否是 ERR
		cout<<NID[id]<<endl;
		return;
	}
	string res=NID[id]; pos-=p[id].pos; 
	while(1){
		if(tt<=4){
			if(pos<len[tt]) cout<<res<<endl;
			else GG();  return;
		}
		int nxtid=upper_bound(h[tt].sub.begin(),h[tt].sub.end(),(SUB){pos,0})-h[tt].sub.begin()-1;
		if(nxtid<0||nxtid>=h[tt].sub.size()) return GG();
		res=res+"."+h[tt].NID[nxtid];
		pos-=h[tt].sub[nxtid].pos;
		tt=h[tt].sub[nxtid].tt;
	}
}
```

关键是这样写错了没有性质分，能拿多少纯看脸。

目前 洛谷 40，云斗 55，小图灵 45，本校 oj 5？？？

所以说应该是 rand(0,100)，CCF 赐予我力量！！！

CCF 太爽了⑧！！！直接给了 60 分！！