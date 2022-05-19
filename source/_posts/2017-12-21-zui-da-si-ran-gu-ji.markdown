---
layout: post
title: "最大似然估计"
date: 2017-12-21 01:00:04 +0800
comments: true
categories: 统计学习
---
﻿<h1 id="em算法基石-最大似然估计">EM算法基石-最大似然估计</h1>

<h2 id="前言">前言</h2>

<p>在统计计算中，<strong>最大期望（EM）算法是在概率模型中寻找参数最大似然估计或者最大后验估计的算法（机器学习十大算法之一）</strong>，其中概率模型依赖于无法观测的隐藏变量（Latent Variable）。最大期望经常用在机器学习和计算机视觉的数据聚类（Data Clustering）领域。而本文要讲的就是最大期望算法的基石-<strong>最大似然估计</strong>。</p>

<h2 id="最大似然估计maximum-likelihoodml">最大似然估计（Maximum Likelihood，ML）</h2>

<h3 id="概述">概述</h3>

<p><strong>最大似然估计</strong>也称极大似然法，是一种统计方法，它用来求一个样本集的相关概率密度函数的参数。这个方法最早是遗传学家以及统计学家罗纳德·费雪爵士在1912年至1922年间开始使用的。</p>

<p><strong>最大似然法</strong>明确地使用概率模型，其目标是寻找能够以较高概率产生观察数据的系统发生树。最大似然法是一类完全基于统计的系统发生树重建方法的代表。</p>

<h3 id="简单举例">简单举例</h3>

<p>设有外形完全相同的两个箱子,甲箱有99个白球1个黑球,乙箱有1个白球99个黑球.今随机地抽取一箱,然后再从这箱中任取一球,结果发现是白球.问这个箱子是甲箱还是乙箱?</p>

<p>仅仅从取出的球是白球这一点是无法从逻辑上严格加以判定该箱究竟是甲箱还是乙箱的。但是如果现在一定要我们做出选择，那么我们只能这样来考虑：从箱中取出的球是白球这一点来看，甲箱和乙箱哪个看上去更像是真正从中取球的箱子？</p>

<p>我们可以这样来分析，如果该箱是甲箱,则取得白球的概率为0.99；如果该箱是乙箱,则取得白球的概率0.01．因此，用“该箱是甲箱”来解释所取的球是白球这一事件更有说服力一些，从而我们判定甲箱比乙箱更像一些。最后我们做出推断,这球是从甲箱取出的。</p>

<h3 id="离散分布离散有限参数空间">离散分布，离散有限参数空间</h3>

<p>看完上面那个简单的例子，下面再来考虑一个抛硬币的例子。假设这个硬币正面跟反面轻重不同。我们把这个硬币抛80次，并把正面的次数记下来，正面记为H，反面记为T），并把抛出一个正面的概率记为p，抛出一个反面的概率记为1 − p。假设我们抛出了49个正面，31 个反面，即49次H，31次T。假设这个硬币是我们从一个装了三个硬币的盒子里头取出的。这三个硬币抛出正面的概率分别为p = 1 / 3, p = 1 / 2, p = 2 / 3. 这些硬币没有标记，所以我们无法知道哪个是哪个。使用最大似然估计，通过这些试验数据，我们可以计算出哪个硬币的可能性最大。这个可能性函数取以下三个值中的一个：</p>



<p><script type="math/tex; mode=display" id="MathJax-Element-98">P(H=49,T=31|\rho=\frac{1}{3})=\textrm{C}^{49}_{80}(\frac{1}{3})^{49}(1-\frac{1}{3})^{31} \approx 0.000 \\
P(H=49,T=31|\rho=\frac{1}{2})=\textrm{C}^{49}_{80}(\frac{1}{2})^{49}(1-\frac{1}{2})^{31} \approx 0.012 \\
P(H=49,T=31|\rho=\frac{2}{3})=\textrm{C}^{49}_{80}(\frac{2}{3})^{49}(1-\frac{2}{3})^{31} \approx 0.054 </script></p>

<p>我们可以看到当<script type="math/tex" id="MathJax-Element-99">\widehat{p}=2/3</script>时，可能性函数取得最大值。这就是p的最大似然估计。</p>



<h3 id="离散分布连续参数空间升级版">离散分布，连续参数空间（升级版）</h3>

<p>现在假设上面的例子中的盒子中有无数个硬币，对于<script type="math/tex" id="MathJax-Element-439">0\leq p \leq 1</script>中的任何一个p， 都有一个抛出正面概率为p的硬币对应，我们再来求其可能性函数的最大值： <br>
<script type="math/tex; mode=display" id="MathJax-Element-440">f_D(H=49,T=31|\rho)=\textrm{C}^{49}_{80}\rho^{49}(1-\rho^{31})</script> <br>
两边同时取p微分 <br>
<script type="math/tex; mode=display" id="MathJax-Element-441">0=\rho^{48}(1-\rho)[49(1-\rho)-31\rho]</script> <br>
求得其解分别为： <br>
<script type="math/tex; mode=display" id="MathJax-Element-442">\rho=0,\rho=1和\rho=\frac{49}{80}</script> <br>
使可能性最大的解显然是p = 49 / 80（因为p = 0 和p = 1 这两个解会使可能性为零）。因此我们说最大似然估计值为<script type="math/tex" id="MathJax-Element-443">\widehat{p}=49/80</script>.</p>

