---
img: 17.jpg
layout: post
title:  "CODE[VS]-silver"
date:   2018-03-12 14:37:47 +0700
categories: posts
tags: [C/C++]
comments: True
author: shawvey
---

> 终于做完silver了，准备做Gold了。这次不仅要打蓝桥杯还有天梯赛，啊~感觉对二叉树很不熟，以后晚上回去要看之前买的数据结构书才行了，不然难题肯定做不出来。

### 1075 明明的随机数
明明想在学校中请一些同学一起做一项问卷调查，为了实验的客观性，他先用计算机生成了N个1到1000之间的随机整数（N≤100），对于其中重复的数字，只保留一个，把其余相同的数去掉，不同的数对应着不同的学生的学号。然后再把这些数从小到大排序，按照排好的顺序去找同学做调查。请你协助明明完成“去重”与“排序”的工作。             
``` c
#include <stdio.h>  
int main( )  
{  
   int i,a[100],b[100],n,temp,j,r,pos;    
   scanf("%d",&n);
   for( i = 0; i < n;i++ ){
	scanf("%d",&a[i]);
   }
   r=0;
   for(i=0;i<n;i++){
	if(i==0){
		b[r]=a[i];
		r++;
	}
	else{
		pos=0;
		for(j=0;j<r;j++){
			if(a[i]==b[j]){
				pos++;
			}
		}
		if(pos==0){
			b[r]=a[i];
			r++;
		}
	}
	
   }
   for(j=r-1;j>=0;j--){
	for(i=0;i<j;i++){
		if(b[i]>b[i+1]){
			temp=b[i];
			b[i]=b[i+1];
			b[i+1]=temp;
		}
	}
}
    printf("%d\n",r);
	for( i = 0; i < r;i++ ){
	   printf("%d ",b[i]); 
   }
   return 0;
}  
```

### 1076 排序
给出n和n个整数，希望你从小到大给他们排序。      
这里使用C++里的函数会起到事半功倍的作用！                              
``` c
#include<iostream>
#include<algorithm>
using namespace std;
int main()
{
 int i,a[100000],n;    
 cin>>n;
 for( i = 0; i < n;i++ )
	cin>>a[i];
 sort(a,a+n);
 for(i=0;i<n;i++)
 cout<<a[i]<<' ';
 return 0;
}
```

