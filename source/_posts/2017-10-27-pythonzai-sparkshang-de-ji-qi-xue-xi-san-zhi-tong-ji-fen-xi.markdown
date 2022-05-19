---
layout: post
title: "Python在Spark上的机器学习(三)之统计分析"
date: 2017-10-27 18:39:42 +0800
comments: true
categories: pyspark&spark
---
﻿<h1 id="python在spark上的机器学习三之统计分析">Python在Spark上的机器学习(三)之统计分析</h1>



<h2 id="背景">背景</h2>

<p>通常来说，一个完整使用机器学习建模解决问题的过程包含一下步骤：</p>

<ul>
<li>数据获取</li>
<li>数据预处理</li>
<li>数据统计分析</li>
<li>算法建模</li>
<li>训练</li>
<li>预测/分类</li>
</ul>

<p>这就意味着，在我们进行一般的数学建模或者挑选机器学习训练算法之前，应该先对数据进行清洗以及简单的统计分析，以便了解数据中显著的特征或者规律（虽然现在的机器学习方法，很多情况下根本不需要了解数据的意义，仅仅是通过堆叠特征就能获得一个可行的结果，但这显然离一个优秀的结果还是有一段距离的）。为了得到更加鲁棒的模型，以及了解数据背后的含义，我们这篇文章就来讲讲如何在PySpark上进行简单的统计分析。</p>



<h3 id="概念介绍">概念介绍</h3>

<p><strong>描述性统计分析</strong> <br>
这是一个统计学的概念，描述性统计是以揭示数据分布特性的方式汇总并表达定量数据的方法。主要包括数据的频数分析、数据的集中趋势分析、数据离散程度分析、数据的分布、以及一些基本的统计图形。特征括并表示定量数据，揭示数据分布的特征。 <br>
描述性统计是一类统计方法的汇总，作用是提供了一种概括和表征数据的有效且相对简便的方法。通常用图示法来表述，易于看懂，能发现质量特性值（总体）的分布状况、趋势走向的一些规律，便于采取措施。用于汇总和表征数据，通常是对数据进一步定量分析的基础，或是对推断性统计方法的有效补充。</p>



<h3 id="数据读取">数据读取</h3>

<p>本文使用的是一个信用欺诈检测的一个数据集，具体下载地址：<a href="http://tomdrabas.com/data/LearningPySpark/ccFraud.csv.gz">这里</a></p>

```python
import PySpark.sql.types as typ
fraud = sc.textFile('data/ccFraud.csv')
header = fraud.first()
fraud = fraud.filter(lambda row:row!=header)\
.map(lambda row:[int(elem) for elem in row.split(',')])
fields = [
    *[
        typ.StructField(h[1:-1],typ.IntegerType(),True)
        for h in header.split(',')
    ]
]
schema = typ.StructType(fields)
fraud_df = spark.createDataFrame(fraud,schema=schema)
fraud_df.printSchema()
fraud_df.head()
```

<blockquote>
  <p>输出结果 <br>
  root <br>
   |– custID: integer (nullable = true) <br>
   |– gender: integer (nullable = true) <br>
   |– state: integer (nullable = true) <br>
   |– cardholder: integer (nullable = true) <br>
   |– balance: integer (nullable = true) <br>
   |– numTrans: integer (nullable = true) <br>
   |– numIntlTrans: integer (nullable = true) <br>
   |– creditLine: integer (nullable = true) <br>
   |– fraudRisk: integer (nullable = true) <br>
  Out: <br>
  Row(custID=1, gender=1, state=35, cardholder=1, balance=3000, numTrans=4, numIntlTrans=14, creditLine=2, fraudRisk=0)</p>
</blockquote>

<p>在上面的代码中，我们读入了我们的数据集，以及创建了一个DataFrame，下面我们再进行一些简单的分析操作。</p>



<h3 id="简单统计分析">简单统计分析</h3>

```python
fraud_df.groupby('gender').count().show() #按照性别分类汇总


numerical = ['balance', 'numTrans', 'numIntlTrans']
desc = fraud_df.describe(numerical) 
##对balance，numTrans，numIntTrans进行描述性分析
desc.show()


# 计算balance值分布的偏度
fraud_df.agg({'balance': 'skewness'}).show()
```

<blockquote>
  <p>输出结果 <br>
  +——+——-+ <br>
  |gender|  count| <br>
  +——+——-+ <br>
  |     1|6178231| <br>
  |     2|3821769| <br>
  +——+——-+ <br>
  +——-+—————–+——————+—————–+ <br>
  |summary|          balance|          numTrans|     numIntlTrans| <br>
  +——-+—————–+——————+—————–+ <br>
  |  count|         10000000|          10000000|         10000000| <br>
  |   mean|     4109.9199193|        28.9351871|        4.0471899| <br>
  | stddev|3996.847309737258|26.553781024523122|8.602970115863904| <br>
  |    min|                0|                 0|                0| <br>
  |    max|            41485|               100|               60| <br>
  +——-+—————–+——————+—————–+ <br>
  +——————+ <br>
  | skewness(balance)| <br>
  +——————+ <br>
  |1.1818315552993839| <br>
  +——————+</p>
</blockquote>

<p>上面我们使用了一些常用的统计分析函数，以及简单地了解了一下PySpark 聚合函数agg()的使用。通常地，常用的聚合函数还有avg() , count(), countDistinct() , max() 等。</p>



<h3 id="相关分析">相关分析</h3>

<p><strong>相关分析</strong>（correlation analysis），相关分析是研究现象之间是否存在某种依存关系，并对具体有依存关系的现象探讨其相关方向以及相关程度，是研究随机变量之间的相关关系的一种统计方法。通常两个变量之间存在的相关关系有：正相关、完全正相关、负相关、完全负相关、无相关。</p>

<p><strong>相关系数</strong>是最早由统计学家卡尔·皮尔逊设计的统计指标，是研究变量之间线性相关程度的量，一般用字母 r 表示。由于研究对象的不同，相关系数有多种定义方式，较为常用的是皮尔逊相关系数。简单来说，相关系数是衡量两个变量间相关关系的指标。</p>

<p>在PySpark中计算两个变量之间的相关系数是非常简单的，往往只需要一条代码：</p>

<blockquote>
  <p>fraud_df.corr(‘balance’, ‘numTrans’)#计算balance和numTrans的相关系数</p>
</blockquote>

<p>输出</p>

<blockquote>
  <p>Out：0.0004452314017265385</p>
</blockquote>

<p>我们还可以通过下面的方法来创建一个相关矩阵。</p>


```python
n_numerical = len(numerical)
corr = []
for i in range(0, n_numerical):
    temp = [None] * i
    for j in range(i, n_numerical):
        temp.append(fraud_df.corr(numerical[i], numerical[j]))
    corr.append(temp)
```

<blockquote>
  <p>输出结果; <br>
  [[1.0, 0.0004452314017265387, 0.0002713991339817875], [None, 1.0, -0.00028057128198165555], [None, None, 1.0]]</p>
</blockquote>

<p>正如输出结果所示，这个信用欺诈检测的数据集中的特征之间不存在过大的相关关系，基本全部都为相互独立关系。因此，在选择特征代入机器学习算法训练时，可以采用全部的变量。正也是统计分析的用处，可以帮助我们了解变量之间的线性相关关系，有利于帮住我们选取训练的特征变量。</p>
