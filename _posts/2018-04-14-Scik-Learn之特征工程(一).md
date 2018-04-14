---
layout: post
title: Scik-Learn之特征工程(一)
categories: [Scik-Learn,机器学习,特征工程]
description: Scik-Learn之特征工程(一)
keywords: Linux,Scik-Learn,机器学习
comments: true
---


# Scik-Learn之特征工程(一)

>系统环境Ubuntu、Deepin

## 安装
创建一个基于Python3的虚拟环境(可以在你自己已有的虚拟环境中)
```
mkvitualenv -p /usr/bin/python3.5 虎势环境名称
```
在ubuntu的虚拟环境当中运行以下命令
```
pip3 install Scikit-learn
```
然后通过导入命令查看是否可以使用(需要Numpy,pandas乖库)
```
import sklearn
```
>如果已经安装过anaconda，可以直接使用内置的scik-learn库

## 数据的特征抽取
>sklearn特征抽取API 
>sklearn.feature_extraction

#### 字典特征抽取
    作用:对字典数据进行特征值化
    类：sklearn.feature_extraction.DictVectorizer
###### DictVectorizer语法
* DictVectorizer(sparse=True,..)
    * DictVectorizer.fit_transform(X)
        * X:字典或者包含字典的迭代器
        * 返回值：返回sparse矩阵
    * DictVectorizer.inverse_transform(X)
        * X:array数组或者sparse矩阵
        * 返回值：转换之前数据格式
    * DictVectorizer.get_feature_names(X)
        * 返回类别名称
    * DictVectorizer.transform(X)
        * 按照原先的标准转换
        
###### 案例测试
```
from sklearn.feature_extraction import DictVectorizer

        
data = [{'city': '北京', 'temperature':100},
        {'city': '上海', 'temperature': 60},
        {'city': '深圳', 'temperature': 30},]

# 创建DictVectorizer实例对象
dicts = DictVectorizer(sparse=False)
mydatas = dicts.fit_transform(data)
print('Sparse矩阵：'.format(mydatas))

# 获取特征类别多名称
print('类别名称'.format(dicts.get_feature_names()))

# 数据还原
trans = dicts.inverse_transform(mydatas)
print('数据还原：'.format(trans))    
```
运行结果
```
[[  0.   1.   0. 100.]
 [  1.   0.   0.  60.]
 [  0.   0.   1.  30.]]
['city=上海', 'city=北京', 'city=深圳', 'temperature']
[{'city=北京': 1.0, 'temperature': 100.0}, {'city=上海': 1.0, 'temperature': 60.0}, {'city=深圳': 1.0, 'temperature': 30.0}]
```
    
#### 文本的特征抽取
> 作用：对文本数据进行特征值化
> 类：sklearn.feature_extraction.text.CountVectorizer

###### CountVectorizer语法
* CountVectorizer()
    * 返回词频矩阵
    * CountVectorizer.fit_transform(X)
        * X:文本或者包含文本字符串的可迭代对象
        * 返回值：返回sparse矩阵
    * CountVectorizer.inverse_transform(X)
        * X:array数组或者sparse矩阵
        返回值：转换之前数据格式
    * CountVectorizer.get_feature_names()
        * 返回值：单词列表
        
###### 案例测试
```
from sklearn.feature_extraction.text import CountVectorizer

mytext = ['to be or not to be, that is a question',
              'where there is a will, there is a way']

# 创建文本特征抽取对象
dataCount = CountVectorizer()

# 获取特征数据
mydata = dataCount.fit_transform(mytext)

# 特征类别名称
print('特征类别名称：\n', dataCount.get_feature_names())

print('特征数据矩阵:\n',mydata.toarray())

# 数据还原
print('数据还原:',dataCount.inverse_transform(mydata))
```
    > 对中文进行特征提取时需要先使用jieba进行分词
    > 下载pip3 install jieba
    > 使用
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
