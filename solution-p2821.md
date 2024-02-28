# P2821 变幻数 题解

## Part 1 ：题目分析
这道题非常像一道数学的材料阅读题，实际上也可以按照材料阅读的方式来思考。

首先，根据变幻数的定义，如果给出的子变幻数存在父变幻数，那么它肯定能分解为几个 $1\sim9$ 的数相乘。所以，只要给出的子变幻数有大于 $9$ 的质因数，就一定没有父变幻数。

由于题目要求的是最小的父变幻数，所以生成的父变幻数一定要是位数最小的，然后再把每一位的数字从小到大组合，形成最小的父变幻数。

根据以上推论，很容易就可以想到这道题的解法。
## Part 2 ：运算
首先要把给出的子变幻数分解为几个 $1\sim9$ 的数相乘。

由于要保证位数最少，所以每一轮分解就要尽可能分解出大的数，所以枚举分解的数时要从大到小。
```cpp
void work(int n){
	for(int i=9;i>=2;i--){ //从大到小枚举因数
		if(n%i==0){ //能整除 
			t[++cnt]=i; //用一个t数组记录因数 
			work(n/i); //递归 
			return; //这里不同于dfs，只要找到了可以整除的数，就不用继续枚举了，所以要return 
		}
	}
}
```
接下来就要判断这个数是否有父变幻数，同 Part 1 部分的论述，如果剩下了一个不能被 $2\sim9$ 整除，且大于 $9$ 的数，那么它就没有父变幻数。
```cpp
if(n>9) flag=1; //用flag标记 
```
将前面这些全部做完后，剩下的就很简单了。

由于现在已经确保了位数最少，所以只需要将每一位上的数从小到大排序就可以了。
```cpp
sort(t+1,t+cnt+1); //cnt就是刚才分解时用来当作下标累加器的变量，在这里就相当于位数
```
最后再按顺序输出就可以了。
```cpp
for(int i=1;i<=cnt;i++) printf("%d",t[i]); //不能输出空格
```
## Part 3 ：高精度
由于题目给出的子变幻数**位数**在 $1000$ 以内，所以很明显需要高精度。

下面是重载运算符形式的高精度代码，在这里不做多的解释。
```cpp
string operator * (string a1,string b1){ //高精度乘高精度
    int a[20000]={},b[20000]={},c[50000]={},lena=a1.length(),lenb=b1.length(),lenc,i,j,x;
    string result;
    for(int i=0;i<=lena-1;i++) a[lena-i]=a1[i]-'0';
    for(int i=0;i<=lenb-1;i++) b[lenb-i]=b1[i]-'0';
    for(int i=1;i<=lena;i++){
        x=0;
        for(j=1;j<=lenb;j++){
            c[i+j-1]=a[i]*b[j]+x+c[i+j-1];
            x=c[i+j-1]/10;
            c[i+j-1]%=10;
        }
        c[i+lenb]=x;
    }
	lenc=lena+lenb;
	while(c[lenc]==0&&lenc>1) lenc--;
    for(int i=lenc;i>=1;i--) result+=c[i]+'0';
    return result;
}
string operator / (string a1,int b){ //高精度除单精度
    int a[20000],c[20000],lena=a1.length(),i,x=0,lenc;
    string result;
  	for(int i=0;i<=lena-1;i++) a[i+1]=a1[i]-'0';
    for(int i=1;i<=lena;i++){
        c[i]=(x*10+a[i])/b;
        x=(x*10+a[i])%b;
    }
    lenc=1;
    while(c[lenc]==0&&lenc<lena) lenc++;
    for(int i=lenc;i<=lena;i++) result+=c[i]+'0';
    return result;
}
```
有了这个好东西，我们就只用把 ```int``` 改成 ```string``` 就行了。

但在我调试时，高精度比较大小部分一直出问题，所以在判断有无父变幻数时我换了一种判断位数的方法。
```cpp
//这里的n是string类型
if(n.size()>1) flag=1; //和"if(n>9)"的作用是一样的
```
还有一点，由于本人太弱了，高精度取余不会写，所以只好想了另一种方法来代替取余。
```cpp
if((n/i)*i==n) //注意"n/i"必须打括号，否则编译器会直接将"/i*i"忽略
```

## Part 4 ：代码
```cpp
#include<bits/stdc++.h>
#define maxn 10000005
using namespace std;
string operator * (string a1,string b1){
    int a[20000]={},b[20000]={},c[50000]={},lena=a1.length(),lenb=b1.length(),lenc,i,j,x;
    string result;
    for(int i=0;i<=lena-1;i++) a[lena-i]=a1[i]-'0';
    for(int i=0;i<=lenb-1;i++) b[lenb-i]=b1[i]-'0';
    for(int i=1;i<=lena;i++){
        x=0;
        for(j=1;j<=lenb;j++){
            c[i+j-1]=a[i]*b[j]+x+c[i+j-1];
            x=c[i+j-1]/10;
            c[i+j-1]%=10;
        }
        c[i+lenb]=x;
    }
	lenc=lena+lenb;
	while(c[lenc]==0&&lenc>1) lenc--;
    for(int i=lenc;i>=1;i--) result+=c[i]+'0';
    return result;
}
string operator / (string a1,int b){
    int a[20000],c[20000],lena=a1.length(),i,x=0,lenc;
    string result;
  	for(int i=0;i<=lena-1;i++) a[i+1]=a1[i]-'0';
    for(int i=1;i<=lena;i++){
        c[i]=(x*10+a[i])/b;
        x=(x*10+a[i])%b;
    }
    lenc=1;
    while(c[lenc]==0&&lenc<lena) lenc++;
    for(int i=lenc;i<=lena;i++) result+=c[i]+'0';
    return result;
}
string n;
int flag,cnt;
int t[maxn];
string num[10]={"0","1","2","3","4","5","6","7","8","9"};
void work(string n){
	for(int i=9;i>=2;i--){ //从大到小枚举因数
		if((n/i)*num[i]==n){ //能整除 
			t[++cnt]=i; //用一个t数组记录因数 
			work(n/i); //递归 
			return; //这里不同于dfs，只要找到了可以整除的数，就不用继续枚举了，所以要return 
		}
	}
	if(n.size()>1) flag=1; //用flag标记 
}
signed main(){
	cin>>n;
	work(n);
	if(flag==1){
		printf("There is no such number!");
		return 0; //直接结束程序 
	}
	sort(t+1,t+cnt+1); //从小到大排序 
	for(int i=1;i<=cnt;i++) printf("%d",t[i]); //不能输出空格 
	return 0;
}
```
