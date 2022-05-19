---
layout: post
title: "贝叶斯景象图"
date: 2018-02-27 22:51:43 +0800
comments: true
categories: 统计学习
---
﻿<h1 id="贝叶斯景象图">贝叶斯景象图</h1>

<h2 id="理论说明">理论说明</h2>



<h3 id="均匀分布">均匀分布</h3>

<p>对于一个含有Ｎ个未知元素的贝叶斯推断问题，我们隐式地为其先验分布创建了一个Ｎ维空间。先验分布上某一点的概率，都投射到某个高维的面或曲线上，其形状由先验分布决定。比如，假定有两个未知元素 <script type="math/tex" id="MathJax-Element-81">p_1、p_2</script>，其先验分布都是（０，５）上的均匀分布，那么先验分布存在于一个边长为５的正方形空间，而其概率面就是正方形上方的一个平面（由于假定了均匀分布，因此每一点概率相同）。</p>

<h3 id="代码绘图演示">代码绘图演示</h3>
```python
%matplotlib inline
import scipy.stats as stats
from IPython.core.pylabtools import figsize
import numpy as np
figsize(12.5, 4)

import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D

jet = plt.cm.jet
fig = plt.figure()
x = y = np.linspace(0, 5, 100)
X, Y = np.meshgrid(x, y)

plt.subplot(121)
uni_x = stats.uniform.pdf(x, loc=0, scale=5)
uni_y = stats.uniform.pdf(y, loc=0, scale=5)
M = np.dot(uni_x[:, None], uni_y[None, :])
im = plt.imshow(M, interpolation='none', origin='lower',
                cmap=jet, vmax=1, vmin=-.15, extent=(0, 5, 0, 5))

plt.xlim(0, 5)
plt.ylim(0, 5)
plt.title("Landscape formed by Uniform priors.")

ax = fig.add_subplot(122, projection='3d')
ax.plot_surface(X, Y, M, cmap=plt.cm.jet, vmax=1, vmin=-.15)
ax.view_init(azim=390)
plt.title("Uniform prior landscape; alternate view");
```


<p><img src="https://ws1.sinaimg.cn/large/a3d23450ly1fovd74ecb8j20lg083tae.jpg" alt="" title=""></p>



<h3 id="指数分布">指数分布</h3>

<p>再者，如果 <script type="math/tex" id="MathJax-Element-192">p_1、p_2</script>的先验分布为Exp(3)和Exp(10)，那么对应的空间便是二维平面上，各维都取正值确定的范围，而对应的概率面的形状就是一个从（0，0）点向正值方向流淌的瀑布。</p>

<p>以下的示例图就描绘了这样的情形，其中颜色越是趋向于暗红的位置，其先验概率就越高。反过来，颜色越是趋向于深蓝的位置，其先验概率就越低。</p>

<h3 id="代码绘图演示">代码绘图演示</h3>

```python
figsize(12.5, 5)
fig = plt.figure()
plt.subplot(121)

exp_x = stats.expon.pdf(x, scale=3)
exp_y = stats.expon.pdf(x, scale=10)
M = np.dot(exp_x[:, None], exp_y[None, :])
CS = plt.contour(X, Y, M)
im = plt.imshow(M, interpolation='none', origin='lower',
                cmap=jet, extent=(0, 5, 0, 5))
#plt.xlabel("prior on $p_1$")
#plt.ylabel("prior on $p_2$")
plt.title("$Exp(3), Exp(10)$ prior landscape")

ax = fig.add_subplot(122, projection='3d')
ax.plot_surface(X, Y, M, cmap=jet)
ax.view_init(azim=390)
plt.title("$Exp(3), Exp(10)$ prior landscape; \nalternate view");
```

<p><img src="https://ws1.sinaimg.cn/large/a3d23450ly1fovdfq6o3cj20mg0a577q.jpg" alt="" title=""></p>

<p>这些二维空间的例子很简单，我们的大脑可以轻易想象得到。但实际中，先验分布所在的空间和其概率面往往具有更高的维度。</p>



<h3 id="观测值对先验分布的影响">观测值对先验分布的影响　</h3>

<p>在实际中，观测样本对空间不会有影响，但它会改变概率面的形状，将其在某些局部区域拉伸或挤压，以表明参数的真实性在哪里。更多的数据意味着对概率面更多的拉伸和挤压，使得最初的概率面形状变得十分奇怪。反之数据越少，那么最初的形状就保留得越好。不管如何，最后得到的概率面描述了后验分布的形状。</p>

