---
layout: post
title: "Python 实现推荐系统"
date: 2017-09-24 10:51:37 +0800
comments: true
categories: 机器学习
---

<h1 id="python-实现推荐系统">Python 实现推荐系统</h1>



<h2 id="引言">引言</h2>

<p>最早的推荐系统应该是亚马逊为了提升长尾货物的用户抵达率而发明的。已经有数据证明，长尾商品的销售额以及利润总和与热门商品是基本持平的。亚马逊网站上在线销售的商品何止百万，但首页能够展示的商品数量又极其有限，给用户推荐他们可能喜欢的商品就成了一件非常重要的事情。当然，商品搜索也是一块大蛋糕，亚马逊的商品搜索早已经开始侵蚀谷歌的核心业务了。</p>

<p>从这些例子之中，我们可以看到我们能够使用许多不同的方式来搜集兴趣偏好。有时候，这些数据可能来自人们购买的商品，以及这些商品关联的评价信息。我们可以利用一组算法从中挖掘，建立几个有意思的推荐系统。</p>



<h2 id="推荐系统的简介">推荐系统的简介</h2>

<p>两种最普遍的推荐系统的类型是基于内容的推荐系统和协同过滤推荐系统（CF）。协同过滤基于用户对产品的态度产生推荐，基于内容的推荐系统基于物品属性的相似性进行推荐。CF可以分为基于内存的协同过滤和基于模型的协同过滤。</p>



<h3 id="基于内容推荐">基于内容推荐</h3>

<p><strong>基于内容的推荐（Content-based Recommendation）</strong>，它是建立在项目的内容信息上作出推荐的，而不需要依据用户对项目的评价意见，更多地需要用机器学习的方法从关于内容的特征描述的事例中得到用户的兴趣资料。在基于内容的推荐系统中，项目或对象是通过相关的特征的属性来定义，系统基于用户评价对象的特征，学习用户的兴趣，考察用户资料与待预测项目的相匹配程度。用户的资料模型取决于所用学习方法，常用的有决策树、神经网络和基于向量的表示方法等。</p>



<h4 id="基于内容推荐方法的优点是">基于内容推荐方法的优点是：</h4>

<ul>
<li>不需要其它用户的数据，没有冷开始问题和稀疏问题。</li>
<li>能为具有特殊兴趣爱好的用户进行推荐。</li>
<li>能推荐新的或不是很流行的项目，没有新项目问题。</li>
<li>通过列出推荐项目的内容特征，可以解释为什么推荐那些项目。</li>
<li>已有比较好的技术，如关于分类学习方面的技术已相当成熟。</li>
</ul>



<h4 id="缺点是">缺点是:</h4>

<p>要求内容能容易抽取成有意义的特征，要求特征内容有良好的结构性，并且用户的口味必须能够用内容特征形式来表达，不能显式地得到其它用户的判断情况。</p>



<h3 id="协同过滤推荐">协同过滤推荐</h3>

<p><strong>协同过滤推荐（Collaborative Filtering Recommendation）</strong>，是推荐系统中应用最早和最为成功的技术之一。它一般采用最近邻技术，利用用户的历史喜好信息计算用户之间的距离，然后利用目标用户的最近邻居用户对商品评价的加权评价值来预测目标用户对特定商品的喜好程度，系统从而根据这一喜好程度来对目标用户进行推荐。协同过滤最大优点是对推荐对象没有特殊的要求，能处理非结构化的复杂对象，如音乐、电影。</p>

<p>协同过滤是基于这样的假设：为一用户找到他真正感兴趣的内容的好方法是首先找到与此用户有相似兴趣的其他用户，然后将他们感兴趣的内容推荐给此用 户。其基本思想非常易于理解，在日常生活中，我们往往会利用好朋友的推荐来进行一些选择。协同过滤正是把这一思想运用到电子商务推荐系统中来，基于其他用 户对某一内容的评价来向目标用户进行推荐。</p>

<p>基于协同过滤的推荐系统可以说是从用户的角度来进行相应推荐的，而且是自动的即用户获得的推荐是系统从购买模式或浏览行为等隐式获得的，不需要用户努力地找到适合自己兴趣的推荐信息，如填写一些调查表格等。</p>

<p>和基于内容的过滤方法相比，</p>



<h4 id="协同过滤具有如下的优点">协同过滤具有如下的优点：</h4>

