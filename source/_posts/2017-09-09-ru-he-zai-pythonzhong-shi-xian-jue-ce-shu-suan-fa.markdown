---
layout: post
title: "如何在Python中实现决策树算法"
date: 2017-09-09 15:33:50 +0800
comments: true
categories: 机器学习
---
<h1 id="如何在python中实现决策树算法">如何在Python中实现决策树算法</h1>

<p>决策树算法是一种简单的预测算法，但正是因为它模型的简单性，常作为一些高级的组合算法的基础，例如<strong>bagging，random forests ，gradient boosting</strong> 等等。再者，由于决策树的最终模型和预估行为具有较强的业务相关与可解析性，使得它十分受从业者与领域专家的欢迎，在各行各业中也有十分多的应用。</p>



<h4 id="决策树的优缺点">决策树的优缺点</h4>

<p>优点：计算复杂度不高，输出结果易于理解，对中间值的缺失不敏感，可以处理不相关特征数据</p>

<p>缺点：可能会产生过拟合问题</p>



<h2 id="决策树的构建">决策树的构建</h2>

<p>在构建决策树之前，我们首先要明确的第一个问题就是：在当前的数据集上哪个特征在划分数据分类时起决定性作用。而其中划分数据集的最大原则就是：<strong><em>将无序的数据变得更加有序。</em></strong></p>

<p>在选择如何去划分数据集的时候，我们可以采用各种不同的方法。但是每种方法都有各自的优缺点。组织杂乱无章的数据的一种方法就是使用信息论度量信息。</p>

<p>在划分数据集之前之后信息发生的变化称为<strong>信息增益</strong>，而获得信息增益最高的特征就是我们用于划分数据的最好选择。</p>

<p>在评测哪种数据划分方式是最好的数据划分之前，我们必须学习如何计算信息增益，而为了能够计算和理解信息增益，我们又必须先了解一个概念，即：集合信息的度量方式，其被称为<strong>香农熵</strong>或简称<strong>熵</strong>。这个名字来源于信息论之父<a href="https://baike.baidu.com/item/%E5%85%8B%E5%8A%B3%E5%BE%B7%C2%B7%E8%89%BE%E5%B0%94%E4%BC%8D%E5%BE%B7%C2%B7%E9%A6%99%E5%86%9C/10588593?fr=aladdin&amp;fromid=1146248&amp;fromtitle=%E9%A6%99%E5%86%9C">克劳德×香农</a></p>

<p>熵：被定义为信息的<strong>期望值</strong>，如果一个事件发生的概率是<script type="math/tex" id="MathJax-Element-1">p(x)</script>,则其信息熵为:</p>

<blockquote>
  <p><script type="math/tex" id="MathJax-Element-2">H(X)=-\sum _{i=i}^n p(x_i) \log  \left (p (x_i)\right)</script></p>
  
  <p>Eg:<img src="http://www.saedsayad.com/images/Entropy_3.png" alt="enter image description here" title=""></p>
</blockquote>

<p>其中n是分类的数目。</p>

<p>可以这样验证一下：如果这件事发生的概率是1，其信息熵<script type="math/tex" id="MathJax-Element-3">H</script>则等于0，因为你知道他一定会发生，丝毫不会觉得惊喜;但是如果这件事的概率趋向于无穷小，比如国足夺得世界杯冠军，那么他的信息熵就会趋向于无穷大。就像你心中听完上一条信息之后，心中可能就有数十万只草泥马奔过一样。</p>



<h4 id="使用python计算信息的熵">使用Python计算信息的熵</h4>

<p>下面我们将以《机器学习实战》一书中给出的例子为基础进一步探讨本文所讲内容。</p>

<blockquote>
  <p><strong>海洋生物数据表</strong></p>
  
  <table>
<thead>
<tr>
  <th>index</th>
  <th>不浮出水面是否可以生存</th>
  <th>是否有脚蹼</th>
  <th>是否属于鱼类</th>
</tr>
</thead>
<tbody><tr>
  <td>0</td>
  <td>是</td>
  <td>是</td>
  <td>是</td>
</tr>
<tr>
  <td>1</td>
  <td>是</td>
  <td>是</td>
  <td>是</td>
</tr>
<tr>
  <td>2</td>
  <td>是</td>
  <td>否</td>
  <td>否</td>
</tr>
<tr>
  <td>3</td>
  <td>否</td>
  <td>是</td>
  <td>否</td>
</tr>
<tr>
  <td>4</td>
  <td>否</td>
  <td>是</td>
  <td>否</td>
</tr>
</tbody></table>

</blockquote>



