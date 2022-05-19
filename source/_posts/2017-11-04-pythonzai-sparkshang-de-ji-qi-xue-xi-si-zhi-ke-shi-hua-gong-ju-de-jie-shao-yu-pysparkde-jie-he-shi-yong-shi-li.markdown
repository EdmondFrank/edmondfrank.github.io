---
layout: post
title: "Python在Spark上的机器学习(四)之可视化工具的介绍与PySpark的结合使用示例"
date: 2017-11-04 23:20:17 +0800
comments: true
categories: pyspark&spark
---

﻿<h1 id="python在spark上的机器学习四之可视化工具的介绍与pyspark的结合使用示例">Python在Spark上的机器学习(四)之可视化工具的介绍与PySpark的结合使用示例</h1>



<h2 id="前言">前言</h2>

<p>在Python和Java的生态圈中，有许多可用的可视化库，但是在这篇文章中，我们主要来介绍一下matplotlib 和 Bokeh的使用。</p>

<p>首先，这两个库都是<a href="https://www.anaconda.com/">Anaconda</a>预装的。如果你是通过Anaconda来搭建的Python的科学计算环境的话，直接就可以通过import导入来使用这两个库了。</p>

<p>但是如果还没安装和配置好环境的朋友，可以自行参考<a href="http://matplotlib.org/index.html">Matplotlib</a>和<a href="https://bokeh.pydata.org/en/latest/">Bokeh</a>的官方站点的教程来下载配置环境。</p>

<blockquote>
  <p>注：这一类对各系统平台支持良好的库，一般安装流程也就无非两条pip命令，如： <br>
  python -mpip install -U pip <br>
  python -mpip install -U matplotlib <br>
  pip install bokeh <br>
  或 <br>
  conda install bokeh <br>
  所以各位读者也没有必要担心配置麻烦。</p>
</blockquote>



<h2 id="有关matplotlib和bokeh的介绍">有关matplotlib和bokeh的介绍</h2>



<h3 id="matplotlib">Matplotlib</h3>

<p><strong>Matplotlib</strong>是一个Python 2D绘图库，可以跨平台生成各种通用格式和适用于交互式环境的高质量图表。 Matplotlib可直接用于Python脚本，IPython shell，Jupyter以及Web应用程序服务器之中。 <br>
<strong>Matplotlib</strong>简化了许多繁琐的绘图操作，使得原本简单的图表在绘制上更加简单，而复杂的图表绘制也更容易上手。只需几行代码即可生成许多好看的图表。如，直方图、功率谱、条形图、错误图，散点图等。</p>

<p>官方绘图预览：</p>

<p><img src="http://matplotlib.org/_images/sphx_glr_simple_plot_0011.png" alt="enter image description here" title=""> <br>
<img src="http://matplotlib.org/_images/sphx_glr_histogram_features_0011.png" alt="enter image description here" title=""></p>

<p><img src="http://matplotlib.org/_images/sphx_glr_barchart_demo_0011.png" alt="enter image description here" title=""> <br>
<img src="http://matplotlib.org/_images/sphx_glr_pie_features_0011.png" alt="enter image description here" title=""></p>



<h3 id="bokeh">Bokeh</h3>

<p><strong>Bokeh</strong> (Bokeh.js) 是一个 Python 交互式可视化库，支持现代化 Web 浏览器，提供非常完美的展示功能。Bokeh 的目标是使用 D3.js 样式提供优雅，简洁新颖的图形化风格，同时提供大型数据集的高性能交互功能。Boken 可以快速的创建交互式的绘图，仪表盘和数据应用。</p>

<p>鉴于Bokeh强调的更多是一种交互式的绘图体验，在这里我就不貼静态图了，不过下面我会附上一些官方demo的例子，让大家感受下Bokeh的强大之处。</p>

<p><a href="https://bokeh.pydata.org/en/latest/docs/gallery/stocks.html">趋势走向图</a></p>

<p><a href="https://bokeh.pydata.org/en/latest/docs/gallery/iris.html">散点图</a></p>

<p><a href="https://bokeh.pydata.org/en/latest/docs/gallery/texas.html">地域分布图</a></p>

<p><a href="https://bokeh.pydata.org/en/latest/docs/gallery/boxplot.html">箱型图</a></p>



