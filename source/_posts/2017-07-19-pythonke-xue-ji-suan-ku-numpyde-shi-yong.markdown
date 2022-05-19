---
layout: post
title: "Python科学计算库NumPy的使用"
date: 2017-07-19 19:10:49 +0800
comments: true
categories: Python
---

# Python科学计算库NumPy的使用

## NumPy的介绍
    
NumPy(Numerical Python的缩写)是一个开源的Python科学计算库。使用NumPy,就可以很自然地使用数组和矩阵。NumPy包含很多实用的数学函数,涵盖线性代数运算、傅里叶变换和随机数生成等功能。如果你的系统中已经装有LAPACK,NumPy的线性代数模块会调用它,否则NumPy将使用自己实现的库函数。LAPACK是一个著名的数值计算库,最初是用Fortran写成的,Matlab同样也需要调用它。从某种意义上讲,NumPy可以取代Matlab和Mathematica的部分功能,并且允许用户进行快速的交互式原型设计。

## NumPy的数组对象
ndarray是一个多维数组对象，该对象由实际数据+描述性元数据组成。
使用Numpy需要先安装和导入NumPy库，有关安装教程可以参考[Installing NumPy](https://docs.scipy.org/doc/numpy/user/install.html)
    
> 导入语法：import numpy as np

此处使用np为别名是为了避免命名空间被污染。

**特点**：

1. NumPy数组一般是同质（即数组内的元素为相同类型，特殊类型除外）
2. NumPy数组的下标也是从0开始
3. 可以方便地创建高维数组

*eg：*
```bash
In: a = np.arange(5)
In: a.dtype
Out: dtype('int64')
```
而在32位系统中，得到的结果类型可能是int32。

**多维数组的创建**：
```bash
In: m = np.array([np.arange(2), np.arange(2)])
In: m
Out:
array([[0, 1],
[0, 1]])
In: m.shape #shape可以输出数组行列（维度）信息
Out: (2, 2)
```
**选取数组元素**：
我们继续沿用上面创建的数组m
```bash
In: m[0,0]
Out: 0
In: m[0,1]
Out: 1
```
    是的,从数组中选取元素就是这么简单。对于数组 a ,只需要用 a[m,n] 选取各数组元素,其中 m 和 n 为元素下标。

**NumPy的数据类型**:
    Python支持的数据类型有整型、浮点型以及复数型,但这些类型不足以满足科学计算的需求,因此NumPy添加了很多其他的数据类型。在NumPy中,许多函数的参数中可以指定数据类型,通常这个参数是可选的:
```bash
In: np.arange(7, dtype=uint16)
Out: array([0, 1, 2, 3, 4, 5, 6], dtype=uint16)
```
其中完整的数据类型可以通过np.sctypeDict.keys()查到：
```bash
dict_keys([0, 1, 2, 3, 'D', 5, 6, 'ushort', 8, 'P', 10, 11, 12, 'uintp', 14, 15, 16, 17, 18, 19, 'Float16', 21, 22, 23, 'cfloat', 4, 'Object0', 'int32', 'UInt64', 'Complex128', 'uint0', 'i2', 7, 'Int16', 'int', 'complex', 'ubyte', 'Int32', 'float', 'i', 'short', 'B', 'str0', 9, 'complex_', 'O', 'long', 'bytes', 'float_', 'Int64', 'int0', 'Void0', 'float128', 'Float64', 'Str0', 'int64', 'b', 'longdouble', 'void', 'f', 'longcomplex', 'ulonglong', 'intp', 'UInt32', 'V', 'object_', 'longlong', 'csingle', 'uint', 'c32', 'M', 'I', 'singlecomplex', 'double', 'timedelta64', 'object', 'unicode_', 'Float128', 'uint64', 'h', 'str', 'd', 'UInt8', 20, 'complex128', 'string_', 'clongfloat', 'H', 'm8', 'clongdouble', 'S', 'g', 'bool_', 'unicode', 'f16', 13, 'int8', 'void0', 'L', 'M8', 'uint32', 'p', 'bytes0', 'e', 'datetime64', 'U', 'float16', 'c16', '?', 'Bool', 'byte', 'i4', 'c8', 'int16', 'half', 'uint16', 'str_', 'i8', 'Complex32', 'Int8', 'bool', 'Bytes0', 'G', 'l', 'uint8', 'f2', 'single', 'f8', 'q', 'Q', 'm', 'Complex64', 'f4', 'u2', 'Float32', 'i1', 'u4', 'Datetime64', 'intc', 'float64', 'a', 'complex64', 'u1', 'bytes_', 'cdouble', 'object0', 'UInt16', 'bool8', 'float32', 'uintc', 'Timedelta64', 'F', 'longfloat', 'b1', 'u8', 'int_', 'complex256'])
```
**创建自定义数据类型**：

(1)创建数据类型

```bash
In: t = np.dtype([('name',np.str_,128),('count',np.int32),('price',np.float64)])
In: t
Out: dtype([('name', '<U128'), ('count', '<i4'), ('price', '<f8')])
```
(2)查看数据类型

```bash
In: t['name']
Out: dtype('<U128')
```
(3)使用自定义数据

```bash
In: itemz = np.array([('Meaning of life DVD', 42, 3.14), ('Butter', 13, 2.72)], dtype=t)
In: itemz[1]
Out: ('Butter', 13, 2.72)
```

**一维数组的索引和切片**：
```bash
In: a = np.arange(9)
In: a[3:7]
Out: array([3, 4, 5, 6])
```
**多维数组索引和切片**:
```bash
In： b=np.arange(24).reshape(2,3,4)
In: b
Out: array([[[ 0,  1,  2,  3],
        [ 4,  5,  6,  7],
        [ 8,  9, 10, 11]],

       [[12, 13, 14, 15],
        [16, 17, 18, 19],
        [20, 21, 22, 23]]])
In: b[0,2,1]
Out: 9-
#我们还可以这样写,选取第0组的所有元素:
In: b[0,:,:]
Out: array([[
0, 1, 2, 3],
[4, 5, 6, 7],
[8, 9,10,11]])
```
*同时，b[0,:,:] == b[0,...]。*
更多的多维数组的索引和切片操作可以参考[NumPy使用手册](https://docs.scipy.org/doc/numpy/reference/)

**数组展平**:

(1)ravel

```bash
In: b-
Out:
array([[[ 0, 1, 2, 3],
[ 4, 5, 6, 7],
[ 8, 9,10,11]],
[[12,13,14,15],
[16,17,18,19],
[20,21,22,23]]])
In: b.ravel()
Out:
array([ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16,
17, 18, 19, 20, 21, 22, 23])
```

(2)flatten

```bash
In: b.flatten()
Out:
array([ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16,
17, 18, 19, 20, 21, 22, 23])
```
flatten 和 ravel的区别在于：flatten函数会请求分配内存来保存结果，而reval函数只是返回数组的一个视图（view）

(3)改变数组的shape属性

```bash
In: b.shape = (6,4)
In: b
Out:
array([ 0, 1, 2, 3],
[ 4, 5, 6, 7],-
[ 8, 9,10,11],
[12,13,14,15],
[16,17,18,19],
[20,21,22,23]],
```

(4)transpose,相当与线性代数的转置

```bash
In: b.transpose()
Out:
array([[ 0, 4, 8,12,16,20],
[ 1, 5, 9,13,17,21],
[ 2, 6,10,14,18,22],
[ 3, 7,11,15,19,23]])
```
(5)resize，功能和reshape相同，但是resize会直接影响原操作数组

```bash
In: b.resize((2,12))
In: b
Out:
array([[ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11],
[12,13,14,15,16,17,18,19,20,21, 22, 23]])
```
**数组组合**：

(1)水平组合

```bash
In: a = np.arange(9).reshape(3,3)
In: b=2*a
In: np.hstack((a,b))
Out: array([[ 0,  1,  2,  0,  2,  4],
       [ 3,  4,  5,  6,  8, 10],
       [ 6,  7,  8, 12, 14, 16]])
```
*同样可以使用concatenate函数实现同样效果*
```bash
    In: concatenate((a,b),axis=1)
    Out: array([[ 0, 1, 2, 0, 2, 4],
    [ 3, 4, 5, 6, 8,10],
    [ 6, 7, 8,12,14,16]])
```

(2)垂直组合

```bash
    In: np.vsta-ck((a,b))
    Out: array([[ 0,  1,  2],
       [ 3,  4,  5],
       [ 6,  7,  8],
       [ 0,  2,  4],
       [ 6,  8, 10],
       [12, 14, 16]])
```
*同样可以使用concatenate函数实现同样效果*
```bash
In: concatenate((a,b),axis=0)
Out: In: np.vstack((a,b))
Out: array([[ 0,  1,  2],
[ 3,  4,  5],
[ 6,  7,  8],
[ 0,  2,  4],
[ 6,  8, 10],
[12, 14, 16]])
```
(3)深度组合

深度组合,就是将一系列数组沿着纵轴(深度)方向进行层叠组合。举个例子,有若干张二维平面内的图像点阵数据,我们可以将这些图像数据沿纵轴方向层叠在一起,这就形象地解释了什么是深度组合。
```bash
In: np.dstack((a,b))
Out: array([[[ 0,  0],
        [ 1,  2],
        [ 2,  4]],
       [[ 3,  6],
        [ 4,  8],
        [ 5, 10]],

       [[ 6, 12],
        [ 7, 14],
        [ 8, 16]]])
```
(4)列组合,column_stack 函数对于一维数组将按列方向进行组合,而对于二维数组, column_stack 与 hstack 的效果是相同的

(5)行组合,当然,NumPy中也有按行方向进行组合的函数,它就是 row_stack 。对于两
个一维数组,将直接层叠起来组合成一个二维数组。同样，对于二维数组，row_stack 与 vstack 的效果是相同的。

**数组分割**:

NumPy数组可以进行水平、垂直或深度分割,相关的函数有 hsplit 、 vsplit 、 dsplit 和split 。我们可以将数组分割成相同大小的子数组,也可以指定原数组中需要分割的位置。

(1)水平分割

```bash
In: a
Out: array([[0, 1, 2],
       [3, 4, 5],
       [6, 7, 8]])
In: np.hsplit(a,3) #沿水平方向分割成三个大小相同的子数组
Out: [array([[0],
        [3],
        [6]]), array([[1],
        [4],
        [7]]), array([[2],
        [5],
        [8]])]
```
(2)垂直分割

```bash
In: np.vsplit(a,3) #在垂直方向上分割成三个大小相同的子数组
Out： [array([[0, 1, 2]]), array([[3, 4, 5]]), array([[6, 7, 8]])]
```
(3)深度分割,dsplit函数将按深度方向分割数组。

```bash
In: np.dsplit(np.arange(27).reshape(3,3,3),3)
Out: [array([[[ 0],
         [ 3],
         [ 6]],
 
        [[ 9],
         [12],
         [15]],
 
        [[18],
         [21],
         [24]]]), array([[[ 1],
         [ 4],
         [ 7]],
 
        [[10],
         [13],
         [16]],
 
        [[19],
         [22],
         [25]]]), array([[[ 2],
         [ 5],
         [ 8]],
 
        [[11],
         [14],
         [17]],
 
        [[20],
         [23],
         [26]]])]
```
*同时，hsplit,vsplit同样也可以用函数split来实现，其使用就像上面的数组组合函数concatenate类似*


**到此，NumPy的常规的数组操作基本就结束了！**