<ul>
<li>能够过滤难以进行机器自动内容分析的信息，如艺术品，音乐等。</li>
<li>共享其他人的经验，避免了内容分析的不完全和不精确，并且能够基于一些复杂的，难以表述的概念（如信息质量、个人品味）进行过滤。</li>
<li>有推荐新信息的能力。可以发现内容上完全不相似的信息，用户对推荐信息的内容事先是预料不到的。这也是协同过滤和基于内容的过滤一个较大的差别，基于内容的过滤推荐很多都是用户本来就熟悉的内容，而协同过滤可以发现用户潜在的但自己尚未发现的兴趣偏好。</li>
<li>能够有效的使用其他相似用户的反馈信息，较少用户的反馈量，加快个性化学习的速度。</li>
</ul>



<h4 id="缺点是-1">缺点是：</h4>

<p>最典型的问题有， <br>
稀疏问题（Sparsity） <br>
可扩展问题（Scalability） <br>
冷启动问题</p>



<h2 id="实例应用">实例应用</h2>

<p>我们下面通过使用MovieLens数据集（这是一个有关电影评分的经典数据集， 其中Movielens-100k和movielens-1M有用户对电影的打分，电影的title、genre、IMDB链接、用户的gender、age、occupation、zip code。movielens-10M中还有用户对电影使用的tag信息。）</p>

<p>数据集的<a href="http://files.grouplens.org/datasets/movielens/ml-100k.zip">下载地址</a></p>



<h3 id="数据读取">数据读取</h3>


```python
import numpy as np
import pandas as pd
header = ['user_id','item_id','rating','timestamp']
df = pd.read_csv('/home/ef/Desktop/ml-100k/u.data',
                sep='\t',names=header)
print(df.head())
n_users = df.user_id.unique().shape[0]
n_items = df.item_id.unique().shape[0]
print('Number of users = ' + str(n_users) + ' | Number of movies = ' + str(n_items))
```


<blockquote>
  <p>输出结果： <br>
     user_id  item_id  rating  timestamp <br>
  0      196      242       3  881250949 <br>
  1      186      302       3  891717742 <br>
  2       22      377       1  878887116 <br>
  3      244       51       2  880606923 <br>
  4      166      346       1  886397596 <br>
  Number of users = 943 | Number of movies = 1682</p>
</blockquote>



<h3 id="数据划分">数据划分</h3>

<p>使用scikit-learn库来划分数据集</p>


```python
from sklearn import cross_validation as cv
X_train,x_test = cv.train_test_split(df,test_size=0.2)
```


<h3 id="基于内存的协同过滤">基于内存的协同过滤</h3>

<p>基于内存的协同过滤方法可以分为两个部分：</p>

<ul>
<li>用户－产品协同过滤，即：基于用户的协同过滤推荐</li>
<li>产品－产品协同过滤，即：基于物品的协同过滤推荐</li>
</ul>

<p>基于用户的协同过滤推荐，将选取一个特定的用户，基于打分的相似性发现类似于该用户的用户，并推荐那些相似用户喜欢的产品。基于物品的协同过滤推荐会选取一个产品，发现喜欢该产品的用户，并找到这些相似用户还喜欢的其它产品。</p>

<p>基于用户的协同过滤推荐：“喜欢这东西的人也喜欢……” <br>
基于物品的协同过滤推荐：“像你一样的人也喜欢（买个该商品的还买了）……”</p>

<p>在这两种情况下，从整个数据集构建一个用户产品矩阵。</p>



<h4 id="用户产品矩阵的例子">用户产品矩阵的例子：</h4>

<p>计算相似性，并创建一个相似性矩阵。 <br>
通常用于推荐系统中的距离矩阵是余弦相似性，其中，打分被看成n维空间中的向量，而相似性是基于这些向量之间的角度进行计算的。用户a和b的余弦相似性可以用下面的公式进行计算：</p>

<p><script type="math/tex" id="MathJax-Element-1">Similar_u^{cos}(U_a,U_b)=\frac{U_a*U_b}{||U_a|| * ||U_b||} = \frac {\sum x_{a,m}x_{b,m}}{\sqrt {\sum x^2_{a,m}\sum x^2_{b,m} }}</script></p>

<p>同时，物品a和b的相似读也可以写成以上形式。</p>

<p>创建用户产品矩阵，针对测试数据和训练数据，创建两个矩阵：</p>

```python
from sklearn import cross_validation as cv
X_train,x_test = cv.train_test_split(df,test_size=0.2)

train_data_matrix = np.zeros((n_users,n_items))
for line in X_train.itertuples():
    train_data_matrix[line[1]-1, line[2]-1] = line[3]
test_data_matrix = np.zeros((n_users, n_items))
for line in x_test.itertuples():
    test_data_matrix[line[1]-1, line[2]-1] = line[3]

## 通过余弦相似度计算用户和物品的相似度
from sklearn.metrics.pairwise import pairwise_distances
user_similarity = pairwise_distances(train_data_matrix, metric = "cosine")
item_similarity = pairwise_distances(train_data_matrix.T, metric = "cosine")
```


