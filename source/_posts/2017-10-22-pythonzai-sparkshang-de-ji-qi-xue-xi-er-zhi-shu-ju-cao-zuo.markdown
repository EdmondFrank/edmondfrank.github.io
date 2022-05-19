---
layout: post
title: " Python在Spark上的机器学习(二)之数据操作"
date: 2017-10-22 16:18:47 +0800
comments: true
categories: pyspark&spark
---
﻿<h1 id="python在spark上的机器学习二之数据操作">Python在Spark上的机器学习(二)之数据操作</h1>



<h2 id="pyspark">PySpark</h2>

<p>PySpark 是 Spark 为 Python 开发者提供的 API。 <br>
Spark是用Scala语言写成的，Scala把要编译的东西编译为Java虚拟机（JVM）的字节码（bytecode）。Spark的开源社区开发了一个叫PySpark的工具库。它允许使用者用Python处理RDD。这多亏了一个叫Py4J的库，它让Python可以使用JVM的对象（比如RDD）。  <br>
Pyspark Internals这篇<a href="https://cwiki.apache.org/confluence/display/SPARK/PySpark+Internals">wiki</a>里介绍了pyspark的实现机制，大体是下面这张图就可以表示：</p>

<p><img src="http://i.imgur.com/YlI8AqEl.png" alt="enter image description here" title=""></p>



<h2 id="resilient-distributed-datasets">Resilient Distributed Datasets</h2>

<p>说到Spark上的数据模式，一定不能少的就是Spark中的核心：RDD了。与许多专有的大数据处理平台不同，Spark建立在统一抽象的RDD之上，使得它可以以基本一致的方式应对不同的大数据处理场景，包括MapReduce，Streaming，SQL，Machine Learning以及Graph等。这即Matei Zaharia所谓的“设计一个通用的编程抽象（Unified Programming Abstraction）。这正是Spark让人着迷的地方。</p>

<p><strong>RDD 具体是什么呢？</strong> <br>
RDD，全称Resilient Distributed Datasets，又称弹性分布式数据集。是一个可容错的、并行的数据结构，可以让用户显示地将数据储存到磁盘和内存当中，并能控制数据的分区。</p>

<p>RDD本质上是一个内存数据集，在访问RDD时，指针只会指向与操作相关的部分。例如存在一个面向列的数据结构，其中一个实现为Int的数组，另一个实现为Float的数组。如果只需要访问Int字段，RDD的指针可以只访问Int数组，避免了对整个数据结构的扫描。</p>

<p>RDD将操作分为两类：transformation与action。无论执行了多少次transformation操作，RDD都不会真正执行运算，只有当action操作被执行时，运算才会触发。而在RDD的内部实现机制中，底层接口则是基于迭代器的，从而使得数据访问变得更高效，也避免了大量中间结果对内存的消耗。</p>



<h2 id="使用pyspark">使用Pyspark</h2>

<p>在按照系列的上一个教程搭建好环境后，在终端中直接输入pyspark就可以运行Python与Spark的交互式的shell了。</p>

<p>那么，下面我们就以一些简单的例子来使用pyspark。</p>



<h3 id="创建rdd">创建RDD</h3>




```python
data = sc.parallelize(
    [('Amber', 22), ('Alfred', 23), ('Skye',4), ('Albert', 12), 
     ('Amber', 9)])
data
```


<p>输出：</p>

<blockquote>
  <p>ParallelCollectionRDD[3] at parallelize at PythonRDD.scala:475</p>
</blockquote>



<h3 id="rdd对象转换成python对象">RDD对象转换成Python对象</h3>

```python
data_heterogenous = sc.parallelize([('Ferrari', 'fast'), {'Porsche': 100000}, ['Spain','visited', 4504]]).collect()
data_heterogenous
```
<p>输出：</p>

<blockquote>
  <p>[(‘Ferrari’, ‘fast’), {‘Porsche’: 100000}, [‘Spain’, ‘visited’, 4504]]</p>
</blockquote>



<h3 id="读取文件及统计词频">读取文件及统计词频</h3>

<p>首先word.txt文件内容如下：</p>

<blockquote>
  <p>The dynamic lifestyle <br>
  people lead nowadays <br>
  causes many reactions <br>
   in our bodies and <br>
   the one that is the <br>
   most frequent of all <br>
   is the headache. However so good</p>
</blockquote>

```python
from operator import add
lines = sc.textFile('word.txt')
counts = lines.flatMap(lambda x: x.split(' '))\
.map(lambda x : (x, 1))\
.reduceByKey(add)
output = counts.collect()
print(output)
for (word, count) in output:
    print("%s: %i" %(word, count))
```

<p>输出：</p>

<blockquote>
  <p>[(”, 4), (‘good’, 1), (‘in’, 1), (‘is’, 2), (‘However’, 1), (‘of’, 1), (‘causes’, 1), (‘lifestyle’, 1), (‘The’, 1), (‘headache.’, 1), (‘reactions’, 1), (‘most’, 1), (‘frequent’, 1), (‘that’, 1), (‘all’, 1), (‘our’, 1), (‘dynamic’, 1), (‘nowadays’, 1), (‘so’, 1), (‘the’, 3), (‘people’, 1), (‘bodies’, 1), (‘many’, 1), (‘one’, 1), (‘and’, 1), (‘lead’, 1)] <br>
  …</p>
