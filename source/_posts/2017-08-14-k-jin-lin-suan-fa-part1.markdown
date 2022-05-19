---
layout: post
title: "K-近邻算法-Part1"
date: 2017-08-14 23:48:56 +0800
comments: true
categories: 机器学习
---
<h1 id="k-近邻算法-part1">K-近邻算法-Part1</h1>



<h2 id="概述">概述</h2>

<p>K-近邻算法，即K-近邻分类算法，简称KNN其通过采用测量不同的特征值之间的距离方法进行分类。</p>



<h3 id="有关k-近邻算法的问题">有关K-近邻算法的问题</h3>

<p>优点：精度高，对异常值不敏感，无数据输入假定</p>

<p>缺点：计算复杂度较高，空间复杂度较高</p>

<p>适用数据范围：数值型和标称型</p>



<h3 id="工作原理">工作原理</h3>

<p>存在一个样本数据集合，即训练样本集，并且训练样本中的每一个数据都存在标签，我们可以清楚地知道每一个数据条目其与对应分类的所属关系。</p>

<p>然后在接受没有标记的新数据输入时，将新数据的特征提取出来将其一一与训练样本集中数据对应的特征进行比较，然后算法提取样本集中特征最相似的（即所谓的最近邻）的分类标签来作为新数据的标签。</p>

<p>其中为了避免偶尔性和离群值造成的误差因此有了以样本数据集中前k个最相似的数据作为判别的参考的标准这种做法。<strong>通常k是不大于20的整数而且一般选取奇数作为k值</strong>（奇数可以在投票分类时避免出现等票的情况），最终的分类结果由k个样本的分类标签投票形成，出现最多的分类标签作为新数据的分类。</p>



<h3 id="一般算法流程">一般算法流程</h3>

<ol>
<li>收集数据</li>
<li>准备数据：对数据进行清洗和结构化处理，使得数据可以进行距离计算</li>
<li>分析数据：提取相关特征</li>
<li>训练分类：knn算法并不需要训练</li>
<li>测试算法：计算错误率</li>
<li>使用算法：将新数据输入进行对应结构化之后，运行算法进行判定分类情况，并对后续的分类结果进行进一步处理应用</li>
</ol>



<h2 id="用knn-制作简单的分类器">用KNN 制作简单的分类器</h2>

<p>使用数据：<a href="https://archive.ics.uci.edu/ml/datasets/Iris">UCI的鸢尾花数据集</a> <br>
点击进入目标链接后: </p>

<blockquote>
  <p>按照 Download Data Folder &gt; iris.data 路径来下载指定数据集</p>
</blockquote>



<h4 id="准备数据">准备数据</h4>

<p>在这里我们使用Python 的 Pandas 包以没有标题栏的csv文件的形式读入数据</p>

<p><strong>关键函数：read_csv</strong></p>



```python

# loading libraries
import pandas as pd

# define column names
names = ['sepal_length', 'sepal_width', 'petal_length', 'petal_width', 'class']

# loading training data
df = pd.read_csv('iris.data.txt', header=None, names=names)
df.head()
```

<p><strong>输出</strong>：</p>

<table>
<thead>
<tr>
  <th>index</th>
  <th>sepal_length</th>
  <th>sepal_width</th>
  <th>petal_length</th>
  <th>petal_width</th>
  <th>class</th>
</tr>
</thead>
<tbody><tr>
  <td>0</td>
  <td>5.1</td>
  <td>3.5</td>
  <td>1.4</td>
  <td>0.2</td>
  <td>Iris-setosa</td>
</tr>
<tr>
  <td>1</td>
  <td>4.9</td>
  <td>3.0</td>
  <td>1.4</td>
  <td>0.2</td>
  <td>Iris-setosa</td>
</tr>
<tr>
  <td>2</td>
  <td>4.7</td>
  <td>3.2</td>
  <td>1.3</td>
  <td>0.2</td>
  <td>Iris-setosa</td>