<h4 id="评估">评估</h4>

<p>这里采用均方根误差(RMSE)来度量预测评分的准确性,可以使用sklearn的mean_square_error(MSE)函数，其中RMSE仅仅是MSE的平方根。</p>

```python
from sklearn.metrics import mean_squared_error
from math import sqrt
def rmse(prediction, ground_truth):
    prediction = prediction[ground_truth.nonzero()].flatten()
    ground_truth = ground_truth[ground_truth.nonzero()].flatten()
    return sqrt(mean_squared_error(prediction, ground_truth))    

print ('User based CF RMSE: ' + str(rmse(user_prediction, test_data_matrix)))
print ('Item based CF RMSe: ' + str(rmse(item_prediction, test_data_matrix)))
print ('User based CF RMSE: ' + str(rmse(user_prediction, test_data_matrix)))
print ('Item based CF RMSe: ' + str(rmse(item_prediction, test_data_matrix)))

```

<blockquote>
  <p>输出结果： <br>
  User based CF RMSE: 3.087357412872858 <br>
  Item based CF RMSe: 3.437038163412728</p>
</blockquote>

<p>但从结果来看，算法的推荐效果还算理想。</p>



<h3 id="基于模型的协同过滤">基于模型的协同过滤</h3>

<p>基于模型的协同过滤是基于<strong>矩阵分解(MF)的</strong>，矩阵分解广泛应用于推荐系统中，它比基于内存的CF有更好的扩展性和稀疏性。MF的目标是从已知的评分中学习用户的潜在喜好和产品的潜在属性，随后通过用户和产品的潜在特征的点积来预测未知的评分。 <br>
这是一个典型的机器学习的问题，可以将已有的用户喜好信息作为训练样本，训练出一个预测用户喜好的模型，这样以后用户在进入系统，可以基于此模型计算推荐。这种方法的问题在于如何将用户实时或者近期的喜好信息反馈给训练好的模型，从而提高推荐的准确度。</p>



<h4 id="svd">SVD</h4>

<p>SVD即：<strong>奇异值分解（Singular value decomposition）</strong>奇异值分解是线性代数中一种重要的矩阵分解，在信号处理、统计学等领域有重要应用。奇异值分解在某些方面与对称矩阵或Hermite矩阵基于特征向量的对角化类似。然而这两种矩阵分解尽管有其相关性，但还是有明显的不同。对称阵特征向量分解的基础是谱分析，而奇异值分解则是谱分析理论在任意矩阵上的推广。</p>

<p>在矩阵M的奇异值分解中</p>

<p><script type="math/tex" id="MathJax-Element-2">M=UΣV^T</script> </p>

<ul>
<li><strong>U的列(columns)</strong>组成一套对M的正交”输入”或”分析”的基向量。这些向量是MM*的特征向量。</li>
<li><strong>V的列(columns)</strong>组成一套对M的正交”输出”的基向量。这些向量是M*M的特征向量。</li>
<li><strong>Σ对角线上的元素</strong>是奇异值，可视为是在输入与输出间进行的标量的”膨胀控制”。这些是M*M及MM*的奇异值，并与U和V的列向量相对应。</li>
</ul>

<p>那么矩阵M就可以分解成U，Σ，V。 <br>
U矩阵表示对应于隐藏特性空间中的用户的特性矩阵，而V矩阵表示对应于隐藏特性空间中的产品的特性矩阵。</p>

<p>现在，我们可以通过U，Σ和<script type="math/tex" id="MathJax-Element-3">V^T</script>的点积进行预测了。</p>

```python
# 计算矩阵的稀疏度
sparsity = round(1.0 - len(df) / float(n_users*n_items),3)
print('The sparsity level of MovieLen100K is ' + str(sparsity * 100) + '%')

import scipy.sparse as sp
from scipy.sparse.linalg import svds
u, s, vt = svds(train_data_matrix, k = 20)
s_diag_matrix = np.diag(s)
x_pred = np.dot(np.dot(u,s_diag_matrix),vt)
print('User-based CF MSE: ' + str(rmse(x_pred, test_data_matrix)))

```


<blockquote>
  <p>输出结果： <br>
  The sparsity level of MovieLen100K is 93.7% <br>
  User-based CF MSE: 2.63901831155016</p>
</blockquote>

<p>最后，我们可以发现，采用SVD分解矩阵的基于模型的协同过滤方法在推荐的表现上更胜一筹。</p>