</blockquote>



<h2 id="dataframe">DataFrame</h2>

<p>DataFrameDataFrame是Spark推荐的统一结构化数据接口，是一个不可变的分布式数据集合，它结构与关系数据库中的表类似。</p>

<p>类似于Python Pandas DataFrame或R DataFrame，它能够让用户轻松处理结构化数据。</p>

<p>DataFrame还允许用户通过Spark SQL数据库或者采用一些函数式的方法查询及操作结构数据，下面我们就通过一些例子来了解和使用DataFrame。</p>



<h3 id="创建dataframes">创建DataFrames</h3>


```python
stringJSONRDD = sc.parallelize(("""
{ "id": "123",
"name": "Katie",
"age": 19,
"eyeColor": "brown"
}""",
"""{
"id": "234",
"name": "Michael",
"age": 22,
"eyeColor": "green"
}""",
"""{
"id": "345",
"name": "Simone",
"age": 23,
"eyeColor": "blue"
}""")
)
swimmersJSON = spark.read.json(stringJSONRDD)
swimmersJSON.show()
swimmersJSON.createOrReplaceTempView("swimmersJSON") #这里创建了临时表
```

<p>输出：</p>

<blockquote>
  <p>+—+——–+—+——-+ <br>
  |age|eyeColor| id|   name| <br>
  +—+——–+—+——-+ <br>
  | 19|   brown|123|  Katie| <br>
  | 22|   green|234|Michael| <br>
  | 23|    blue|345| Simone| <br>
  +—+——–+—+——-+</p>
</blockquote>



<h3 id="dataframe的简单内容及类型查询">DataFrame的简单内容及类型查询</h3>



```python
spark.sql("select * from swimmersJSON").collect()
swimmersJSON.printSchema() #显示表数据的类型
```

<p>输出：</p>

<blockquote>
  <p>[Row(age=19, eyeColor=’brown’, id=’123’, name=’Katie’), <br>
   Row(age=22, eyeColor=’green’, id=’234’, name=’Michael’), <br>
   Row(age=23, eyeColor=’blue’, id=’345’, name=’Simone’)] <br>
   root <br>
   |– age: long (nullable = true) <br>
   |– eyeColor: string (nullable = true) <br>
   |– id: string (nullable = true) <br>
   |– name: string (nullable = true)</p>
</blockquote>



<h3 id="指定数据储存及处理的类型">指定数据储存及处理的类型</h3>



```python
# Import types
from pyspark.sql.types import *
# Generate comma delimited data
stringCSVRDD = sc.parallelize([
(123, 'Katie', 19, 'brown'),
(234, 'Michael', 22, 'green'),
(345, 'Simone', 23, 'blue')
])
# Specify schema
schema = StructType([
StructField("id", LongType(), True),
StructField("name", StringType(), True),
StructField("age", LongType(), True),
StructField("eyeColor", StringType(), True)
])
# Apply the schema to the RDD and Create DataFrame
swimmers = spark.createDataFrame(stringCSVRDD, schema)
# Creates a temporary view using the DataFrame
swimmers.createOrReplaceTempView("swimmers")
swimmers.printSchema()
```

<p>输出：</p>

<blockquote>
  <p>root <br>
   |– id: long (nullable = true) <br>
   |– name: string (nullable = true) <br>
   |– age: long (nullable = true) <br>
   |– eyeColor: string (nullable = true)</p>
</blockquote>

<p><strong>明明可以自动匹配储存类型，为什么我们还要手动指定类型呢？</strong> <br>
因为，在自动匹配类型的情况下，有时会将ID，Age等我们未来将要用来计算的数据以String的方式存储，这样就不利于我们对这些数据进行加减等运算，所以手动指定储存类型还是很有必要的。</p>



<h3 id="使用sql语句查询及操作数据">使用SQL语句查询及操作数据</h3>


```python
spark.sql("select count(1) from swimmers").show()
spark.sql("select id, age from swimmers where age = 22").show()
spark.sql(
"select name, eyeColor from swimmers where eyeColor like 'b%'").show()
```

<p>输出：</p>

<blockquote>
  <p>+——–+ <br>
  |count(1)| <br>
  +——–+ <br>
  |       3| <br>
  +——–+</p>
  
  <p>+—+—+ <br>
  | id|age| <br>
  +—+—+ <br>
  |234| 22| <br>
  +—+—+</p>
  
  <p>+——+——–+ <br>
  |  name|eyeColor| <br>
  +——+——–+ <br>
  | Katie|   brown| <br>
  |Simone|    blue| <br>
  +——+——–+</p>
</blockquote>



<h2 id="小结">小结</h2>

<p>在这篇文章中我们可以看出，通过Pyspark结合RDD与DataFrames让我们可以用Python用上Spark平台上的分布式优势，也能够进一步加速和优化我们平时的数据操作。通过Spark导出的抽象层的API我们无需学过过于复杂和繁多的语法就能操作RDD上的数据。这篇文章的内容主要是为了后面用在用Python在Spark进行数据建模和机器学习所铺路，但受限于文章篇幅，还有十分多的函数和API无提及。所以有兴趣的读者可以阅读下<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html">Spark DataFrame的官方文档</a>深入了解一下。</p>