### 1083 Cantor表
现代数学的著名证明之一是Georg Cantor证明了有理数是可枚举的。他是用下面这一张表来证明这一命题的： 1/1 1/2 1/3 1/4 1/5 … 2/1 2/2 2/3 2/4 … 3/1 3/2 3/3 … 4/1 4/2 … 5/1 … … 我们以Z字形给上表的每一项编号。第一项是1/1，然后是1/2，2/1，3/1，2/2，…               
![](https://i.imgur.com/rnjHQxi.png)       
``` c
#include<stdio.h>
#include"math.h"
int main(){
	double n;
	int value,front,deal;
	scanf("%lf",&n);
	value=sqrt(2*(n+1));
	front=n-((1+value-1)*(value-1))/2;
	if(value%2==0){
		printf("%d/%d",front,value+1-front);		
	}
	else{
		printf("%d/%d",value+1-front,front);
	}
	return 0;
}   
```

### 1160 蛇形矩阵
小明玩一个数字游戏，取个n行n列数字矩阵（其中n为不超过100的奇数），数字的填补方法为：在矩阵中心从1开始以逆时针方向绕行，逐圈扩大，直到n行n列填满数字，请输出该n行n列正方形矩阵以及其的对角线数字之和.             
``` c
#include<stdio.h>
int main(){
	int n,a[100][100],square,i,j,djx=0;
	scanf("%d",&n);
	square=n*n;
	for(i=0;i<n/2;i++){
		for(j=n-1-i;j>=1+i;j--){
			a[n-1-i][j]=square;
			square--;
		}
		for(j=n-1-i;j>=1+i;j--){
			a[j][i]=square;
			square--;
		}
		for(j=i;j<=n-2-i;j++){
			a[i][j]=square;
			square--;
		}
		for(j=i;j<=n-2-i;j++){
			a[j][n-1-i]=square;
			square--;
		}
	}
	a[n/2][n/2]=square;
	for(i=0;i<n;i++){
		for(j=0;j<n;j++){
			printf("%d ",a[i][j]);
			if(i==j){
				djx+=a[i][j];
			}
			if(i+j==n-1){
				djx+=a[i][j];
			}
		}
		printf("\n");
	}
	printf("%d",djx-1);
	return 0;
} 
```

### 1012 最大公约数和最小公倍数问题
输入二个正整数x0,y0(2<=x0<100000,2<=y0<=1000000),求出满足下列条件的P,Q的个数,条件:       1.P,Q是正整数       
2.要求P,Q以x0为最大公约数,以y0为最小公倍数.        
试求:满足条件的所有可能的两个正整数的个数.              
``` c
#include<stdio.h>
#include <stdbool.h>
bool isok(int a,int b){
	int min,i,q=0;
	min=a<b?a:b;
	for(i=2;i<=min;i++){
		if(a%i==0&&b%i==0){
			q=1;
		}
	}
	if(q==0){
		return true;
	} 
	else{
		return false;
	}
}
int main(){
	int x0,y0,value,value2,i,j,temp,count;
	scanf("%d %d",&x0,&y0);
	if(y0%x0==0){
		value=y0/x0;
		count=2;
		for(i=2;i<=value/2;i++){
		if(value%i==0){
			value2=value/i;
			if(isok(i,value2)){
				count+=1; 
			}
		}
	}
	printf("%d",count);
	}
	else{
		printf("%d",0);
	}

	return 0;
}
```

### 1212 最大公约数
求两个数A和B的最大公约数。 1<=A,B<=2^31-1                
``` c
#include<stdio.h>
long long gcd(long long a,long long b){
	long long i,min,gys;
	min=a<b?a:b;
	for(i=1;i<=a;i++){
		if(a%i==0&&b%i==0){
			gys=i;
		}
	}
	return gys;
}
int main(){
	long long a,b;
	scanf("%lld %lld",&a,&b);
	printf("%lld",gcd(a,b));
	return 0;
} 
```

### 1430 素数判定
质数又称素数。指在一个大于1的自然数中，除了1和此整数自身外，不能被其他自然数整除的数。      
素数在数论中有着很重要的地位。比1大但不是素数的数称为合数。1和0既非素数也非合数。质数是与合数相对立的两个概念，二者构成了数论当中最基础的定义之一。基于质数定义的基础之上而建立的问题有很多世界级的难题，如哥德巴赫猜想等。算术基本定理证明每个大于1的正整数都可以写成素数的乘积，并且这种乘积的形式是唯一的。这个定理的重要一点是，将1排斥在素数集合以外。如果1被认为是素数，那么这些严格的阐述就不得不加上一些限制条件。        
概念:         
只有1和它本身两个约数的自然数，叫质数(Prime Number)。（如：由2÷1=2，2÷2=1，可知2的约数只有1和它本身2这两个约数，所以2就是质数。与之相对立的是合数：“除了1和它本身两个约数外，还有其它约数的数，叫合数。”如：4÷1=4，4÷2=2，4÷4=1，很显然，4的约数除了1和它本身4这两个约数以外，还有约数2，所以4是合数。）         
100以内的质数有2，3，5，7，11，13，17，19，23，29，31，37，41，43，47，53，59，61，67，71，73，79，83，89，97，在100内共有25个质数。        
注：（1）1既不是质数也不是合数。因为它的约数有且只有1这一个约数。（2）2和3是所有素数中唯一两个连着的数。             
``` c
#include<stdio.h>
#include <stdbool.h>
bool isprime(int n){
	int flag=0,i;
	if(n==1&&n==2){
		return true;
	}
	else{
	for(i=2;i<n/2;i++){
		if(n%i==0){
			flag=1;
		}
	}
	if(flag==0){
		return true;
	}
	else{
		return false;
	}
	}
}
int main(){
	int n;
	scanf("%d",&n);
	if(isprime(n)){
		printf("\\t");
	}
	else{
		printf("\\n");
	}
	return 0;
}
```

### 1474 十进制转m进制
将十进制数n转换成m进制数 m<=16 n<=100          
``` c
#include<stdio.h>
int main(){
	int n,m,a[100],i,num=0;
	scanf("%d %d",&n,&m);
	do{
		a[num]=n%m;
		n=n/m;
		num++;
	}while(n!=0);
	for(i=num-1;i>=0;i--){
		if(a[i]<=9){
		printf("%d",a[i]);
		}
		else{
			printf("%c",65+a[i]-10);
		}
	
	}
	return 0;
}
```
### 1475 m进制转十进制
将m进制数n转化成一个十进制数 m<=16        
题目保证转换后的十进制数<=100            
``` c
#include<stdio.h>
#include<math.h>
#include <string.h>
int main(){
	int m,count=0,length,value;
	long num=0;
	char a[10000];
	scanf("%s %d",a,&m);
	length=strlen(a)-1;
	while(length>=0){
		if(a[length]=='A'){
			num+=10*pow(m,count);
		}
		else if(a[length]=='B'){
			num+=11*pow(m,count);
		}
		else if(a[length]=='C'){
			num+=12*pow(m,count);
		}
		else if(a[length]=='D'){
			num+=13*pow(m,count);
		}
		else if(a[length]=='E'){
			num+=14*pow(m,count);
		}
		else if(a[length]=='F'){
			num+=15*pow(m,count);
		}
		else{
			num+=(a[length]-48)*pow(m,count);
		}
		count++;
		length--;
	};

	printf("%d",num);
	return 0;
}
```
### 1011 数的计算
我们要求找出具有下列性质数的个数(包含输入的自然数n):        
先输入一个自然数n(n<=1000),然后对此自然数按照如下方法进行处理:       
1.不作任何处理;       
2.在它的左边加上一个自然数,但该自然数不能超过原数的一半;      
3.加上数后,继续按此规则进行处理,直到不能再加自然数为止.        
``` c
#include<stdio.h>

int f(int n){                    
  int s=1,i;                         
  for(i=1; i<=n/2; i++)       
    s+=f(i);
  return s;
}

int main(){
  int n;
  scanf("%d",&n);
  printf("%d",f(n));
  return 0;
}
```

### 1978 Fibonacci数列 3
斐波纳契数列是这样的数列：
f1 = 1      
f2 = 1      
f3 = 2      
f4 = 3       
....         
fn = fn-1 + fn-2       
输入一个整数n         
求fn         
``` c
#include<stdio.h>
int fibonacci(int n){
	if(n==1||n==2){
		return 1; 
	}
	else{
		return fibonacci(n-1)+fibonacci(n-2);
	}
}
int main(){
	int n;
	scanf("%d",&n);
	printf("%d",fibonacci(n)); 
	return 0;
}
```

### 1501 二叉树最大宽度和高度
给出一个二叉树，输出它的最大宽度和高度。       
``` c
#include <stdio.h>  
#include <string.h>  
  
int a[1000][2],s[1000];  
int i,n,x,y;   
  
void dfs(int i,int k)  
{  
    s[k]=s[k]+1;  
    if(k>x)  x=k;  
    if(a[i][1]!=0){
    	dfs(a[i][1],k+1);
    }
    if(a[i][2]!=0)  dfs(a[i][2],k+1);  
}  
  
int main()  
{  
    scanf("%d",&n);  
    memset(a,0,sizeof(a));  
    memset(s,0,sizeof(s));  
    for(i=1;i<=n;i++)  
        scanf("%d%d",&a[i][1],&a[i][2]);  
    x=0;  
    dfs(1,1);  
    y=0;  
    for(i=1;i<1000;i++)  
        if(s[i]>y)   y=s[i];  
    printf("%d %d",y,x);  
    return 0;  
}  
```

### 1842 递归第一次
同学们在做题时常遇到这种函数        
f(x)=5 (x>=0)        
f(x)=f(x+1)+f(x+2)+1 (x<0)        
下面就以这个函数为题做一个递归程序吧      
``` c
#include<stdio.h>
int f(int n){
	if(n>=0){
		return 5;
	}
	else{
		return f(n+1)+f(n+2)+1;
	}
}
int main(){
	int n;
	scanf("%d",&n);
	printf("%d",f(n));
	return 0;
} 
```

### 3038 3n+1问题
3n+1问题是一个简单有趣而又没有解决的数学问题。这个问题是由L. Collatz在1937年提出的。克拉兹问题（Collatz problem）也被叫做hailstone问题、3n+1问题、Hasse算法问题、Kakutani算法问题、Thwaites猜想或者Ulam问题。         
问题如下：            
（1）输入一个正整数n；               
（2）如果n=1则结束；                
（3）如果n是奇数，则n变为3n+1，否则n变为n/2；                
（4）转入第（2）步。                
克拉兹问题的特殊之处在于：尽管很容易将这个问题讲清楚，但直到今天仍不能保证这个问题的算法对所有可能的输入都有效——即至今没有人证明对所有的正整数该过程都终止。              
``` c
#include<stdio.h>
int main(){
	int a[100],T,i,b[100];
	scanf("%d",&T);
	for(i=0;i<T;i++){
		scanf("%d",&a[i]);
	}
	for(i=0;i<T;i++){
		b[i]=0;
		while(a[i]!=1){
			if(a[i]%2==0){
				a[i]=a[i]/2;
			}
			else{
				a[i]=3*a[i]+1;
			}
			b[i]++;
		}
	}
	for(i=0;i<T;i++){
		printf("%d\n",b[i]);
	}
	return 0;
}
```

### 3143 二叉树的序遍历
求一棵二叉树的前序遍历，中序遍历和后序遍历。              
``` c
#include<stdio.h>
#include<string.h>
int a[20][2];
void center(int k){
	printf("%d ",k);
	if(a[k][0]){
		center(a[k][0]);
	}
	if(a[k][1]){
		center(a[k][1]);
	}
}
void left(int k){
	if(a[k][0]){
		left(a[k][0]);
	}
	printf("%d ",k);
	if(a[k][1]){
		left(a[k][1]);
	}
}
void right(int k){
	if(a[k][0]){
		right(a[k][0]);
	}
	if(a[k][1]){
		right(a[k][1]);
	}
	printf("%d ",k);
}
int main(){
	int n,i;
	scanf("%d",&n);
	for(i=1;i<=n;i++){
		scanf("%d %d",&a[i][0],&a[i][1]);
	}
	center(1);
	printf("\n");
	left(1);
	printf("\n");
	right(1);
	printf("\n");
	return 0;
} 
```

### 3145 汉诺塔游戏
汉诺塔问题（又称为河内塔问题），是一个大家熟知的问题。在A，B，C三根柱子上，有n个不同大小的圆盘（假设半径分别为1-n吧），一开始他们都叠在我A上（如图所示），你的目标是在最少的合法移动步数内将所有盘子从A塔移动到C塔。                   
游戏中的每一步规则如下：                  
1.每一步只允许移动一个盘子（从一根柱子最上方到另一个柱子的最上方）               
2.移动的过程中，你必须保证大的盘子不能在小的盘子上方（小的可以放在大的上面，最大盘子下面不能有任何其他大小的盘子）                
如对于n=3的情况，一个合法的移动序列式：               
1 from A to C               
2 from A to B             
1 from C to B         
3 from A to C          
1 from B to A           
2 from B to C                        
1 from A to C          
给出一个数n，求出最少步数的移动序列。               
``` c
#include<stdio.h>
#include<math.h>
void hanoi(int n,char a,char b,char c){
	if(n==1){
		printf("%d from %c to %c\n",n,a,c);
        return;
	}
	else{
		hanoi(n-1,a,c,b);
		printf("%d from %c to %c\n",n,a,c);
		hanoi(n-1,b,a,c);
	}
}
int main(){
	int n;
	int times;
	scanf("%d",&n);
	times=pow(2,n)-1;
	printf("%d\n",times);
	hanoi(n,'A','B','C');
	return 0;
} 
```
                 