<p>假如我们现在想对两个参数为<script type="math/tex" id="MathJax-Element-239">\lambda</script>的泊松分布进行估计。那么我们将要分别比较用均匀分布和指数分布来对<script type="math/tex" id="MathJax-Element-240">\lambda</script>的先验分布进行假设的不同效果。</p>

<h3 id="代码绘图演示-2">代码绘图演示</h3>



```python
# create the observed data

# sample size of data we observe, trying varying this (keep it less than 100 ;)
N = 1

# the true parameters, but of course we do not see these values...
lambda_1_true = 1
lambda_2_true = 3

#...we see the data generated, dependent on the above two values.
data = np.concatenate([
    stats.poisson.rvs(lambda_1_true, size=(N, 1)),
    stats.poisson.rvs(lambda_2_true, size=(N, 1))
], axis=1)
print("observed (2-dimensional,sample size = %d):" % N, data)

# plotting details.
x = y = np.linspace(.01, 5, 100)
likelihood_x = np.array([stats.poisson.pmf(data[:, 0], _x)
                        for _x in x]).prod(axis=1)
likelihood_y = np.array([stats.poisson.pmf(data[:, 1], _y)
                        for _y in y]).prod(axis=1)
L = np.dot(likelihood_x[:, None], likelihood_y[None, :])
observed (2-dimensional,sample size = 1): [[0 2]]
In [4]:
figsize(12.5, 12)
# matplotlib heavy lifting below, beware!
plt.subplot(221)
uni_x = stats.uniform.pdf(x, loc=0, scale=5)
uni_y = stats.uniform.pdf(x, loc=0, scale=5)
M = np.dot(uni_x[:, None], uni_y[None, :])
im = plt.imshow(M, interpolation='none', origin='lower',
                cmap=jet, vmax=1, vmin=-.15, extent=(0, 5, 0, 5))
plt.scatter(lambda_2_true, lambda_1_true, c="k", s=50, edgecolor="none")
plt.xlim(0, 5)
plt.ylim(0, 5)
plt.title("Landscape formed by Uniform priors on $p_1, p_2$.")

plt.subplot(223)
plt.contour(x, y, M * L)
im = plt.imshow(M * L, interpolation='none', origin='lower',
                cmap=jet, extent=(0, 5, 0, 5))
plt.title("Landscape warped by %d data observation;\n Uniform priors on $p_1, p_2$." % N)
plt.scatter(lambda_2_true, lambda_1_true, c="k", s=50, edgecolor="none")
plt.xlim(0, 5)
plt.ylim(0, 5)

plt.subplot(222)
exp_x = stats.expon.pdf(x, loc=0, scale=3)
exp_y = stats.expon.pdf(x, loc=0, scale=10)
M = np.dot(exp_x[:, None], exp_y[None, :])

plt.contour(x, y, M)
im = plt.imshow(M, interpolation='none', origin='lower',
                cmap=jet, extent=(0, 5, 0, 5))
plt.scatter(lambda_2_true, lambda_1_true, c="k", s=50, edgecolor="none")
plt.xlim(0, 5)
plt.ylim(0, 5)
plt.title("Landscape formed by Exponential priors on $p_1, p_2$.")

plt.subplot(224)
# This is the likelihood times prior, that results in the posterior.
plt.contour(x, y, M * L)
im = plt.imshow(M * L, interpolation='none', origin='lower',
                cmap=jet, extent=(0, 5, 0, 5))

plt.scatter(lambda_2_true, lambda_1_true, c="k", s=50, edgecolor="none")
plt.title("Landscape warped by %d data observation;\n Exponential priors on \
$p_1, p_2$." % N)
plt.xlim(0, 5)
plt.ylim(0, 5);
```

<p><img src="https://ws1.sinaimg.cn/large/a3d23450ly1fovdubnzc1j20mo0m10y2.jpg" alt="" title=""></p>

<p>四张图里的黑点代表了参数的真实取值，左下图为均匀先验得到的后验分布图。虽然观测值相同，但是两种假设下的后验分布形状是不一样的。其主要原因是因为观测点的位置在两者的假设的前提先验概率是不一样的。这样，我们可以知道，即便只有一个观测值，形成的山峰也试图要包括参数值的真实位置。当然，在真正的推断中，仅用一个观测值显然也是十分不科学的，这里仅仅为了方便阐述而已。</p>

<p>本文参考自<a href="https://github.com/CamDavidsonPilon/Probabilistic-Programming-and-Bayesian-Methods-for-Hackers/blob/master/Chapter3_MCMC/Ch3_IntroMCMC_PyMC3.ipynb">《Probabilistic-Programming-and-Bayesian-Methods-for-Hackers》</a></p>
