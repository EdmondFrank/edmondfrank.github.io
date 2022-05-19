---
layout: post
title: "SVM支持向量机"
date: 2017-10-11 16:38:50 +0800
comments: true
categories: 机器学习
---
﻿<h1 id="svmsupport-vector-machines-支持向量机">SVM(Support Vector Machines)-支持向量机</h1>

<p>曾经很多人都认为SVM是现在的最好的分类器，即：在不加以修改的前提之下，就能够在数据上进行训练并能够对训练集之外的数据点做出很好的分类决策。</p>

<h2 id="基于最大间隔分割数据">基于最大间隔分割数据</h2>

<p><strong>支持向量机：</strong> <br>
优点：泛化错误率低，计算开销不大，结果容易解释 <br>
缺点：对参数调节和核函数的选择策略敏感，原始分类器不加修改仅适用于处理二类问题。 <br>
<img src="http://scikit-learn.org/stable/_images/sphx_glr_plot_separating_hyperplane_0011.png" alt="enter image description here" title=""></p>

<p>在上图中，我们可以看出：我们可以很容易在图中画出一条直线将两组数据点分开。在这种情况下，这组数据被称为<strong>线性可分(linearly separable)</strong>数据，而将数据集分割开来的直线我们称为<strong>分隔超平面(separating hyperplane)</strong>。在上图例子中，由于数据点都在二维平面上，所有此时分隔超平面就只是一条直线而已。但是，如果所给的数据为三维的，那么此时用来分隔数据的就是一个平面。依此类推，如果给出一个N（N&gt;3）维数据集，那么则要用一个N-1维的对象来对数据进行分隔，该对象亦被称为<strong>超平面(hyperplane)</strong>。</p>

<p>我们希望通过这样的方式来构建分类器。即如果数据点离决策边界越远，那么其最后的预测效果就越可信。那么我们继续看回上面的图片，我们可以看到有三条直线（一条实线，两条虚线）它们分别都能将数据分隔开，但是其中哪一条才是最理想的呢？通常，我们可以做过这样的方法来确定一个最佳分隔平面，即：我们先找到离分隔平面最近的点，确保它们离分隔平面的距离尽可能远。</p>

<p>这里我们又要先引入一些新的概念： <br>
首先是，点到分隔面的距离被称为<strong>间隔(margin)</strong> <br>
<strong>支持向量(support vector)：</strong>就是离分割平面最近的那些点。</p>

<p>接下来，就是最大化支持向量都分隔面的距离，并需要找到此问题的优化求解方法。</p>



<h2 id="寻找最大间隔">寻找最大间隔</h2>

<p><strong>Maximum Marginal（最大间隔）</strong>是SVM的一个理论基础之一。选择使得间隔最大的函数作为分隔平面是十分容易解释的。从概率的角度上而言，就是使得置信度最小的点置信度最大;从现实意义上而言，两个个体的类别差异越大，我们自然也能够更为准确地分类。</p>

<p><img src="https://i.loli.net/2017/10/11/59ddd657a3c93.png" alt="svm.png" title=""></p>

<p>上图就是一个对之前所提及的类别间隙的一个描述。其中中间的黑色实线为<strong>分隔边界（Classifier Boundary）</strong>是我们算法中要求解的<strong>f(x)</strong>，红色和蓝色的线(plus plane 与 minus plane)就是支持变量所在的平面，而红色，蓝色之间的间隙就是我们要最大化的分类间的间隙。</p>

<p>首先，我们可以知道其两个支持向量的所在平面可以表达成如下形式：</p>

<p><img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/a677ed33ca0c840aa9295405fc095c8aefa73e48" alt="enter image description here" title=""></p>

<p>和</p>

<p><img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/6c3dbeeb7d5af27a2551ec07ce172ddbce62fc58" alt="enter image description here" title=""></p>

<p>然后，这两个超平面的距离可以表达成：<img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/ac553c94aec996dffa3369adcf285319178a474f" alt="enter image description here" title=""></p>

<p>因此，为了最大化两个平面的距离，我们只要最小化<img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/5b6f27a892f3053ef0bfe273f88f18351a1a18ac" alt="" title="">即可。</p>

<p><strong>综上所述</strong>，我们可以将整个原理表示为：</p>

<blockquote>
  <p>Minimize<img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/5b6f27a892f3053ef0bfe273f88f18351a1a18ac" alt="" title="">  <br>
  subject to<img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/7181cf8e723b24ad6d85b73b9513dcaac0d31023" alt="" title=""> <br>
  for <img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/520ffef648f7b26db5bae564be860346630635fc" alt="enter image description here" title=""></p>
</blockquote>

<p>当支持变量确定的时候，整个分割函数也就确定了下来。除此之外，支持向量的出现还可以让向量后方的样本点不用再参与计算，大大降低了算法的计算复杂度。</p>

