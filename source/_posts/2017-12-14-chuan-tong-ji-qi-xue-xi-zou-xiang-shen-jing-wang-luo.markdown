---
layout: post
title: "传统机器学习走向神经网络"
date: 2017-12-14 23:43:13 +0800
comments: true
categories: 神经网络与深度学习
---

﻿<h1 id="传统机器学习走向神经网络">传统机器学习走向神经网络</h1>

<h2 id="神经网络的基本结构">神经网络的基本结构</h2>

<p>首先我们先来看一下最基础的神经网络结构： <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fmgm7cftunj20kf0ckaep.jpg" alt="" title=""></p>

<p>由上图的结构可以看出，这个神经网络具有三层，其中输入层不计。而中间的橙色层则为两层隐藏层，最右的蓝色层为输出层。输入从最左边的输入层进行输入，然后经过两次隐藏层和激活函数之后进行输出，这样我们可以把这个神经网络简单地表示成一下的式子： <br>
<script type="math/tex; mode=display" id="MathJax-Element-82">Y_{out} = W_iX_{in}+B</script> <br>
W为X的权重，而B为函数的偏置。 <br>
其中，偏置值B的存在有利于打破数据对称的局面，使得神经网络可以应用在非对称的数据之上。</p>



<h2 id="神经网络的基本算法">神经网络的基本算法</h2>

<p>前向传导：前向传导的思想比较简单，下面的一张图足以概括它的主要思想。 <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fmgp39x2ltj20oh0gydi9.jpg" alt="" title=""></p>

<p>反向传播：反向传播的方法其实也比较简单，其主要思想是涉及求偏导，以及链式求导法则。 <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fmgp6qj2wlj20mj0i441b.jpg" alt="" title=""></p>

<p>梯度下降：梯度下降法是一个最优化算法，通常也称为最速下降法。最速下降法是求解无约束优化问题最简单和最古老的方法之一，虽然现已不具有实用性，但是许多有效算法都是以它为基础进行改进和修正而得到的。最速下降法是用负梯度方向为搜索方向的，最速下降法越接近目标值，步长越小，前进越慢。</p>

<h2 id="朴素贝叶斯和神经网络">朴素贝叶斯和神经网络</h2>

<p>首先朴素贝叶斯算法的原始形式可以表达成以下的形式： <br>
<script type="math/tex" id="MathJax-Element-1467">G(x)=arg\  max\ p(y)  
\prod\limits^n_{i=1}p(x_i|y)^{x_i}</script></p>

<p>除此之外，该算法还有一下特点： <br>
<script type="math/tex" id="MathJax-Element-1468">x_i只有0，1两种取值</script> <br>
<script type="math/tex" id="MathJax-Element-1469">x_i取1意味着x_i对应了的特征“出现了”</script>  <br>
<script type="math/tex" id="MathJax-Element-1470">x_i取0意味着x_i对应了的特征“没出现”</script></p>

<p>这样转换成矩阵的形式时，我们可以采用独热编码亦称One-hot Encode。 <br>
独热编码：</p>

<p>解决了分类标签的问题，那么我们又该怎样用神经网络的线性模型形式来表达贝叶斯公式中概率相乘的情况呢？</p>

<p>没错，就是使用对数函数。根据对数函数的性质<script type="math/tex" id="MathJax-Element-1471">log_2X+log_2Y=log_2XY</script>,我们就可以通过对数变换，将乘法转换成加法的形式，我们可以把上面的朴素贝叶斯公式改写成： <br>
<script type="math/tex" id="MathJax-Element-1472">G(x)=arg\ max\ log(y)+\sum\limits^n_{i=1}x_ilog\ p(x_i|y)</script></p>

<p>那么我们就可以用退化成线性模型的神经网络来实现朴素贝叶斯模型。</p>



<h3 id="核心实现">核心实现</h3>


```python
# 独热化处理部分
from sklearn.preprocessing import OneHotEncoder
enc = OneHotEncoder()
x_train = enc.fit_transform(x_train).toarray()
x_test = enc.transform(x_test).toarray()

## .....篇幅有限,此处省略其余代码

# NaiveBayes -> NN 权值转换部分
class NB2NN(TransformationBase):
    def __init__(self, *args, **kwargs):
        super(NB2NN, self).__init__(*args, **kwargs)
        self._name_appendix = "NaiveBayes"
        self.model_param_settings.setdefault("activations", None)

    def _transform(self):
        self.hidden_units = []
        x, y, x_test, y_test = self._get_all_data()
        nb = MultinomialNB()
        nb.fit(x, y)
        self._print_model_performance(nb, "Naive Bayes", x, y, x_test, y_test)
        self._transform_ws = [nb.feature_log_prob_.T]
        self._transform_bs = [nb.class_log_prior_]
```

<h2 id="决策树贝叶斯和神经网络">决策树贝叶斯和神经网络</h2>

