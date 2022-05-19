---
layout: post
title: "Python在Spark上的机器学习(一)之环境搭建"
date: 2017-10-16 14:07:29 +0800
comments: true
categories: Pyspark&Spark
---
﻿<h1 id="python在spark上的机器学习一之环境搭建">Python在Spark上的机器学习(一)之环境搭建</h1>

<p>前面已经介绍了不少机器学习的算法了，那么机器学习又该如何结合大数据一起使用么？</p>



<h2 id="常言道工欲善其事必先利其器">常言道：工欲善其事，必先利其器</h2>

<p>既然来结合大数据与机器学习，我们就不得不提Spark了。</p>

<p>首先，<a href="https://spark.apache.org/docs/latest/index.html">Apache Spark</a> 是专为大规模数据处理而设计的快速通用的计算引擎。Spark是UC Berkeley AMP lab (加州大学伯克利分校的AMP实验室)所开源的类Hadoop MapReduce的通用并行框架，Spark，拥有Hadoop MapReduce所具有的优点；但不同于MapReduce的是Job中间输出结果可以保存在内存中，从而不再需要读写HDFS，因此Spark能更好地适用于数据挖掘与机器学习等需要迭代的MapReduce的算法。</p>

<p>Spark 是一种与 Hadoop 相似的开源集群计算环境，但是两者之间还存在一些不同之处，这些有用的不同之处使 Spark 在某些工作负载方面表现得更加优越，换句话说，Spark 启用了内存分布数据集，除了能够提供交互式查询外，它还可以优化迭代工作负载。</p>

<p>讲了这么多Spark的优点，那么现在我们就先开始来搭建一个Spark 集群环境吧！</p>



<h3 id="安装基础环境">安装基础环境</h3>

<p><strong>1. Java1.8环境搭建</strong>（下载JDK1.8的）：</p>

<blockquote>
  <p>下载页面： <br>
  <a href="http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html">http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html</a></p>
</blockquote>

<p>安装过程可以参考<a href="http://www.linuxidc.com/Linux/2016-05/131348.htm">Linux公社给出的教程</a></p>

<p><strong>Ubuntu用户：</strong> <br>
Ubuntu用户可以通过添加PPA源再通过Apt来进行安装</p>

<blockquote>
  <p><span>$</span> sudo add-apt-repository ppa:webupd8team/java <br>
  <span>$</span> sudo apt-get update <br>
  <span>$</span> sudo apt-get install oracle-java8-installer</p>
</blockquote>

<p><strong>2. Scala环境搭建</strong></p>

<p>下载scala安装包：</p>



<pre class="prettyprint"><code class=" hljs avrasm">wget -O <span class="hljs-string">"scala-2.12.3.deb"</span> 
<span class="hljs-label">https:</span>//downloads<span class="hljs-preprocessor">.lightbend</span><span class="hljs-preprocessor">.com</span>/scala/<span class="hljs-number">2.12</span><span class="hljs-number">.3</span>/scala-<span class="hljs-number">2.12</span><span class="hljs-number">.3</span><span class="hljs-preprocessor">.deb</span>
或者
wget -O <span class="hljs-string">"scala-2.12.3.rpm"</span> <span class="hljs-string">"https://downloads.lightbend.com/scala/2.12.3/scala-2.12.3.rpm"</span>
</code></pre>

<p>安装deb/rpm包：</p>



<pre class="prettyprint"><code class=" hljs avrasm">rpm -ivh scala-<span class="hljs-number">2.12</span><span class="hljs-number">.3</span><span class="hljs-preprocessor">.rpm</span>
dpkg -i scala-<span class="hljs-number">2.12</span><span class="hljs-number">.3</span><span class="hljs-preprocessor">.deb</span></code></pre>

<p>增加SCALA_HOME</p>

<blockquote>
  <p>$ vim /etc/profile</p>
</blockquote>

<p>增加如下内容;</p>

<blockquote>
  <p>export SCALA_HOME=/usr/share/scala</p>
</blockquote>

<p>刷新配置</p>

<blockquote>
  <p>$ source /etc/profile</p>
</blockquote>



<h3 id="安装hadoop">安装Hadoop</h3>

<p><strong>1.下载二进制包：</strong> <br>
wget <a href="http://www-eu.apache.org/dist/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz">http://www-eu.apache.org/dist/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz</a></p>

<p><strong>2.解压并移动至相应目录：</strong></p>

<p>我的习惯是将软件放置/opt目录下：</p>

<blockquote>
  <p>tar -xvf hadoop-2.7.3.tar.gz <br>
  mv hadoop-2.7.3 /opt</p>
</blockquote>

<p><strong>3.修改相应的配置文件：</strong></p>

<blockquote>
  <p><strong>（1）</strong> $ vim /etc/profile</p>
</blockquote>

<p>增加如下内容：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment">#hadoop enviroment </span>
<span class="hljs-keyword">export</span> HADOOP_HOME=/opt/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>/
<span class="hljs-keyword">export</span> PATH=<span class="hljs-string">"<span class="hljs-variable">$HADOOP_HOME</span>/bin:<span class="hljs-variable">$HADOOP_HOME</span>/sbin:<span class="hljs-variable">$PATH</span>"</span>
<span class="hljs-keyword">export</span> HADOOP_CONF_DIR=<span class="hljs-variable">$HADOOP_HOME</span>/etc/hadoop
<span class="hljs-keyword">export</span> YARN_CONF_DIR=<span class="hljs-variable">$HADOOP_HOME</span>/etc/hadoop</code></pre>

