---
layout: post
title:  "CODE[VS]-bronze"
date:   2018-03-02
excerpt: "CODE[VS]-bronze练习题"
tags: [C/C++]
comments: true
---

> 四月初就是蓝桥杯了，开始全力准备蓝桥杯。目标不大，拿到省三就好啦，拍拍肩！

### 1201 最小数和最大数
输入n个数，n<=100，找到其中最小的数和最大的数。        
``` c
#include<stdio.h>
int main()
{
int n,i;
int max=-2147483648,min=2147483647,x;
scanf("%d",&n);
for (i=1;i<=n;++i)
{
	scanf("%d",&x);
	if (x>max)
    	max=x;
	if (x<min)
		min=x;
}
    printf("%d %d",min,max) ;
    return 0;
}
```

### 1202 求和
求n个数的和。       
``` c
#include<stdio.h>
int main(){
	int n,i,sum=0,value;
	scanf("%d",&n);
	for(i=0;i<n;i++){
		scanf("%d",&value);
		sum+=value;
	}
	printf("%d",sum);
	return 0;
} 
```

### 1203 判断浮点数是否相等
给出两个浮点数，请你判断这两个浮点数是否相等。      
``` c
#include<stdio.h>
int main(){
	float a,b;
	scanf("%f %f",&a,&b);
	if(a==b){
		printf("yes");
	}
	else{
		printf("no");
	}
	return 0;
} 
```

### 1206 保留两位小数
保留两位小数输出一个浮点数。       
``` c
#include<stdio.h>
int main(){
	double value;
	scanf("%lf",&value);
	printf("%.2lf",value);
	return 0;
}
```

### 2235 机票打折
输入机票原价（3到4位的正整数，单位：元），再输入机票打折率（小数点后最多一位数字）。编程计算打折后机票的实际价格（单位：元。计算结果要将个位数四舍五入到十位数“元”）。输入只有一行两个数（两数间用一个空格分隔），第一个为整数，表示机票原价，第二个整数或实数（如是实数，小数点后最多1位数字）表示打折率。          
``` c
#include<stdio.h>
int main(){
	int price,afterprice;
	float count;
	scanf("%d %f",&price,&count);
	afterprice=price*(count/10);
	if(afterprice%10>=5){
		afterprice=(afterprice/10+1)*10;
	}
	else{
		afterprice=(afterprice/10)*10;
	}
	printf("%d",afterprice);
	return 0;
}
```

### 1204 寻找子串位置
给出字符串a和字符串b，保证b是a的一个子串，请你输出b在a中第一次出现的位置。          
``` c
#include<stdio.h>
int main(){
	char a[100],b[100];
	int i,pos=0;
	scanf("%s %s",a,b);
	for(i=0;a[i]!='\0';i++)
		if(a[i]==b[0])
			pos=i+1;
	printf("%d",pos); 
	return 0;
}
```

### 1205 单词翻转
给出一个英语句子，希望你把句子里的单词顺序都翻转过来。       
``` c
#include<stdio.h>
#include<string.h>
int main(){
	char a[100];
	int i,j,r,q;
	gets(a);
	j=strlen(a)-1;
	r=0;
	for(i=strlen(a)-1;i>=0;i--){
		if(a[i]==' '){
			r=i;
			for (q=r+1;q<=j;q++)
			printf("%c",a[q]);
			printf(" ");
			j=r-1;
		}
	}
	for (q=0;q<r;q++)
	printf("%c",a[q]);
	return 0;
}
```