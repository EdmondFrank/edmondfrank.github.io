---
layout: post
title: "深度学习入门简介（二）"
date: 2017-12-08 12:52:08 +0800
comments: true
categories: 神经网络与深度学习
---
﻿<h1 id="深度学习入门简介二">深度学习入门简介（二）</h1>

<h2 id="深度学习的三部曲">深度学习的三部曲</h2>



<h3 id="训练前的准备">训练前的准备</h3>

<p>1）训练数据 <br>
在训练一个深度学习的模型之前，我们首先需要准备的就是训练数据，若是图片的话其中就包括：图片的内容以及他的标签。 <br>
<strong>注：学习的分类目标也是包括在训练数据里面的</strong></p>

<p>2）学习目标 <br>
学习的目标往往就是一个二分类或者多分类问题。而对于最后的效果，我们需要达到当我们输入一个待预测或分类的值时，正确的结果应该对应那个最大概率的输出项。</p>

<p>3）损失函数 <br>
简单来说，深度学习的分类和回归的本质就是，找到一个使得在所有样本项上取得的误差值最小的函数。而预测值与真实值的误差我们可以通过他们之间的距离计算得出。</p>



<h3 id="最小化误差">最小化误差</h3>

<p>为了达到一个分类或预测准确的效果，我们就要找到一个网络中的对应的超参数<script type="math/tex" id="MathJax-Element-471">\theta</script>使得网络的预测与真实值的误差是最小的。其中一个简单而粗暴的方法就是：枚举法。但是这样做的效率显然非常的低效。为了能够更加优化地找到或者说是接近使得网络取得最小误差的超参数<script type="math/tex" id="MathJax-Element-472">\theta</script>我们可以采用<strong>梯度下降法</strong>，其根据预设的学习率不断更新权重的梯度来接近局部最优解。</p>

<p>其具体过程图如下所示： <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fm92vbwnztj20ps0em0ub.jpg" alt="" title=""></p>

<p><strong>梯度下降的缺点：</strong> <br>
由于梯度下降每次计算时都是随机选取一个开始点，再根据学习率来慢慢减小全局误差。这样一来，学习率的设定就十分重要了，过大的学习率容易越过最低点，而过小的学习率又使得误差降低的速度过慢，且过小的学习率也会造成学习过程中陷入局部最低点后无法跳出。但实际上由于精度误差的问题梯度下降永远无法到达真正意义上的全局最低点，即无法取得全局最有解。但在多次的迭代运算后一般可以达到一个可接受的损失误差的局部最优解。</p>

<p>具体图示如下： <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fm937sjcc5j20q40hcdn6.jpg" alt="" title=""></p>

<h3 id="反向传播">反向传播</h3>

<p><strong>反向传播算法</strong>：这是一种高效的计算权值梯度的方式。</p>

<p>有关算法的详细介绍可以参考：</p>

<p><a href="http://speech.ee.ntu.edu.tw/~tlkagk/courses/MLDS_2015_2/Lecture/DNN%20backprop.ecm.mp4/index.html">http://speech.ee.ntu.edu.tw/~tlkagk/courses/MLDS_2015_2/Lecture/DNN%20backprop.ecm.mp4/index.html</a></p>

<p>通常我们在使用流行框架来构建神经网络时，不用亲自考虑如何去计算和处理梯度值，框架的作者在实现框架时已经做好了相关处理。</p>



<h3 id="神经网络的理论">神经网络的理论</h3>

<p>根据 <a href="http://neuralnetworksanddeeplearning.com/chap4.html">A visual proof that neural nets can compute any function</a> 文章的描述任何的连续函数 f 都可以用一个隐藏层内有足够多的神经元的神经网络来近似。</p>

<p><strong>既然这样，为什么今天流行的是深度网络而不是广度网络呢？</strong></p>

<p>根据 <a href="https://www.microsoft.com/en-us/research/publication/conversational-speech-transcription-using-context-dependent-deep-neural-networks/">Seide, Frank, Gang Li, and Dong Yu. “Conversational Speech Transcription <br>
Using Context-Dependent Deep Neural Networks.” Interspeech. 2011. <br>
</a> 论文的研究，广度和深度网络对降低全局误差时的参数如下表所示：</p>

<p><img src="https://ws1.sinaimg.cn/large/a3d23450gy1fm98wlbz8yj20np0g2gmz.jpg" alt="fat-vs-deep" title=""></p>

<p>根据上图的研究结果，我们可以发现使用多层的神经元能够更加容易近似一些函数，这其实就跟我们的电子电路中的逻辑电路类似，即便在电子电路中两层的逻辑门电路就可以实现任意的逻辑操作，但是使用多层的逻辑门电路可以更容易的构建一些逻辑操作。</p>

<h3 id="模块化">模块化</h3>

<p>深度学习中还有一个特点就是<strong>模块化</strong>，在一层层的网络层的堆叠中，每一层都会作为一个模块来学习数据。简单来说，深度学习的过程其实就是一个自动提取特征的过程。对于传统的机器学习而言，数据科学家通过特征工程，提取出数据的特征，再利用特征对数据进行建模以此达到分类预测的效果。深度学习通过各个神经元的加权组合以及反向传播的权值调整，使得整个网络的每一层都渐渐趋向稳定，且其稳定值能够在那个维度上进行部分数据的划分，简单来说就是一个区域性的能够对数据有所区分的特性。那随着各个神经层的共同作用使得深度学习在分类预测应用上效果显著。</p>

<p>最后，深度学习在图像分类的本质大概可以用以下这张图片概括： <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fm99ftf2vwj20ow0dsn0p.jpg" alt="" title=""></p>