```python
#coding:utf-8
# 代码功能：计算香农熵，本代码来源于书籍《机器学习实战》
from math import log #我们要用到对数函数，所以我们需要引入math模块中定义好的log函数（对数函数）

def calcShannonEnt(dataSet):#传入数据集
# 在这里dataSet是一个链表形式的的数据集
    countDataSet = len(dataSet) # 我们计算出这个数据集中的数据个数，在这里我们的值是5个数据集
    labelCounts={} # 构建字典，用键值对的关系我们表示出 我们数据集中的类别还有对应的关系
    for featVec in dataSet: #通过for循环，我们每次取出一个数据集，如featVec=[1,1,'yes']
        currentLabel=featVec[-1] # 取出最后一列 也就是类别的那一类，比如说‘yes’或者是‘no’
        if currentLabel not in labelCounts.keys():
            labelCounts[currentLabel] = 0
        labelCounts[currentLabel] += 1

    shannonEnt = 0.0 # 计算香农熵， 根据公式

    for key in labelCounts:
        prob = float(labelCounts[key])/countDataSet
        shannonEnt -= prob * log(prob,2)


    return shannonEnt
def createDataSet():
    dataSet = [[1,1,'yes'],
              [1,1,'yes'],
              [1,0,'no'],
              [0,1,'no'],
              [0,1,'no']]


    labels = ['no surfacing','flippers']

    return dataSet, labels

myDat,labels = createDataSet()
print(myDat)
print(labels)
calcShannonEnt(myDat)
```

<blockquote>
  <p>输出结果: <br>
  [[1, 1, ‘yes’], [1, 1, ‘yes’], [1, 0, ‘no’], [0, 1, ‘no’], [0, 1, ‘no’]] <br>
  [‘no surfacing’, ‘flippers’] <br>
  0.9709505944546686</p>
</blockquote>

<p>其中，香农熵越高，则代表混合的数据越多，这点我们可以通过在数据集中添加更多的分类来验证。</p>



```python
myDat[0][-1]='maybe'
print(myDat)
calcShannonEnt(myDat)
```

<blockquote>
  <p>输出结果: <br>
  [[1, 1, ‘maybe’], [1, 1, ‘yes’], [1, 0, ‘no’], [0, 1, ‘no’], [0, 1, ‘no’]] <br>
  1.3709505944546687</p>
</blockquote>



<h4 id="划分数据集">划分数据集</h4>

<p>分类算法除了需要测量信息熵之外，还需要划分数据集，度量划分数据集的熵，以便判断当前是否正确地划分了数据集。</p>

<p>那么，我们如何确定目前划分数据的方法是否就是最优的划分方法呢？答案就是：<strong>信息增益</strong>。我们仅需选择使得划分后数据集信息增益最大的特征作为分类的最佳特征即可。</p>

<p>首先假设我们选取了第一个特征<strong>“是否需要浮出水面生存”</strong>的第一种可能<strong>“是”</strong>来划分数据集，我们得到划分之后的数据集就是：</p>

