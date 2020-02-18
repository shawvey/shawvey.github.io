---
img: 10.jpg
layout: post
title:  "将excel表中数据导入到word对应位置中"
date:   2017-12-29 9:42:47 +0700
categories: posts
tags: [python]
comments: True
author: shawvey
---
### 一、实验数据与目的   

excel数据如下图（共829行）：      
![](https://i.loli.net/2017/12/29/5a459e3a0bf5c.png)      
word数据如下图（共1000段类似数据）：      
![](https://i.loli.net/2017/12/29/5a459e39f17a2.png)     
实验目的：将excel表中对应的内容填入到word相对应的位置中去。      

### 二、实验思路      

刚拿到这个数据与题目也是非常萌比的，大致的理解就是把两个文档数据都导入存成list或者dataframe等来进行读取数据，再拼接起来。遂了解了一下python的docx库以及xlrd库。     

### 三、实验过程  

#### 1、导入数据

读取excel并存成list：        
``` python
def open_excel(file= r'C:\Users\烫烫\Desktop\tecent\消防安全技术实务.xlsx'):
    data = xlrd.open_workbook(file)
    return data

#根据名称获取Excel表格中的数据   参数:file：Excel文件路径     colnameindex：表头列名所在行的索引  ，by_name：Sheet1名称
def excel_table_byname(file=r'C:\Users\烫烫\Desktop\tecent\消防安全技术实务.xlsx', colnameindex=0, by_name=r'消防安全技术实务'):
    data = open_excel(file) #打开excel文件
    table = data.sheet_by_name(by_name) #根据sheet名字来获取excel中的sheet
    nrows = table.nrows #行数 
    colnames = table.row_values(colnameindex) #某一行数据 
    list =[] #装读取结果的序列
    for rownum in range(0, nrows): #遍历每一行的内容
         row = table.row_values(rownum) #根据行号获取行
         if row: #如果行存在
             app = [] #一行的内容
             for i in range(len(colnames)): #一列列地读取行的内容
                app.append(row[i])
             list.append(app) #装载数据
    return list
tables = excel_table_byname()
```

读取word文档：     
``` python
doc = docx.Document(r"C:\Users\烫烫\Desktop\tecent\实务单选题.docx")
``` 

#### 2、将excel数据一个一个导入word中去   

在实验过程中，发现word是一段一段存储的，便遍历一段一段，在这里的一段相当于一行。段落的文本获取为paragraph.text，然后再段落里寻找关键字即为paragraph.text.find(keywords)。在word的段落后追加内容的函数为add_run()。      
``` python
if para.text.find('题干')!=-1 and parag1<len(tables):
   para.add_run(tables[parag-1][0])
```
本来应该excel一行数据对于word中的一堆数据，但是excel导入后数据有偏差。题干、选项、正确答案、解析在上一行，知识点ID、知识点名称、题目标签在下。无论无何，excel行数对于他们而言都是一样的，便设置不同的参数来进行累加。      
``` python
parag1=1
parag=1
for para in doc.paragraphs :
    if para.text.find('题干')!=-1 and parag1<len(tables):
        para.add_run(tables[parag-1][0])
    if para.text.find('选项')!=-1 and parag1<len(tables):
        para.add_run(tables[parag-1][1])
    if para.text.find('正确答案')!=-1 and parag1<len(tables):
        para.add_run(tables[parag-1][2])
    if para.text.find('解析')!=-1 and parag1<len(tables):
        para.add_run(tables[parag-1][3])
        parag1=parag1+1
    if para.text.find('知识点ID')!=-1 and parag<len(tables):
        para.add_run(tables[parag][4])
    if para.text.find('知识点名称')!=-1 and parag<len(tables):
        para.add_run(tables[parag][5])
    if para.text.find('题目标签')!=-1 and parag<len(tables):
		para.add_run(tables[parag][6])
		parag=parag+1
``` 
到这里还并没有结束，因为word中题目标签本身就有内容，我们需要将其替换。python的docx没有能替换一部分的解决办法，我采取将这行先置空，然后自己添加相同格式的“题目标签：”,再后面追加excel对应的内容。其中有一个小坑，题目标签加粗了，而冒号没有加粗，所以分开追加。            
``` python
para.text=''
r = para.add_run('题目标签')
r.font.bold = True
r.font.size = Pt(10.5)
r.font.name=u'等线 (中文正文)'
q=para.add_run('：')
q.font.size = Pt(10.5)
q.font.name=u'等线 (中文正文)'
para.add_run(tables[parag][6])
parag=parag+1
``` 
最后，存储文档即可。     
``` python
doc.save(u'D://测试.docx')
```
导出结果如下：        
![](https://i.loli.net/2017/12/29/5a45b547c0ad8.png)

### 四、全部代码
``` python
# -*- coding: utf-8 -*-
"""
Created on Mon Dec 25 17:41:09 2017

@author: shawvey
"""

import docx
import xlrd
from docx.shared import Pt
#打开excel文件
def open_excel(file= r'C:\Users\烫烫\Desktop\tecent\消防安全技术实务.xlsx'):
    data = xlrd.open_workbook(file)
    return data

#根据名称获取Excel表格中的数据   参数:file：Excel文件路径     colnameindex：表头列名所在行的索引  ，by_name：Sheet1名称
def excel_table_byname(file=r'C:\Users\烫烫\Desktop\tecent\消防安全技术实务.xlsx', colnameindex=0, by_name=r'消防安全技术实务'):
    data = open_excel(file) #打开excel文件
    table = data.sheet_by_name(by_name) #根据sheet名字来获取excel中的sheet
    nrows = table.nrows #行数 
    colnames = table.row_values(colnameindex) #某一行数据 
    list =[] #装读取结果的序列
    for rownum in range(0, nrows): #遍历每一行的内容
         row = table.row_values(rownum) #根据行号获取行
         if row: #如果行存在
             app = [] #一行的内容
             for i in range(len(colnames)): #一列列地读取行的内容
                app.append(row[i])
             list.append(app) #装载数据
    return list
tables = excel_table_byname()
doc = docx.Document(r"C:\Users\烫烫\Desktop\tecent\实务单选题.docx")
parag1=1
parag=1
for para in doc.paragraphs :
    if para.text.find('题干')!=-1 and parag1<len(tables):
        para.add_run(tables[parag-1][0])
    if para.text.find('选项')!=-1 and parag1<len(tables):
        para.add_run(tables[parag-1][1])
    if para.text.find('正确答案')!=-1 and parag1<len(tables):
        para.add_run(tables[parag-1][2])
    if para.text.find('解析')!=-1 and parag1<len(tables):
        para.add_run(tables[parag-1][3])
        parag1=parag1+1
    if para.text.find('知识点ID')!=-1 and parag<len(tables):
        para.add_run(tables[parag][4])
    if para.text.find('知识点名称')!=-1 and parag<len(tables):
        para.add_run(tables[parag][5])
    if para.text.find('题目标签')!=-1 and parag<len(tables):
        para.text=''
        r = para.add_run('题目标签')
        r.font.bold = True
        r.font.size = Pt(10.5)
        r.font.name=u'等线 (中文正文)'
        q=para.add_run('：')
        q.font.size = Pt(10.5)
        q.font.name=u'等线 (中文正文)'
        para.add_run(tables[parag][6])
        parag=parag+1
#
doc.save(u'D://测试.docx')

```