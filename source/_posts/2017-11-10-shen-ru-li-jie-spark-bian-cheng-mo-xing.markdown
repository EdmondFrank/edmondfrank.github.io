---
layout: post
title: "深入理解Spark 编程模型"
date: 2017-11-10 13:14:53 +0800
comments: true
categories: pyspark&spark
---
﻿<h1 id="深入理解spark-编程模型">深入理解Spark 编程模型</h1>



<h2 id="spark的编程模型">Spark的编程模型</h2>

<p>Spark的应用程序主要由两部分组成：</p>

<ul>
<li>Driver</li>
<li>Executor</li>
</ul>

<p>除此之外，在Spark的编程模型的构成还包括许多其他的成分，如：<strong>SparkContext</strong>，这是Spark的应用程序的入口，负责调度各个运算资源，协调各个Worker节点上的Executor；</p>

<p>而<strong>Dirver program</strong>则负责运行Spark应用的main()函数并创建SparkContext，通常情况下，我们用SparkContext来指代Driver program。</p>

<p><strong>Executor</strong>：这是Spark应用中运行在Work Node上的一个进程，该进程负责运行Task，并且负责将数据存在内存和磁盘上，每个应用都会申请自己的Executors来负责调度和处理。</p>

<p>其次，在Spark编程模型中还有以下重要的概念，需要了解：</p>

<ul>
<li><strong>Application</strong>：Spark的应用程序，包含一个Driver program 和 若干个Executor</li>
<li><strong>Cluster Manager</strong>：在集群上获取资源的外部服务</li>
<li><strong>Work Node</strong>：集群中任何可以运行Application代码的节点，其中运行着一个或多个Executor进程。</li>
<li><strong>Job</strong>：可以被拆分成Task的并行计算的工作单元，一般由Spark Action触发的一次执行作业</li>
<li><strong>Stage</strong>：每个Job会被拆分成很多个Task，而每组任务就被称作Stage（相当于一个TaskSet）</li>
<li><strong>Task</strong>：运行在Executor上的工作单元</li>
<li><strong>RDD</strong>：弹性分布式数据集的简称，是Spark的最核心的模块和类之一</li>
</ul>



<h2 id="hadoop数据集">Hadoop数据集</h2>

<p>Spark可以将任何Hadoop所支持的存储资源转化成RDD，例如：本地文件，HDFS，Cassandra，HBase等。同时，Spark不仅支持文本文件和SequenceFiles还兼容任何Hadoop InputFormat的格式。</p>



<h3 id="textfile方法">textFile()方法</h3>

<p>使用textFile()可以将本地文件或HDFS文件转化成RDD</p>

<p><strong>读取整个文件目录</strong></p>

