---
img: 3.jpg
layout: post
title:  "C tutorial"
date:   2017-11-26 23:09:47 +0700
categories: posts
tags: [C]
author: shawvey
---
### 英语词汇
 
unary 一元  
syntax 句法  
colon 冒号  
bitwise 按位  
preprocessor 预处理器  
escape sequence 转义字符  
universal character 通用字符  
Assignment Operators 赋值运算符  
Misc Operators 杂项运算符  
the closing curly brace 大括号

### Bitwise Operators

Bitwise operator works on bits and performs bit-by-bit operation. The truth tables for &, |,and ^ are as follows:
![](https://i.imgur.com/nr2rzEm.png)
**&按位与，|按位或，^按位异或，~按位反。**  
Assume if A = 60; and B = 13; now in binary format they will be as follows:  
A = 0011 1100  
B = 0000 1101  
A&B = 0000 1100  
A|B = 0011 1101  
A^B = 0011 0001  
~A = 1100 0011   
来看看具体的含义：
![](https://i.imgur.com/BY1zEmm.png)  

``` bash
#include <stdio.h>
main()
{
unsigned int a = 60; /* 60 = 0011 1100 */
unsigned int b = 13; /* 13 = 0000 1101 */
int c = 0;
c = a & b; /* 12 = 0000 1100 */
printf("Line 1 - Value of c is %d\n", c );
c = a | b; /* 61 = 0011 1101 */
printf("Line 2 - Value of c is %d\n", c );
c = a ^ b; /* 49 = 0011 0001 */
printf("Line 3 - Value of c is %d\n", c );
c = ~a; /*-61 = 1100 0011 */
printf("Line 4 - Value of c is %d\n", c );
c = a << 2; /* 240 = 1111 0000 */
printf("Line 5 - Value of c is %d\n", c );
c = a >> 2; /* 15 = 0000 1111 */TUTORIALS POINT
Simply Easy Learning Page 31
printf("Line 6 - Value of c is %d\n", c );
}
```

### escape sequence  
C中的转义字符：  
![](https://i.imgur.com/8NwHZYn.png)
![](https://i.imgur.com/FZUey2M.png)

### Operators Precedence in C
Operator precedence determines the grouping of terms in an expression. This affects how an expression is evaluated. Certain operators have higher precedence than others; for example, the multiplication operator has higher precedence than the addition operator.
![](https://i.imgur.com/lnesrPE.png)  
![](https://i.imgur.com/Hstlhoe.png)  