<p>再者，有关距离计算的优化。<script type="math/tex" id="MathJax-Element-41">||\vec w||</script>的意思是<script type="math/tex" id="MathJax-Element-42">\vec w</script>的二范数，由之前我们得到的最大间隔<script type="math/tex" id="MathJax-Element-43">M=2/||\vec w||</script>，最大化这个式子等价于最小化<script type="math/tex" id="MathJax-Element-44">|||\vec w||</script>，另外由于<script type="math/tex" id="MathJax-Element-45">||\vec w||</script>是一个单调函数，我们可以对其进行平方，和加上系数。数学经验丰富的朋友可能一眼就看出来，这样做是为了方便求导。</p>

<p>最后我们的式子可以写成：</p>

<p><img src="https://i.loli.net/2017/10/11/59ddd726bc801.png" alt="svmalg.png" title=""></p>

<p><strong>s.t的意思是subject to</strong>，也就是在后面这个限制条件下的意思，这个词在svm的论文里面非常容易见到。这其实是一个<strong>带约束的二次规划(quadratic programming, QP)问题</strong>，是一个<strong>凸问题</strong>，凸问题就是指的不会有局部最优解，可以想象一个漏斗，不管我们开始的时候将一个小球放在漏斗的什么位置，这个小球最终一定可以掉出漏斗，也就是得到全局最优解。s.t.后面的限制条件可以看做是一个凸多面体，我们要做的就是在这个凸多面体中找到最优解。</p>

<h2 id="优化求解">优化求解</h2>

<p>这个优化问题可以用<a href="https://en.wikipedia.org/wiki/Lagrange_multiplier">拉格朗日乘子法</a>去解，使用了<a href="https://en.wikipedia.org/wiki/Karush%E2%80%93Kuhn%E2%80%93Tucker_conditions">KKT条件</a>的理论，这里直接作出这个式子的拉格朗日目标函数： <br>
<img src="https://i.loli.net/2017/10/11/59ddd726bb94a.png" alt="Lagrange.png" title=""></p>

<p>由于求解这个式子的过程需要<a href="https://en.wikipedia.org/wiki/Quadratic_programming#Lagrangian_duality">拉格朗日对偶性</a>的相关知识，以及较深入的数学背景知识，在这篇文章中暂且不谈，以后博客再另外写一篇有关具体推导的文章。</p>

<p>在此，我先贴出在该问题在论文中的关键推导步骤：</p>

<p><img src="https://i.loli.net/2017/10/11/59ddd726dd5a4.png" alt="formulation.png" title=""></p>

<p><img src="https://i.loli.net/2017/10/11/59ddd726d9bc8.png" alt="dual.png" title=""></p>

<p>上图就是我们需要最终优化的式子了。整篇文章到这里，我们终于得到了SVM线性可分问题的优化式子。</p>

<h2 id="svm的使用">SVM的使用</h2>

<p>由于支持向量机算法的实现涉及过多的数学背景，在本中暂不使用原生程序代码实现，而是选择调用Python sklearn 库现有的SVM算法进行使用的举例。</p>

```python
from sklearn import svm
from sklearn import datasets
from sklearn import metrics
clf = svm.SVC()
print(clf)
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
pred = clf.predict(X)
print(metrics.accuracy_score(y,pred))
```

<blockquote>
  <p>输出结果 <br>
  SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0, <br>
    decision_function_shape=None, degree=3, gamma=’auto’, kernel=’rbf’, <br>
    max_iter=-1, probability=False, random_state=None, shrinking=True, <br>
    tol=0.001, verbose=False) <br>
  0.986666666667</p>
</blockquote>

<p>本例使用的是<strong>sklearn</strong>库自带的<strong>iris</strong>数据集使用SVM算法进行训练，然后再进行回测。首先，我们可以看到在不做任意处理的前提下，SVM模型的预估效果也是相当不错的，其准确率高达98%（其结果主要因为使用的数据集优秀导致的，直接运用于工程上时不会有这么高的准确率）。</p>

<p>当然，上面例子中的算法模型的实现方式是十分简陋的。如果真正要使用SVM进行严格地建模，测试集与训练集的划分问题，数据的归一化处理问题等。都是我们需要考虑的元素。但由于本文主要为了介绍与探讨SVM算法，其他细节问题就不一一处理，详细方法可以参考我写的其他文章。</p>



<h2 id="svm的其他实现">SVM的其他实现</h2>

<p>除了Python的sklearn库外，SVM在其它语言及平台也有实现。其中： <br>
最流行的有</p>

<blockquote>
  <p><a href="https://www.csie.ntu.edu.tw/~cjlin/libsvm/">LIBSVM：</a><a href="https://www.csie.ntu.edu.tw/~cjlin/libsvm/">https://www.csie.ntu.edu.tw/~cjlin/libsvm/</a> <br>
  这是经典的svm库支持多种语言调用</p>
</blockquote>

<p>再者，在R语言中也有SVM算法的实现：</p>

<blockquote>
  <p><a href="https://cran.r-project.org/web/packages/e1071/index.html">e1071：</a> <br>
  <a href="https://cran.r-project.org/web/packages/e1071/index.html">https://cran.r-project.org/web/packages/e1071/index.html</a></p>
</blockquote>
