# CSP-S  2023 游寄

去年初赛的时候忘记缴费了，整个赛季全部报销，所以说有快两年没打过正式比赛了，慌得皮包。

### Day -1

仔细研读了 1kri 大佬关于 OI 与玄学的研究 $\to$ [lk1](https://www.luogu.com.cn/blog/imustakioi/oi-yu-xuan-xue-bai-xuan-jiao-di-dan-sheng) [lk2](https://www.luogu.com.cn/blog/imustakioi/xuan-xue-wu-pin-yi-lan) 感觉说的非常有道理啊！！于是整了一些德芙白巧克力，顺带买了瓶装星巴克美式进行测试，为 NOIP 做准备。

下午去 bz 试机，感觉环境是真的好用啊，键盘打起来非常爽，不像某中学的机房的键盘鼠标，难打还断触。

### Day 1

不考 J，所以上午起的比较晚，整个上午感觉比较压抑，想整点什么来复习又不知道复习啥，还好有 B 站和 Elden Ring :)

一直摆到大概 1 点的时候突然想起来一些数学知识好像在考纲里面，然而我差不多都忘完了，于是开始疯狂翻阅+硬记。但是没考

1 点 50 到 bz 集合，jjh 和 bz 的同学进行了亲切且深入的交流。

谨记某人的教诲，开题先看完所有题目再做，于是 2:30 开题，T1 水题？ T2 字符串？？ T3 大模拟？？？ T4 神秘题。

然后大概几分钟写了 T1，2:50 的时候差不多检查完开 T2，结果发现和前几天打的模拟赛有点像，但那场好像是 T4 啊？？于是很快的就写完了 $n^2$ 暴力跑路,这种有点神秘的题目我一般都不会直接开冲的，还是大模拟比较好做。

开 T3，从大概 3:20 写到了 4:50 才过大样例，并且由于样例 3 调了很久才过就觉得应该有一定强度，检查完就扔了。

5:10 开 T4 觉得还有 1h 多这不随便 300+ 吗，又根据某著名言论 `300分以上就是超高分啦` ，后面基本就摆着打的，也没仔细冲正解，T4 暴力好像还挂了一点。

6:30 出考场时听说全都 AK 了！！！非常难受，但是开摆！！

然后晚上大概 9 点正在颓的时候突然想起 T4 的特殊性质好像判错位了，爆挂 BCD 性质 40pts，爽！！

### Day 2

下午回机房，然后交一发洛谷 T3，只有 40 分？？一顿检查发现当时调大样例时写的特判忘记删了！！

```cpp
inline void get_u(){
	int pos; cin>>pos;
	int id=upper_bound(p+1,p+ntot+1,(SUB){pos,0})-p-1;
	if(id<1||id>ntot) return GG();
	int tt=p[id].tt;
	if(tt<=4){ //here，忘记判是否合法了
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
如果 4 操作问的地址之前最近的一个起始位置是一个基本变量，并且这个地址不在这个变量范围内，我也会输出这个变量名（（（

实测 洛谷 40，云斗 55，小图灵 45，信友队45，所以说基本就是 50%？

按照小图灵和云斗的榜来说，如果 T3 挂 0 就 1= 不了了（（（

没有说法，只能祈祷 ccf 数据水吧。

%%%% @DeepSkyCore @zwh2008 AK 爷

### 总结

- 调试代码做好标记

- 不要对题目有心理预设

- 要相信 ccf 数据强度

- 星巴克瓶装美式有 debuff，且绝对值大于德芙白巧