<p>这个结果很容易一般化。只需要用一个字母t代替49用以表达伯努利试验中的被观察数据（即样本）的成功次数，用另一个字母n代表伯努利试验的次数即可。使用完全同样的方法即可以得到最大似然估计值: <br>
<script type="math/tex; mode=display" id="MathJax-Element-444">\widehat{p}=\frac{t}{n}</script></p>

<h3 id="最大似然估计的一般求解步骤">最大似然估计的一般求解步骤</h3>

<ol>
<li>写出似然函数 <br>
　　<script type="math/tex; mode=display" id="MathJax-Element-519">L\theta=\prod_{i=1}^n p(x_i;\theta)(总体X为离散型时)\\  L\theta=\prod_{i=1}^n f(x_i;\theta)(总体X为连续型时)</script> <br>
　　</li>
<li><p>对似然函数两边取对数有 <br>
<script type="math/tex; mode=display" id="MathJax-Element-520">lnL\theta=\sum_{i=1}^n lnp(x_i;\theta)
\\ lnL\theta=\sum_{i=1}^n lnf(x_i;\theta)</script></p></li>
<li><p>对lnL\theta求导数并令之为0： <br>
<script type="math/tex; mode=display" id="MathJax-Element-521">\frac{dlnL\theta}{d\theta}=0</script></p></li>
</ol>

<p>此方程为对数似然方程。解对数似然方程所得，即为未知参数 的最大似然估计值。</p>



<h4 id="举个栗子连续分布连续参数空间终级版">举个栗子：连续分布，连续参数空间（终级版）</h4>

<p>设总体 <script type="math/tex" id="MathJax-Element-805">X~N(μ，σ^2),μ，σ^2(正太分布)</script>为未知参数，<script type="math/tex" id="MathJax-Element-806">X1,X2...,Xn</script>是来自总体X的样本，<script type="math/tex" id="MathJax-Element-807">X1,X2...,Xn</script>是对应的样本值，求<script type="math/tex" id="MathJax-Element-808">μ与σ^2</script>的最大似然估计值。</p>

<p><strong>解:</strong> X的概率密度为 <br>
<script type="math/tex; mode=display" id="MathJax-Element-809">f(x|μ，σ2)=\frac{1}{\sqrt{2\pi\sigma}}e^-\frac{(x_i-\mu)^2}{2\sigma^2} (-\infty<x<+\infty),</script> <br>
　　 <br>
<strong>可得似然函数</strong>如下： <br>
<script type="math/tex; mode=display" id="MathJax-Element-810">L(μ，σ2)=\prod_{i=1}^n\frac{1}{\sqrt{2\pi\sigma}}e^{-\frac{(x_i-\mu)^2}{2{\sigma}^2}}</script></p>

<p><strong>取对数</strong>，得</p>

<p>　　<script type="math/tex; mode=display" id="MathJax-Element-811">lnL(μ，σ2)=-\frac{n}{2}ln(2\pi)-\frac{n}{2}ln(\sigma^2)-\frac{1}{2{\delta}^2}\sum_{i=1}^n{(x_i-\mu)}^2</script></p>

<p><strong>令</strong></p>



<p><script type="math/tex; mode=display" id="MathJax-Element-821">\begin{cases}\frac{\partial}{\partial\mu}ln L(\mu,\sigma)=0,\\\frac{\partial}{\partial\sigma^2}\ln L(\mu,\sigma)=0,\end{cases}</script></p>

<p><strong>可得</strong></p>



<p><script type="math/tex; mode=display" id="MathJax-Element-828">\begin{cases}\frac{1}{\sigma^2}(\sum_{i=1}^2x_i-n\mu)=0,\\-\frac{n}{2\sigma^2}+\frac{1}{2(\sigma^2)^2}\sum_{i=1}^n(x_i-\mu)^2=0.\end{cases}</script></p>

<p><strong>解得</strong></p>



<p><script type="math/tex; mode=display" id="MathJax-Element-843">\begin{cases}\widehat{\mu}=\frac{1}{n}\sum_{i=1}^n x_i=\overline{x}, \\\widehat{\sigma}^2=\frac{1}{n}\sum_{i=1}^n(x_i-\overline{x})^2.\end{cases}</script></p>

<p>故<script type="math/tex" id="MathJax-Element-844">μ和δ2</script>的<strong>最大似然估计量</strong>分别为 <br>
<script type="math/tex; mode=display" id="MathJax-Element-845">\widehat{\mu}=\overline{X}，\widehat{\delta^2}=\frac{1}{n}\sum_{i=1}^n(X_i-\overline{X})^2</script></p>