<blockquote>
  <p><strong>（2）</strong> <span>$</span> vim $HADOOP_HOME/etc/hadoop/hadoop-env.sh</p>
</blockquote>

<p>修改JAVA_HOME 如下：</p>

<blockquote>
  <p>export JAVA_HOME=&lt;你的Java安装目录&gt;</p>
</blockquote>

<p>-</p>

<blockquote>
  <p><strong>（3）</strong> <span>$</span> vim $HADOOP_HOME/etc/hadoop/core-site.xml</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://master:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>io.file.buffer.size<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>131072<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
       <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/opt/hadoop-2.7.3/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<blockquote>
  <p><strong>（4）</strong> <span>$</span> vim $HADOOP_HOME/etc/hadoop/hdfs-site.xml</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.secondary.http-address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:50090<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>2<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/opt/hadoop-2.7.3/hdfs/name<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.datanode.data.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/opt/hadoop-2.7.3/hdfs/data<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<blockquote>
  <p><strong>（5）</strong> <span>$</span> vim $HADOOP_HOME/etc/hadoop/mapred-site.xml</p>
</blockquote>

<p>复制template，生成xml：</p>

<blockquote>
  <p>cp mapred-site.xml.template mapred-site.xml</p>
</blockquote>

<p>内容：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
 <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.jobhistory.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:10020<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.jobhistory.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:19888<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong>（6）</strong> <span>$</span> vim $HADOOP_HOME/etc/hadoop/yarn-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-comment">&lt;!-- Site specific YARN configuration properties --&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
     <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
     <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
           <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
           <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:8032<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
     <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
     <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.scheduler.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:8030<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
     <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.resource-tracker.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:8031<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
     <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
     <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.admin.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:8033<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
     <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
     <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.webapp.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:8088<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
     <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>至此master节点的hadoop搭建完毕</p>

<p>再启动之前我们需要</p>

<p>格式化一下namenode</p>

<blockquote>
  <p>$ hadoop namenode -format</p>
</blockquote>



<h3 id="安装spark">安装Spark</h3>

<p><strong>下载文件：</strong></p>



<pre class="prettyprint"><code class=" hljs mathematica">wget -<span class="hljs-keyword">O</span> <span class="hljs-string">"spark-2.1.0-bin-hadoop2.7.tgz"</span> <span class="hljs-string">"http://d3kbcqa49mib13.cloudfront.net/spark-2.1.0-bin-hadoop2.7.tgz"</span></code></pre>

<p><strong>解压并移动至相应的文件夹：</strong></p>

<blockquote>
  <p>tar -xvf spark-2.1.0-bin-hadoop2.7.tgz <br>
  mv spark-2.1.0-bin-hadoop2.7 /opt</p>
</blockquote>

<p><strong>修改相应的配置文件：</strong></p>

<blockquote>
  <p><strong>（1）</strong> <span>$</span> vim /etc/profie</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment">#Spark enviroment</span>
<span class="hljs-keyword">export</span> SPARK_HOME=/opt/spark-<span class="hljs-number">2.1</span>.<span class="hljs-number">0</span>-bin-hadoop2.<span class="hljs-number">7</span>/
<span class="hljs-keyword">export</span> PATH=<span class="hljs-string">"<span class="hljs-variable">$SPARK_HOME</span>/bin:<span class="hljs-variable">$PATH</span>"</span></code></pre>

<blockquote>
  <p><strong>（2）</strong> <span>$</span> vim $SPARK_HOME/conf/spark-env.sh</p>
</blockquote>

<p>-</p>

<blockquote>
  <p>cp spark-env.sh.template spark-env.sh</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment">#配置内容如下：</span>
<span class="hljs-keyword">export</span> SCALA_HOME=/usr/share/scala
<span class="hljs-keyword">export</span> JAVA_HOME=&lt;你的Java安装目录&gt;
<span class="hljs-keyword">export</span> SPARK_MASTER_IP=master
<span class="hljs-keyword">export</span> SPARK_WORKER_MEMORY=<span class="hljs-number">1</span>g
<span class="hljs-keyword">export</span> HADOOP_CONF_DIR=/opt/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>/etc/hadoop</code></pre>

<p>至此，我们大部分环境基本安装完毕！</p>



<h2 id="测试spark">测试Spark</h2>

<p>为了避免麻烦这里我们使用spark-shell以及本地的文件（非hdfs），做一个简单的worcount的测试。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">val <span class="hljs-built_in">file</span>=sc.textFile(<span class="hljs-string">"/home/ef/Desktop/Notes/wordcount_test"</span>)
val rdd = <span class="hljs-built_in">file</span>.flatMap(<span class="hljs-built_in">line</span> =&gt; <span class="hljs-built_in">line</span>.<span class="hljs-built_in">split</span>(<span class="hljs-string">" "</span>)).map(<span class="hljs-built_in">word</span> =&gt; (<span class="hljs-built_in">word</span>,<span class="hljs-number">1</span>)).reduceByKey(_+_)
rdd.collect()
rdd.foreach(println)</code></pre>

<p><strong>展示图：</strong> <br>
<img src="https://i.loli.net/2017/10/10/59dc80d01e9ff.png" alt="spark-shell.png" title=""></p>



<h2 id="小结">小结</h2>

<p>到此，我们在Spark上进行机器学习训练的环境，就搭建完毕了，下章我们再开始讲Spark中的数据结构与Python中的区别，以及结合Pyspark来进行数据处理。</p>