<blockquote>
  <p>[[1, 1, ‘maybe’], [1, 1, ‘yes’], [1, 0, ‘no’]</p>
</blockquote>

<p><strong>“否”</strong>的话，得到的划分集则是：</p>

<blockquote>
  <p>[[0, 1, ‘no’], [0, 1, ‘no’]]</p>
</blockquote>

<p>其他特征的划分方式也以此类推。</p>

<p>知道如何划分数据集之后，让我们来构建我们下一步的数据划分代码。</p>



```python
# 代码功能：划分数据集
def splitDataSet(dataSet,axis,value): #传入三个参数第一个参数是我们的数据集，是一个链表形式的数据集；第二个参数是我们的要依据某个特征来划分数据集
    retDataSet = [] #由于参数的链表dataSet我们拿到的是它的地址，也就是引用，直接在链表上操作会改变它的数值，所以我们新建一格链表来做操作

    for featVec in dataSet:
        if featVec[axis] == value: #如果某个特征和我们指定的特征值相等
        #除去这个特征然后创建一个子特征
            reduceFeatVec = featVec[:axis]
            reduceFeatVec.extend(featVec[axis+1:])
            #将满足条件的样本并且经过切割后的样本都加入到我们新建立的样本中
            retDataSet.append(reduceFeatVec)

    return retDataSet
```

<p>下面来测试下我们的代码</p>



```python
splitDataSet(myDat,0,1)
```

<blockquote>
  <p>[Out] [[1, ‘maybe’], [1, ‘yes’], [0, ‘no’]]</p>
</blockquote>



```python
splitDataSet(myDat,0,0)
```

<blockquote>
  <p>[Out]  [[1, ‘no’], [1, ‘no’]]</p>
</blockquote>

<p>知道怎么划分数据集之后，我们接下来要做的就是遍历整个数据集的特征值，然后循环计算熵，找出最好的分类特征了。</p>



<h4 id="选择最好的数据划分方式">选择最好的数据划分方式</h4>



```python
def chooseBestFeatureToSplit(dataSet):
    numFeatures = len(dataSet[0])-1
    baseEntropy = calcShannonEnt(dataSet)
    bestInfoGain =0.0
    bestFeature = -1

    for i in range(numFeatures):
        featList = [sample[i] for sample in dataSet]
        uniqueVals = set(featList)
        newEntropy = 0.0
        for value in uniqueVals:
            subDataSet = splitDataSet(dataSet,i,value)
            prob = len(subDataSet)/float(len(dataSet))
            newEntropy += prob * calcShannonEnt(subDataSet)

        infoGain = baseEntropy - newEntropy

        if(infoGain > bestInfoGain):
            bestInfoGain = infoGain
            bestFeature = i

    return bestFeature
```

<p>这里的choseBestFeatureToSplit()函数的使用是需要满足一定条件的： <br>
1.  数据必须是一种有列表元素组成的列表，而且所有的列表元素都要具有相同的数据长度。 <br>
2. 数据的最后一列或者每个实例的最后一个元素是当前实例的类别标签 </p>

<p>接下来，我们来运行choseBestFeatureToSplit()函数来计算出最佳分类特征。</p>



```python
chooseBestFeatureToSplit(myDat)
```

<blockquote>
  <p>[Out] 0</p>
</blockquote>

<p>从这里我们可以得到第0个特征是最好用于划分数据集的特征。确实，这个分类特征的选择就我们目测而言也是最好的选择。</p>

<p>知道如何选取特征之后，下一步我们就要如何将上面的函数有机结合在一起构建出决策树。</p>



<h4 id="处理特殊情况">处理特殊情况</h4>

<p>工作原理： <br>
1.  得到原始数据集 <br>
2. 基于最好的属性值划分数据集 <br>
3.  由于特征值不止一个，将划分后的数据传递到树分支的下一个节点，再重复2操作 <br>
4. 程序遍历完所有划分数据集属性或每个分支下所有实例都具有相同分类后结束</p>



```python
def majorityCnt(classList): # 传入的参数是已经划分完所有特征之后剩余的数据集，
    #例如[['yes'],['yes'],['maybe']]
    classCount={} #创建一个字典
    for vote in classList:  
        if vote not in classCount.keys():
            classCount[vote] = 0
        classCount[vote] += 1
        # 根据上述的语句，以及我们的例子，我们最终可以得到的结果如下： {'yes':2,'maybe':1}
        sortedClassCount = sorted(classCount.items(), key=operator.itemgetter(1),reverse=True)#这个语句比较复杂，我们在下面详细讲解一下。
# 使用字典iteritems
    return sortedClassCount[0][0]
```

<p>这里的majorityCnt()函数是为了处理<strong>特殊分类</strong>而存在的。我们知道如果根据特征来划分属性，每划分一次就会消耗一个特征，如果我们使用完了所有的特征但是类别还没有划分完那我们就可以采用多数表决的方法来确定叶子节点了。</p>

<p>在上面的代码中，我们发现最后我们用排序函数对剩余的类作了降序处理，并只返回了分类个数最多的元素的那个类。这是一种折中的方法，因为对于一些已经使用完特征的数据集，我们不可能清楚地将一些类分离出来，我们就只能统计其中数量最多的那个分类，以次划分。</p>



<h4 id="递归构建决策树">递归构建决策树</h4>



```python

def createTree(dataSet,labels):
    classList = [example[-1] for example in dataSet]

    if classList.count(classList[0]) == len (classList):
        return classList[0]

    if len(dataSet[0]) == 1:
        return majorityCnt(classList)

    bestFeat = chooseBestFeatureToSplit(dataSet)
    bestFeatLabel = labels[bestFeat]

    myTree = {bestFeatLabel:{}}

    del(labels[bestFeatL])

    featValues = [example[bestFeat] for example in dataSet]

    uniqueVals = set(featValues)

    for value in uniqueVals:
        subLabels = labels[:]
        myTree[bestFeatLabel][value] = createTree(splitDataSet(dataSet,bestFeat,value),subLabels)


    return myTree
```

<p>最后，我们来运行我们的代码来构建出一棵决策树看看。</p>



```python
print(createTree(myDat,labels))
```

<blockquote>
  <p>输出结果 <br>
  [‘no surfacing’, ‘flippers’] <br>
  0 <br>
  no surfacing <br>
  [‘flippers’] <br>
  0 <br>
  flippers <br>
  {‘no surfacing’: {0: ‘no’, 1: {‘flippers’: {0: ‘no’, 1: ‘yes’}}}}</p>
</blockquote>

<p>根据返回的结果，我们可以画出以下的决策树。 <br>
<img src="https://i.loli.net/2017/09/09/59b397d98b6e3.png" alt="dt.png" title=""></p>