<h2 id="结合pyspark进行可视化分析">结合PySpark进行可视化分析</h2>



<h3 id="模块加载">模块加载</h3>

<p>以下实验均在Jupyter环境下进行 <br>
<strong>matplotlib</strong></p>



<pre class="prettyprint"><code class="language-python hljs ">%matplotlib inline
<span class="hljs-keyword">import</span> matplotlib.pyplot <span class="hljs-keyword">as</span> plt
plt.style.use(<span class="hljs-string">'ggplot'</span>)</code></pre>

<p><strong>bokeh</strong></p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-keyword">import</span> bokeh.charts <span class="hljs-keyword">as</span> chrt
<span class="hljs-keyword">from</span> bokeh.io <span class="hljs-keyword">import</span> output_notebook
output_notebook()</code></pre>



<h3 id="频率分布分析">频率分布分析</h3>

<p>频率分布图是最为简单有效的观察数据的分布情况的方法之一。</p>



<h4 id="读取数据">读取数据</h4>

<p>本文用到的数据文件依旧是上文所提及的信用欺诈检测的数据集，具体下载地址：<a href="http://tomdrabas.com/data/LearningPySpark/ccFraud.csv.gz">这里</a></p>


```python
import pyspark.sql.types as typ
fraud = sc.textFile('/home/ef/Desktop/learningPySpark-master/ccFraud.csv')
header = fraud.first()
fraud = fraud \
.filter(lambda row: row != header) \
.map(lambda row: [int(elem) for elem in row.split(',')])
fields = [
*[
typ.StructField(h[1:-1], typ.IntegerType(), True)
for h in header.split(',')
]
]
schema = typ.StructType(fields)
fraud_df = spark.createDataFrame(fraud, schema)
hists = fraud_df.select('balance').rdd.flatMap(
lambda row: row
).histogram(20)

fraud_df.printSchema()
```

<blockquote>
  <p>输出： <br>
  root <br>
   |– custID: integer (nullable = true) <br>
   |– gender: integer (nullable = true) <br>
   |– state: integer (nullable = true) <br>
   |– cardholder: integer (nullable = true) <br>
   |– balance: integer (nullable = true) <br>
   |– numTrans: integer (nullable = true) <br>
   |– numIntlTrans: integer (nullable = true) <br>
   |– creditLine: integer (nullable = true) <br>
   |– fraudRisk: integer (nullable = true)</p>
</blockquote>



<h4 id="绘制频率分布直方图">绘制频率分布直方图</h4>

<p><strong>matplotlib</strong></p>



<pre class="prettyprint"><code class="language-python hljs ">data = {
<span class="hljs-string">'bins'</span>: hists[<span class="hljs-number">0</span>][:-<span class="hljs-number">1</span>],
<span class="hljs-string">'freq'</span>: hists[<span class="hljs-number">1</span>]
}
plt.bar(data[<span class="hljs-string">'bins'</span>], data[<span class="hljs-string">'freq'</span>], width=<span class="hljs-number">2000</span>)
plt.title(<span class="hljs-string">'Histogram of \'balance\''</span>)
plt.show()</code></pre>

<blockquote>
  <p>输出: <br>
  <img src="https://ooo.0o0.ooo/2017/10/27/59f2fbe5dc612.png" alt="mat_hist.png" title=""></p>
</blockquote>

<p><strong>bokeh</strong></p>



<pre class="prettyprint"><code class="language-python hljs ">data = {
<span class="hljs-string">'bins'</span>: hists[<span class="hljs-number">0</span>][:-<span class="hljs-number">1</span>],
<span class="hljs-string">'freq'</span>: hists[<span class="hljs-number">1</span>]
}
b_hist = chrt.Bar(
data,
values=<span class="hljs-string">'freq'</span>, label=<span class="hljs-string">'bins'</span>,
title=<span class="hljs-string">'Histogram of \'balance\''</span>)
chrt.show(b_hist)</code></pre>

<blockquote>
  <p>输出: <br>
  <img src="https://ooo.0o0.ooo/2017/10/27/59f2fbe5e1f34.png" alt="bokeh_hist.png" title=""></p>
</blockquote>