<p>首先，决策树的原理主要就是通过数据信息熵的变化来选择当前的最优分类点，然后从根开始一步一步扩展成树。而实质上，最后成功构建出来的决策树，其从根节点开始到每个分类叶子节点的路径对应的都是一组高维空间上的超平面组合。决策树的分类也就是用一组超平面去划分数据空间，使得最后剩下一个唯一确定的标识。</p>

<p>知道决策树的本质之后，我们就可以用这样的方法来将决策树算法迁移到神经网络上： <br>
* 第一个隐藏层表达决策树的中间节点所对应的超平面 <br>
* 第二个隐藏层表达各个决策的路径 <br>
* 第二个隐藏层和输出层之间的权值矩阵表达各个叶节点</p>



<h3 id="核心实现-1">核心实现</h3>

```python
## 因为决策树到神经网络的转换较为复杂,此处仅贴出核心代码
class DT2NN(TransformationBase):
    def __init__(self, *args, **kwargs):
        super(DT2NN, self).__init__(*args, **kwargs)
        self._name_appendix = "DTree"
        self.model_param_settings.setdefault("activations", ["sign", "one_hot"])

    def _transform(self):
        x, y, x_test, y_test = self._get_all_data()
        tree = DecisionTreeClassifier()
        tree.fit(x, y)
        self._print_model_performance(tree, "Decision Tree", x, y, x_test, y_test)

        tree_structure = export_structure(tree)
        n_leafs = sum([1 if pair[1] == -1 else 0 for pair in tree_structure])
        n_internals = n_leafs - 1

        print("Internals : {} ; Leafs : {}".format(n_internals, n_leafs))

        b = np.zeros(n_internals, dtype=np.float32)
        w1 = np.zeros([x.shape[1], n_internals], dtype=np.float32)
        w2 = np.zeros([n_internals, n_leafs], dtype=np.float32)
        w3 = np.zeros([n_leafs, self.n_class], dtype=np.float32)
        node_list = []
        node_sign_list = []
        node_id_cursor = leaf_id_cursor = 0
        max_route_length = 0
        self.hidden_units = [n_internals, n_leafs]

        for depth, feat_dim, rs in tree_structure:
            if feat_dim != -1:
                if depth == len(node_list):
                    node_sign_list.append(-1)
                    node_list.append([node_id_cursor, feat_dim, rs])
                    w1[feat_dim, node_id_cursor] = 1
                    b[node_id_cursor] = -rs
                    node_id_cursor += 1
                else:
                    node_list = node_list[:depth + 1]
                    node_sign_list = node_sign_list[:depth] + [1]
            else:
                valid_nodes = set()
                local_sign_list = node_sign_list[:]
                for i, ((node_id, node_dim, node_threshold), node_sign) in enumerate(
                    zip(node_list, node_sign_list)
                ):
                    valid_nodes.add((node_id, node_sign))
                    if i >= 1:
                        for j, ((local_id, local_dim, local_threshold), local_sign) in enumerate(zip(
                            node_list[:i], local_sign_list[:i]
                        )):
                            if node_sign == local_sign and node_dim == local_dim:
                                if (
                                    (node_sign == -1 and node_threshold < local_threshold) or
                                    (node_sign == 1 and node_threshold > local_threshold)
                                ):
                                    local_sign_list[j] = 0
                                    valid_nodes.remove((local_id, local_sign))
                                    break
                for node_id, node_sign in valid_nodes:
                    w2[node_id, leaf_id_cursor] = node_sign / len(valid_nodes)
                max_route_length = max(max_route_length, len(valid_nodes))
                w3[leaf_id_cursor] = rs / np.sum(rs)
                leaf_id_cursor += 1

        w2 *= max_route_length
        self._transform_ws = [w1, w2, w3]
        self._transform_bs = [b]

#................ 篇幅有限,省略其余代码

# DTree -> NN
def export_structure(tree):
    tree = tree.tree_

    def recurse(node, depth):
        feature_dim = tree.feature[node]
        if feature_dim == _tree.TREE_UNDEFINED:
            yield depth, -1, tree.value[node]
        else:
            threshold = tree.threshold[node]
            yield depth, feature_dim, threshold
            yield from recurse(tree.children_left[node], depth + 1)
            yield depth, feature_dim, threshold
            yield from recurse(tree.children_right[node], depth + 1)

    return list(recurse(0, 0))

```

<h2 id="模型改进">模型改进</h2>



<h3 id="对于朴素贝叶斯">对于朴素贝叶斯</h3>

<p>根据上述的原理和理论，我们可以将朴素贝叶斯和决策树转换成神经网络模型，但是转换之后是否存在意义呢？</p>

<p><strong>首先</strong>本身可以通过简单log对数转换成线性模型的朴素贝叶斯算法来说，其转换的步骤并不复杂，但却能够对朴素贝叶斯的独立假设进行一定的微调修正。</p>

<h3 id="对于决策树">对于决策树</h3>

<p>那么对于决策树来说，神经网络的介入可以对决策树的硬边界作一定的修正和“软化”作用。</p>