<blockquote>
  <p>textFile(“file:///hfds/directory”)</p>
</blockquote>

<p><strong>读取文本或压缩文件（可以自动执行解压缩并加载文件数据）</strong></p>

<blockquote>
  <p>textFile(“file:///hfds/directory/data.gz”)</p>
</blockquote>

<p><strong>使用通配符进行读取</strong></p>

<blockquote>
  <p>textFile(“file:///hfds/data/*.csv”)</p>
</blockquote>

<p>对于其他格式数据的读取有以下的方法：</p>

<ul>
<li><strong>wholeTextFiles()</strong>:读取目录里的小文件，返回由（用户名，内容）结构构成的键值对</li>
<li><strong>sequenceFile<a href="">K,V</a></strong>:可以将SequenceFile转换成RDD</li>
<li><strong>SparkContext.hadoopRDD</strong>:可以将其他任何Hadoop输入类型转换成RDD使用</li>
</ul>



<h2 id="rdd">RDD</h2>

<p><strong>RDD</strong>，弹性分布数据集，是Spark最核心的东西，他表示已被分区，不可变的并能够被并行操作的数据集合，不同的数据集格式对应着不同的RDD的实现。RDD的前提是其必须是可序列化的，同时RDD可以cache到内存之中</p>



<h3 id="特点">特点</h3>

<ol>
<li>只能通过转换操作（如map/filter/groupBy/join等）来从规定数据源（稳定存储的数据或其他RDD）中创建RDD</li>
<li>状态不可变，即不能修改</li>
<li>容错性强，由于RDD中的元素会根据key来分区，并保存在多个节点上，还原时只会重新计算丢失的分区的数据，不会影响整个系统的使用</li>
<li>RDD中会保存他的继承信息，即关于它是如何从其他RDD中生成的信息</li>
<li>被重用的RDD会缓存在内存中，或溢出至磁盘作持久化存储</li>
<li>Spark会延迟计算RDD，这样RDD就能够转换管道化（pipeline）</li>
<li>有丰富的动作（action）如：count/reduce/collect/save等支持</li>
<li>惰性求值，即执行了多少次transformation操作，RDD都不会真正执行运算，而只有action操作执行时，运算才会触发</li>
</ol>



<h2 id="rdd的元数据">RDD的元数据</h2>

<p>每个RDD都包含了5部分的信息，他们包括数据分区的集合，能根据本地性快速访问数据的偏好位置（最佳位置），依赖关系，计算方法（函数），分区策略。</p>

<p>示例：</p>

<p><img src="https://i.loli.net/2017/11/10/5a04866bbb6c5.png" alt="rdd_meta.png" title=""></p>



<h2 id="rdd的操作">RDD的操作</h2>

<p>RDD中的操作主要分为两大类：</p>

<ul>
<li><strong>转换(transformation):</strong>现有的RDD通过转换来生成一个新的RDD，转换是延迟执行（惰性求值）的。</li>
<li><strong>动作(actions):</strong>在RDD上执行动作后，就会运行计算，然后返回结果给驱动程序或者写入文件系统，从而触发Job。</li>
</ul>

<p>常用transformation：</p>

<p><img src="https://i.loli.net/2017/11/10/5a0487f0527d5.png" alt="rdd_transformation.png" title=""></p>

<p>常用actions：</p>

<p><img src="https://i.loli.net/2017/11/10/5a0487f047dab.png" alt="rdd_actions.png" title=""></p>



<h3 id="持久化">持久化</h3>

<p>缓存的操作 <br>
使用<strong>persist</strong>和<strong>cache</strong>方法可以将任意RDD缓存在内存或磁盘文件中，缓存不仅可以加速RDD的读取速度同时兼备了容错性，可以通过构建他的transformation自动重构。</p>

<p><strong>缓存</strong>是Spark最重要的一个功能，就是在不同操作间，持久化（或缓存）一个数据集在内存中。当你持久化一个RDD，每一个结点都将把它的计算分块结果保存在内存中，并在对此数据集（或者衍生出的数据集）进行的其它动作中重用。这将使得后续的动作(actions)变得更加迅速（通常快10倍）。所以缓存是用Spark构建迭代算法的关键。</p>

<p>如果你需要删除被持久化的RDD，可以用unpersistRDD()来完成该工作。</p>

<p>此外，每一个RDD都可以用不同的保存级别进行保存，从而允许你持久化数据集在硬盘，或者在内存作为序列化的Java对象（节省空间），甚至于跨结点复制。这些等级选择，是通过将一个<strong>org.apache.spark.storage.StorageLevel</strong>对象传递给persist()方法进行确定。</p>

<p>cache()方法是使用默认存储级别的快捷方法，也就是StorageLevel.MEMORY_ONLY(将反序列化的对象存入内存）。</p>

<p>StorageLevel有五个属性，分别是：</p>

<ul>
<li>useDisk_是否使用磁盘</li>
<li>useMemory_是否使用内存</li>
<li>useOffHeap_是否使用堆外内存如：Tachyon</li>
<li>deserialized_是否进行反序列化</li>
<li>replication_备份数目。</li>
</ul>

<p><strong>存储级别的选择</strong> <br>
Spark的不同存储级别，旨在满足内存使用和CPU效率权衡上的不同需求。我们建议通过以下的步骤来进行选择：</p>

<ul>
<li>如果你的RDDs可以很好的与默认的存储级别(MEMORY_ONLY)契合，就不需要做任何修改了。这已经是CPU使用效率最高的选项，它使得RDDs的操作尽可能的快。</li>
<li>如果不行，试着使用MEMORY_ONLY_SER并且选择一个快速序列化的库使得对象在有比较高的空间使用率的情况下，依然可以较快被访问。</li>
<li>尽可能不要存储到硬盘上，除非计算数据集的函数，计算量特别大，或者它们过滤 <br>
了大量的数据。否则，重新计算一个分区的速度，和与从硬盘中读取基本差不多快。</li>
</ul>