</tr>
<tr>
  <td>3</td>
  <td>4.6</td>
  <td>3.1</td>
  <td>1.5</td>
  <td>0.2</td>
  <td>Iris-setosa</td>
</tr>
<tr>
  <td>4</td>
  <td>5.0</td>
  <td>3.6</td>
  <td>1.4</td>
  <td>0.2</td>
  <td>Iris-setosa</td>
</tr>
</tbody></table>




<h4 id="构建算法">构建算法</h4>

<p><strong>距离函数</strong></p>

<p>我a们在上面的例子中把一个很重要的概念隐藏了起来，在选择一个数量k还只是小问题，更重要的是距离的计算方法。毕竟，当我们说“最近的k个点”时，这个“近”是怎么衡量的？</p>

<p>在数学中，一个空间上距离的严格定义如下： <br>
设 M 为一个空间，M上的一个距离函数是一个函数<script type="math/tex" id="MathJax-Element-383">d:M\times M \rightarrow R</script>，满足：</p>

<ul>
<li><script type="math/tex" id="MathJax-Element-384">d(x,y)≥0  ∀x,y∈M</script></li>
<li><script type="math/tex" id="MathJax-Element-385">d(x,y)=0⟺x=y</script></li>
<li><script type="math/tex" id="MathJax-Element-386">d(x,y)=d(y,x) ∀x,y∈M</script></li>
<li><script type="math/tex" id="MathJax-Element-387">d(x,z)≤d(x,y)+d(y,z) ∀x,y,z∈M</script></li>
</ul>

<p>两个点 x,y 之间的距离就是<script type="math/tex" id="MathJax-Element-388">d(x,y)</script>。</p>

<p>我们一般最常用的距离函数是欧氏距离，也称作<script type="math/tex" id="MathJax-Element-389">L_2</script>距离。</p>

<p>如果 <br>
<script type="math/tex" id="MathJax-Element-390">x=(x1,x2,…,xn)</script> 和 <script type="math/tex" id="MathJax-Element-391">y=(y1,y2,…,yn)</script>是 n 维欧式空间 Rn 上的两个点，那它们之间的<script type="math/tex" id="MathJax-Element-392">L_2</script>距离是</p>

<p><script type="math/tex" id="MathJax-Element-393">d_2(X,Y)=\sqrt{\sum _{i=1}^n \left(X_i-Y_i\right){}^2}</script></p>

<p>由于Python 的scikit-learn包已经实现了KNN算法，因此我们在这里可以直接调用。</p>

<p>在<a href="http://scikit-learn.org/stable/index.html">scikit-learn</a>中，需要以matrix的形式和目标向量的形式来导入和训练数据。</p>

<p>因此在使用scikit-learn之前需要做额外的数据结构处理，同时还应该把原数据划分成训练数据和测试数据这样更加有利于我们下面的算法正确率评估。</p>

```python

# loading libraries
import numpy as np
from sklearn.cross_validation import train_test_split

# create design matrix X and target vector y
X = np.array(df.ix[:, 0:4]) 	# end index is exclusive
y = np.array(df['class']) 	# another way of indexing a pandas df

# split into train and test
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.33, random_state=42)
```

<p>最后，我们根据划分好的数据集来构建真正的分类器，并用构建出来的分类器来进行数据拟合以及评估他的正确率。</p>


```python

# loading library
from sklearn.neighbors import KNeighborsClassifier
from sklearn import metrics
# instantiate learning model (k = 3)
knn = KNeighborsClassifier(n_neighbors=3)

# fitting the model
knn.fit(X_train, y_train)

# predict the response
pred = knn.predict(X_test)

# evaluate accuracy
# print(y_test,pred)
print(metrics.accuracy_score(y_test, pred))
```

<p><strong>输出</strong></p>

<blockquote>
  <p>0.98</p>
</blockquote>

<p>由此可见，在适用的场合之下，KNN分类器的准确率也是可以达到一个较为理想的水平的。</p>
