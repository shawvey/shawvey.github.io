---
img: 6.jpg
layout: post
title:  "scikit learn"
crawlertitle: "scikit learn"
summary: "了解scikit以及实现"
excerpt: "了解scikit以及实现"
date:   2017-11-27 23:09:47 +0700
categories: posts
tags: [机器学习]
author: shawvey
---
### Scikit-learn**是什么**
[Scikit-learn](http://scikit-learn.org/stable/ "sklearn")（以下简称sklearn）是开源的Python机器学习库，它基于Numpy和Scipy，提供了大量用于数据挖掘和分析的工具，包括数据预处理、交叉验证、算法与可视化算法等一系列接口。


### **K近邻算法-电影分类问题**

代码：
``` python
import numpy as np
from sklearn.neighbors import KNeighborsClassifier  
X=np.array([[3,104],[2,100],[1,81],[101,10],[99,5],[98,2]])
y=['爱情片','爱情片','爱情片','动作片','动作片','动作片']
model=KNeighborsClassifier(n_neighbors=3)
model.fit(X,y)
print(model)
Test=np.array([18,90])
predicted=model.predict(Test)
print(predicted)
```
结果：
``` bash
KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
           metric_params=None, n_jobs=1, n_neighbors=3, p=2,
           weights='uniform')
['爱情片']
```
<br>
### **决策树算法-隐形眼镜分类问题**

代码：
``` python
from sklearn import tree
from sklearn.cross_validation import train_test_split
from sklearn.metrics import accuracy_score
ft=open(r'C:\Users\烫烫\Desktop\lenses.txt')
y=[inst.strip().split('\t')[-1] for inst in ft.readlines()]
x=[[0,0,0,0],[0,0,0,1],[0,0,1,0],[0,0,1,1],[0,1,0,0],[0,1,0,1],[0,1,1,0],
   [0,1,1,1],[1,0,0,0],[1,0,0,1],[1,0,1,0],[1,0,1,1],[1,1,0,0],[1,1,0,1],
   [1,1,1,0],[1,1,1,1],[2,0,0,0],[2,0,0,1],[2,0,1,0],[2,0,1,1],[2,1,0,0],
   [2,1,0,1],[2,1,1,0],[2,1,1,1]]
xtrain,xtest,ytrain,ytest = train_test_split(x,y,test_size=0.3)
clf = tree.DecisionTreeClassifier(criterion='entropy')
print(clf)
clf.fit(xtrain,ytrain)
predicted = clf.predict(xtest)
print(predicted)
print('正确率为：%f'%accuracy_score(ytest,predicted))
``` 
结果：
``` bash
DecisionTreeClassifier(class_weight=None, criterion='entropy', max_depth=None,
            max_features=None, max_leaf_nodes=None,
            min_impurity_split=1e-07, min_samples_leaf=1,
            min_samples_split=2, min_weight_fraction_leaf=0.0,
            presort=False, random_state=None, splitter='best')
['no lenses' 'hard' 'soft' 'no lenses' 'soft' 'hard' 'no lenses'
 'no lenses']
正确率为：0.750000
``` 
<br>
### **朴素贝叶斯-当气候情况如下时，判断是否出去打网球**

代码：
``` python
from sklearn.naive_bayes import GaussianNB
model = GaussianNB()
#获取数据，并序列化
fr=open(r"C:\Users\烫烫\Desktop\data_train.txt")
values=[inst.strip().split('\t') for inst in fr.readlines()][1:-1]
dicts={'sunny':0,'overcast':1,'rainy':2,'hot':0,'mild':1,'cool':2,
       'high':0,'normal':1,'WEAK':0,'STRONG':1,'no':0,'yes':1}
X=[]
for line in values:
    flag=[]
    for i in range(len(line)-1):
        flag.append(dicts[line[i]])
    X.append(flag)
y=[dicts[line[-1]] for line in values]
print (X)
#构建模型进行预测
model.fit(X, y)
print(model)
predicted = model.predict([0,2,0,1])
print(predicted)
```
结果:
``` bash
[[0, 0, 0, 0], [0, 0, 0, 1], [1, 0, 0, 0], [2, 1, 0, 0], [2, 2, 1, 0], 
[2, 2, 1, 1], [1, 2, 1, 1], [0, 1, 0, 0], [0, 2, 1, 0], [2, 1, 1, 0],
[0, 1, 1, 1], [1, 1, 0, 1]]
GaussianNB(priors=None)
[0]
```
<br>
### **逻辑斯蒂回归-疝气病症预测病马死亡率**

代码：
``` python
# 1.加载数据
dataTrain=open(r'D:\四年记\工作室\ML\machinelearninginaction\Ch05\horseColicTraining.txt')
dataTest=open(r'D:\四年记\工作室\ML\machinelearninginaction\Ch05\horseColicTest.txt')
TrainMat = []; TrainLabel = []
TestMat = []; TestLabel = []
for line in dataTrain.readlines():
    strArr = line.strip().split('\t')
    lineArr=[]
    for i in range(21):
        lineArr.append(float(strArr[i]))
    TrainMat.append(lineArr)
    TrainLabel.append(float(strArr[21]))
for line in dataTest.readlines():
    strArr = line.strip().split('\t')
    lineArr=[]
    for i in range(21):
        lineArr.append(float(strArr[i]))
    TestMat.append(lineArr)
    TestLabel.append(float(strArr[21]))
#
# 3.标准化特征值
from sklearn.preprocessing import StandardScaler
lg = StandardScaler()
lg.fit(TrainMat,TrainLabel)
X_train_std = lg.transform(TrainMat)
X_test_std = lg.transform(TestMat)

# 4. 训练逻辑回归模型
logreg = linear_model.LogisticRegression(C=1e5)
logreg.fit(TrainMat,TrainLabel)

# 5. 预测
prepro = logreg.predict_proba(X_test_std)
print(logreg)
print(prepro)
acc = logreg.score(X_test_std,TestLabel)
print(acc)
``` 
结果：
``` bash
LogisticRegression(C=100000.0, class_weight=None, dual=False,
          fit_intercept=True, intercept_scaling=1, max_iter=100,
          multi_class='ovr', n_jobs=1, penalty='l2', random_state=None,
          solver='liblinear', tol=0.0001, verbose=0, warm_start=False)
[[ 0.14968906  0.85031094]
 [ 0.14555252  0.85444748]
 [ 0.68479831  0.31520169]
 ..., 
 [ 0.65474317  0.34525683]
 [ 0.36321864  0.63678136]
 [ 0.26711916  0.73288084]]
0.686567164179
```
<br>