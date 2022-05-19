---
layout: post
title: "PCA（主成分分析）python实现"
date: 2017-05-07 19:03:30 +0800
comments: true
categories: 机器学习
---
<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. 背景</a></li>
<li><a href="#sec-2">2. PCA算法是如何实现的？</a></li>
<li><a href="#sec-3">3. 基本步骤</a></li>
<li><a href="#sec-4">4. 什么是协方差矩阵？</a></li>
<li><a href="#sec-5">5. 算法实现</a></li>
<li><a href="#sec-6">6. 测试数据</a></li>
<li><a href="#sec-7">7. Demo</a></li>
</ul>
</div>
</div>

# 机器学习算法 PCA（主成分分析）python实现

# 背景<a id="sec-1" name="sec-1"></a>

PCA（Principal Component Analysis）,PCA的主要作用是降低数据集的维度，然后挑选出主要的特征。
PCA的主要思想是移动坐标轴，找到方差最大的方向上的特征值。

# PCA算法是如何实现的？<a id="sec-2" name="sec-2"></a>

简单来说，就是将数据从原始的空间中转换到新的特征空间中，
例如原始的空间是三维的(x,y,z)，x、y、z分别是原始空间的三个基，
我们可以通过某种方法，用新的坐标系(a,b,c)来表示原始的数据，
那么a、b、c就是新的基，它们组成新的特征空间。在新的特征空间中，可能所有的数据在c上的投影都接近于0，
即可以忽略，那么我们就可以直接用(a,b)来表示数据，这样数据就从三维的(x,y,z)降到了二维的(a,b)。

# 基本步骤<a id="sec-3" name="sec-3"></a>

-   计算数据集的协方差矩阵
-   计算协方差矩阵的特征值和特征向量
-   保留最重要的n个特征

#### 参考链接： <http://deeplearning.stanford.edu/wiki/index.php/%E4%B8%BB%E6%88%90%E5%88%86%E5%88%86%E6%9E%90>

# 什么是协方差矩阵？<a id="sec-4" name="sec-4"></a>

其定义是：变量向量减去均值向量，然后乘以变量向量减去均值向量的转置再求均值。
例如x是变量，μ是均值，协方差矩阵等于E[(x-μ)(x-μ)<sup>t</sup>]，
物理意义是这样的，例如x=（x<sub>1</sub>,x<sub>2</sub>,&#x2026;,x<sub>i）</sub>
那么协方差矩阵的第m行n列的数为x<sub>m</sub>与x<sub>n</sub>的协方差，若m=n，则是x<sub>n</sub>的方差。
如果x的元素之间是独立的，那么协方差矩阵只有对角线是有值，
因为x独立的话对于m≠n的情况x<sub>m</sub>与x<sub>n</sub>的协方差为0。另外协方差矩阵是对称的。

# 算法实现<a id="sec-5" name="sec-5"></a>
```python
    def pca(dataMat, topNfeat=9999999):
        meanVals = np.mean(dataMat, axis=0)
        #print(meanVals)
        meanRemoved = dataMat - meanVals #remove mean
        #print(meanRemoved)
        covMat = np.cov(meanRemoved, rowvar=0)
        eigvals,eigvects = np.linalg.eig(np.mat(covMat)) #计算协方差矩阵的特征值和特征向量
        eig_valind = np.argsort(eigvals)                    #sort, sort goes smallest to largest
        eig_valind = eig_valind[:-(topNfeat+1):-1]  #cut off unwanted dimensions
        red_eigvects = eigvects[:,eig_valind]          #reorganize eig vects largest to smallest
        low_datamat = meanRemoved * red_eigvects#transform data into new dimensions
        reconmat = (low_datamat * red_eigvects.T) + meanVals
        return low_datamat, reconmat
```
# 测试数据<a id="sec-6" name="sec-6"></a>
```
学习时间  分数
9         39
15        56
25        93
14        61
10        50
18        75
0         32
16        85
5         42
19        70
16        66
20        80
```
# Demo<a id="sec-7" name="sec-7"></a>
```python
    import numpy as np
    import matplotlib.pyplot as plt
    def loadDataSet(fileName, delim='\t'):
        fr = open(fileName)
        stringArr = [line.strip().split(delim) for line in fr.readlines()]
        datArr = [list(map(float,line)) for line in stringArr]
        #print(mat(datArr))
        fr.close()
        return np.mat(datArr)
    
    def pca(dataMat, topNfeat=9999999):
        meanVals = np.mean(dataMat, axis=0)
        #print(meanVals)
        meanRemoved = dataMat - meanVals #remove mean
        #print(meanRemoved)
        covMat = np.cov(meanRemoved, rowvar=0)
        eigvals,eigvects = np.linalg.eig(np.mat(covMat)) #计算协方差矩阵的特征值和特征向量
        eig_valind = np.argsort(eigvals)                    #sort, sort goes smallest to largest
        eig_valind = eig_valind[:-(topNfeat+1):-1]  #cut off unwanted dimensions
        red_eigvects = eigvects[:,eig_valind]          #reorganize eig vects largest to smallest
        low_datamat = meanRemoved * red_eigvects#transform data into new dimensions
        reconmat = (low_datamat * red_eigvects.T) + meanVals
        return low_datamat, reconmat
    
    def plotBestFit(dataSet1,dataSet2):
        dataArr1 = np.array(dataSet1)
        dataArr2 = np.array(dataSet2)
        n = np.shape(dataArr1)[0]
        n1=shape(dataArr2)[0]
        xcord1 = []; ycord1 = []
        xcord2 = []; ycord2 = []
        xcord3=[];ycord3=[]
        j=0
        for i in range(n):
            xcord1.append(dataArr1[i,0]); ycord1.append(dataArr1[i,1])
            xcord2.append(dataArr2[i,0]); ycord2.append(dataArr2[i,1])
        fig = plt.figure()
        ax = fig.add_subplot(111)
        ax.scatter(xcord1, ycord1, s=30, c='red', marker='s')
        ax.scatter(xcord2, ycord2, s=30, c='green')
    
        plt.xlabel('X1'); plt.ylabel('X2');
        plt.show()
    
    if __name__=='__main__':
        mata=loadDataSet('score')
        a,b= pca(mata, 4)
        plotBestFit(a,b)
```
{% img /images/pca.png %}
