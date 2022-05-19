---
layout: post
title: "基本抽样和蓄水池抽样"
date: 2017-05-12 19:33:10 +0800
comments: true
categories: 机器学习
---

<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. 前言</a></li>
<li><a href="#sec-2">2. 基本采样方法的简介</a>
<ul>
<li><a href="#sec-2-1">2.1. 单纯随机抽样（simple random sampling）</a></li>
<li><a href="#sec-2-2">2.2. 系统抽样（systematic sampling）</a></li>
</ul>
</li>
<li><a href="#sec-3">3. 扩展</a>
<ul>
<li><a href="#sec-3-1">3.1. 蓄水池抽样算法</a></li>
</ul>
</li>
</ul>
</div>
</div>


# 前言<a id="sec-1" name="sec-1"></a>

在阅读机器学习以及神经网络的相关资料中，我们总会时不时看见统计采样的身影。
看似简单的统计采样，在各种学习算法中发挥的强大的作用，一份好的样本，不但可以大幅度降低算法的计算量，
还能较为准确的代表整个样本空间，对算法的效率优化和整体的模型设计都有着非常大的意义。

# 基本采样方法的简介<a id="sec-2" name="sec-2"></a>

## 单纯随机抽样（simple random sampling）<a id="sec-2-1" name="sec-2-1"></a>

**主要思想** ：首先将整体编号，然后采用随机数的方法进行不放回性地抽取，并将所取得的数据组成新的样本。

-   *优点* :操作简单
-   *缺点* :数据量大时难以编号

**代码实现** ：
```python
    import random
    
    def loaddata(filename):
        dataMat = []
        with open(filename) as fp:
            for line in fp.readlines():
                dataMat.append(line.strip().split('\t'))
        return dataMat
    
    def simple_sampling(dataMat, num):
        try:
            samples = random.sample(dataMat, num)
            return samples
        except:
            print('sample larger than population')
```
## 系统抽样（systematic sampling）<a id="sec-2-2" name="sec-2-2"></a>

**主要思想** ：先将总体分成n个部分，然后依次用相等间隔，从每一个部分中抽取出一个数据项组成观察样本。

-   *优点* :易于理解，样本涵盖范围广，有利于避免边缘化。
-   *缺点* :容易受总体的增减趋势影响。

**代码实现** ：
```python
    def loaddata(filename):
        dataMat = []
        with open(filename) as fp:
            for line in fp.readlines():
                dataMat.append(line.strip().split('\t'))
        return dataMat
    
    def systematic_sampling(dataMat, num):
        k = int(len(dataMat)/num)
        samples = [random.sample(dataMat[i*k:(i+1)*k], 1) for i in range(num)]
        return samples
```
# 扩展<a id="sec-3" name="sec-3"></a>

## 蓄水池抽样算法<a id="sec-3-1" name="sec-3-1"></a>

蓄水池抽样是个很有趣的问题，这个问题的来源是关于等概率抽样的一种思考，
问题是，如何能在不知道总体对象数量（或者数量巨大）的情况下抽取k个对象，
使得每个对象被抽取到的概率相同。

**原理** ：考虑最终一定要抽取到k个对象，所以先任意抽出k个，
然后对剩下的对象分别以某种概率概率，使得最终每个对象被抽到的概率相同。

**算法步骤** ：

-   输入：长度为N的数组L（N未知或者很大）；输出：被等可能抽出的长度为k的数组l
-   对输入L取前k个数组成的数组作为蓄水池
-   对于L的第i(i=k+1,k+2,…,N)个数，任取r为0~i-1之间的整数，若r>k-1,则不进行替换，若r<=k-1,则用第i个数去替换蓄水池中第r个数
-   遍历一遍L，取到的l中的每个元素都是以概率k/n等可能取到的

**代码实现** ：
```python
    import numpy as np
    import matplotlib.pyplot as plt
    def pool(L,k):
        arr = L[:k]
        for i,e in enumerate(L[k:]):
            r = np.random.randint(0,k+i+1)
            if r<=k-1:
                arr[r] = e
        return arr
    def count(q,n):
        L = array("d")
        l1 = array("d")
        l2 = array("d")
        for e in q:
            L.append(e)
        for e in range(n):
            l1.append(L.count(e))
        for e in l1:
            l2.append(e/sum(l1))
        return l1,l2
    if __name__ == '__main__':
        a = np.array([[i]*10000000 for i in range(3)])#生成等量的0，1，2
        a.shape = 1,-1
        L = a[0]
        k = 300000#设置要抽取的样本的数量，一般远小于总体数量
        p = pool(L, k)
        l1 = ['value=%d'% x for x in range(3)]
        plt.pie(count(p,3)[0],labels=l1,labeldistance=0.1,autopct='%1.2f%%')
        plt.title("Reservoir sampling")
        plt.show()
```
