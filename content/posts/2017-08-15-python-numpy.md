---
title: 关于python科学计算库numpy学习总结
date: 2017-08-15 14:20:49
lastmod: 2023-12-03 15:33:44
tags: [python]
---

关于 python 科学计算库 numpy 学习总结

<!-- more -->

> 本文中部分 print 输出可能会报错，原因为 python3 的 print 通过函数方式使用，与 python2 中的 print 通过解释执行不同，需要使用 print()进行控制台打印

## 安装 numpy

python3 -m pip install -U pip 更新 pip
pip install numpy

### 安装方式 II

pip install ipython
ipython --pylab

pylab 模式下会自动导入 SciPy,NumPy,Matplotlib 模块

## 引入 numpy

import numpy as py

## 使用 numpy

arange()函数用于创建同类型多维数组（homogeneous multidimensional array）

用 arange 创建的数组使用 type()查看类型为 ndarray

reshape()函数用于重新构造数组成为其他维度数组

例如：np.arange(20).reshape(4,5)

[[0  1  2  3  4]
 [ 5  6  7  8  9]
 [10 11 12 13 14]
 [15 16 17 18 19]]

### arrry 数组相关属性：

ndim：维度
shape：各维度大小
size：元素个数
dtype：元素类型
dsize：元素占位大小

### 生成特殊矩阵

全零矩阵：np.zeros()

注意：ones()和 zeros()函数的第一个参数是一个指向数列的指针，不能直接是一个数列，例如上图报错情况

全一矩阵：np.ones(d,dtype=int)
默认生成浮点型，可通过第二个参数指定元素数据类型

随机数数组

np.random.rand(5)生成包含 5 个[0,1)区间的数的数组

### 数组计算

a = np.array([1.0, 2],[2, 4])
a
[[1.  2.]
 [ 2.  4.]]
由于数组是【同质】的，python 会自动将整型转换为浮点型

- np.exp(a)：自然常数 e（约等于 2.7）的 a 次方
- np.sqrt(a)：a 的开方
- np.square(a)：a 的平方
- np.power(a,3)：a 的 3 次方

- a.sum()：所有元素之和
- a.max()：最大元素
- a.min()：最小元素
- a.max(axis=1)：每行最大
- a.min(axis=0)：每列最小

### 数组与矩阵（matrix）

#### 注意：

- 矩阵是二维数组，矩阵乘法相求左侧矩阵列数等于右侧矩阵行数
- 数组可以是任意正整数维数，乘法要求两侧数组行列数均相同

#### 相互转换

##### 数组转矩阵

np.asmatrix(a)
np.mat(a)

##### 直接生成

np.matrix('1.0 2.0;3.0 4.0')

### 生成指定长度的一维数组

np.linspace(0,2,9)：生成从 0 开始，到 2 结束，包含 9 个元素的等差数列

### 数组元素访问

```py
a = np.array([3.2, 1.5],[2.5, 4])
print a[0][1]
1.5
print a[0,1]
1.5
```

注意：
若 b=a 是将 b 和 a 同时指向同一个 array，若修改 a 或者 b 的某个元素，a 和 b 都会改变
若想 a 和 b 不会关联修改，则需要 b = a.copy()为 b 单独生成一份拷贝

```py
a:
[[0  1  2  3  4]
 [ 5  6  7  8  9]
 [10 11 12 13 14]
 [15 16 17 18 19]]
```

a[: , [1,3]]：访问 a 的所有行的 2、4 列

#### 访问符合条件的元素

a[: , 2]a[: , 0] > 5]

解释：
a [x] [y]表示访问符合 x、y 条件的 a 的元素，[: , 2]表示取所有行的第 3 列，[a[: , 0] > 5]表示取第一列大于 5 的行（即第 3、4 行），最终即表示取第 3、4 行的第 3 列，即得结果 array([12, 17])这个“子”数组

numpy.where()查找符合条件的位置
例如：loc = np.where(a == 11)
print loc
(array([2]), array([1]))

结果是一个表示坐标的元组，元组第一个数组表示查询结果的行坐标，第二个数组表示结果的列坐标
print a[loc[0][0], loc[1][0]]
11

上式为通过位置反求元素 11
注意：where 求出的结果为元组，不能通过 loc[x,y]的方式获取元素（该获取方式为数组的方式，因为元组没有索引），只能通过 loc[x][y]的方式获取

## 数组其他操作

### 矩阵转置

```py
a = np.random.rand(2,4)
a = np.transpose(a)将 a 数组转置

b = np.random.rand(2,4)
b = np.mat(b)
print b.T 转置矩阵
```

### 矩阵求逆

```py
import numpy.linalg as nlg
a = np.random.rand(2,2)
a = np.mat(a)
ia = nlg.inv(a) 得逆矩阵
print a \* ia

[[1.  0.]
 [ 0.  1.]]
```

### 特征值和特征向量

a = np.random.rand(3,3)
eig_value, eig_vector = nlg.eig(a)

### 拼接矩阵（使用场景：循环处理某些数据后的操作）

按列拼接两个向量成一个矩阵

vstack
hstack

实例：
```py
a = np.random.rand(2,2)
b = np.random.rand(2,2)
c = np.hstack([a,b]) 水平拼接
d = np.vstack([a,b]) 垂直拼接
```

### 缺失值

nan 作为缺失值的记录
通过 isnan 判定
```py
a = np.random.rand(2,2)
a[0, 1] = np.nan
print (np.isnan(a))
```

nan_to_num 可用来将 nan 替换成 0
pandas 提供能指定 nan 替换值的函数

```py
print(np.nan_to_num(a))
[[0.54266589  0.        ]
 [ 0.92468339  0.70599254]]
```

更多 Numpy 函数见
- [http://wiki.scipy.org/Numpy_Example_List](http://wiki.scipy.org/Numpy_Example_List)
- [http://docs.scipy.org/doc/numpy](http://docs.scipy.org/doc/numpy)

参考文献

- https://uqer.io/community/share/54ca15f9f9f06c276f651a56
- http://wiki.scipy.org/Tentative_NumPy_Tutorial

Sheppard K. Introduction to Python for econometrics, statistics and data analysis. Self-published, University of Oxford, version, 2012, 2.

本文最初发布于个人 [CSDN](https://blog.csdn.net/sinat_16791487/article/details/77188078) 博客
