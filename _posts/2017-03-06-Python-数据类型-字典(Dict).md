---
layout: post
title: Python-数据类型-字典(Dict)
categories: Python语法
description: Python-数据类型-字典(Dict)
keywords: Python语法
---
### Python数据类型-字典(详解)
字典是一种可变容器模型，可以存储任意类型的对象。
字典的每个元素是``键值对``形式的，即：``key和value``
键和值中间使用``：``连接，每个键值对之间使用逗号``,``隔开，整个字典包括在或括号``{}``中，格式如下：
```
d = {key1:value1, key2:value2}
```
### 注意
- 键必须是唯一的，值则不必唯一。
- 值可以是任何数据类型，但键必须是``不可变``的，如字符串、数字、元组。

### 字典和列的区别
- 列表是有序的对象的集合
- 字典是无序的对象的结合

### 字典的操作

#### 访问字典里的值

- dict[key] : 从字典中取值，key不存在会报错``keyError``

```python
In [1]: dict = {"name":"zhangsan", "age":18}

In [2]: dict["name"]
Out[2]: 'zhangsan'

In [3]: dict["age"]
Out[3]: 18

In [4]: dict["gender"]
---------------------------------------------------------------------------
KeyError                                  Traceback (most recent call last)
<ipython-input-4-ea9de2a2f2d9> in <module>()
----> 1 dict["gender"]

KeyError: 'gender'
```
- dict.keys(): 取出字典中所有的key  在Python2中返回的是包含所有key的列表，返回的是可迭代对象，通过for 遍历可以取出所有的key

```python
In [18]: dict.keys()
Out[18]: dict_keys(['name', 'age', 'gennder'])
```

- dict.values(): 返回所有value的列表。

```python
In [19]: dict.values()
Out[19]: dict_values(['zhangsan', 18, '男'])
```

- dict.items(): 返回所有key， value元组的列表

```python
In [20]: dict.items()
Out[20]: dict_items([('name', 'zhangsan'), ('age', 18), ('gennder', '男')])
```
#### 删除操作

- del dict[key] : 删除指定的键值对，key不存在会报错

```python
In [5]: del dict["age"]

In [6]: dict
Out[6]: {'name': 'zhangsan'}
```
- dict.pop(key)： 删除指定的键值对，key 不存在会报错，返回值是删除的键对应的值

```python
Out[9]: {'age': 18, 'gender': '男', 'name': 'zhangsan'}

In [11]: dict.pop("gender")
Out[11]: '男'
```
- dict.popitem(): 随机删除一个键值对，返回删除的键值对

```python
In [13]: dict = {"name": "zhangsan", "age": 18, "gennder": "男"}

In [14]: dict.popitem()
Out[14]: ('gennder', '男')
```
- dict.clear(): 清空字典

```python
In [15]: dict.clear()

In [16]: dict
Out[16]: {}
```
#### 赋值操作
- dict[key] = value : 如果key存在，修改数据  如果key不存在，新建键值对

```python
In [26]: dict = {"name": "zhangsan", "age": 18, "gender": "男"}

In [27]: dict["gender"] = "中性"

In [28]: dict
Out[28]: {'age': 18, 'gender': '中性', 'name': 'zhangsan'}

In [29]: dict["bir"] = "1993-06-25"

In [31]: dict
Out[31]: {'age': 18, 'bir': '1993-06-25', 'gender': '中性', 'name': 'zhangsan'}
```

- dict.setdefault(key,value) : 如果key存在，不会修改数据，如果key不存在，新建键值对

```python
In [33]: dict = {"name": "zhangsan", "age": 18, "gender": "男"}

In [34]: dict.setdefault("name", "Lisi")
Out[34]: 'zhangsan'

In [35]: dict
Out[35]: {'age': 18, 'gender': '男', 'name': 'zhangsan'}

In [36]: dict.setdefault("bir", "1993-06-25")
Out[36]: '1993-06-25'

In [37]: dict
Out[37]: {'age': 18, 'bir': '1993-06-25', 'gender': '男', 'name': 'zhangsan'}
```

- dict1.update(dict2) : 将字典2的数据合并到字典1

```python
In [39]: dict1 = {"name": "zhangsan", "age": 18, "gender": "男"}

In [40]: dict2 = {"classes": 8, "cls_num": 305}

In [41]: dict1.update(dict2)

In [42]: dict1
Out[42]: {'age': 18, 'classes': 8, 'cls_num': 305, 'gender': '男', 'name': 'zhangsan'}
```
### 字典内置的函数
- cmp(dict1, dict2): 比较两个字典元素。
- len(dict): 计算字典元素个数，即键的总数。
- str(dict): 输出字典可打印的字符串表示。
- type(dict): 返回输入的变量类型，如果变量是字典就返回字典类型。

### 字典内置方法
- dict.clear(): 删除字典内所有的元素。
- dict.copy(): 返回一个字典的浅拷贝。
- dict.fromkeys(seq[,val]): 创建一个新字典，以序列 seq 中元素做字典的键，val 为字典所有键对应的初始值.
- dict.get(key,default=None): 返回指定键的值，如果值不在字典中则返回default的值。
- dict.has_key(key): 如果键在字典dict里返回true，否则返回false。
- dict.items(): 以列表返回可遍历的(key,value)的元组数组。
- dict.keys(): 以列表返回一个字典所有的键。
- dict.values(): 以列表返回一个字典所有的值。
- dict.setdefault(key, default=None): 跟get()类似，但如果键不存在字典中，将会添加键并将值设置为default的值。
- dict.update(dict2): 把字典dict2的键值更新到dict里。
- dict.pop(key[,default]): 删除字典给定键key所对应的值，返回值为被删除的值，key值必须给出，否则返回default的值。
- dict.popitem(): 随机删除并删除字典中的一对键和值
