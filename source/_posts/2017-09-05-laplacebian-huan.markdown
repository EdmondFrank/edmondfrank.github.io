---
layout: post
title: "Laplace变换"
date: 2017-09-05 21:20:57 +0800
comments: true
categories: 课程笔记
---
<h1 id="拉普拉斯变换">拉普拉斯变换</h1>

<p>对于复值函数<script type="math/tex" id="MathJax-Element-1978">f(t)</script>,若<script type="math/tex" id="MathJax-Element-1979">\int_0^{\infty } f(t) \epsilon ^{-s t} \, dt</script>在复平面上的某一个区域<script type="math/tex" id="MathJax-Element-1980">D(s\in D)</script> 内收敛于<script type="math/tex" id="MathJax-Element-1981">F(s)</script>,则称：</p>

<p><script type="math/tex" id="MathJax-Element-1982">F(s)=\int_0^{\infty } f(t) \epsilon ^{-s t} \, dt</script></p>

<p>为函数的拉普拉斯变换（Laplace）变换（简称拉氏变换），记为</p>

<p><script type="math/tex" id="MathJax-Element-1983">F(s)=L[f(t)]</script></p>

<p>在科技领域，一般是对时间为自变量的函数进行拉普拉斯变换，即在t &lt; 0时，函数无意义或不需要考虑。</p>

<h3 id="线性性质">线性性质</h3>

<p>设<script type="math/tex" id="MathJax-Element-893">\alpha _1,\alpha _2</script>为常数， <br>
<script type="math/tex" id="MathJax-Element-894">F_1(s) = L[f_1(t)] , F_2(s) = L[f_2(t)] </script> 则  <br>
<script type="math/tex" id="MathJax-Element-895"> L[\alpha _1f_1(t) + \alpha _2f_2(t)] = \alpha_1F_1(s) + \alpha_2F_2(s)</script></p>

<h3 id="时移性质">时移性质</h3>

<p>若 <script type="math/tex" id="MathJax-Element-905">L[f(t)] = F(s)</script>，对于<script type="math/tex" id="MathJax-Element-906">t_0 > 0</script>，有<script type="math/tex" id="MathJax-Element-907">L[f( t-t_0 )] = e^{-st_0} F(s)</script></p>

<h3 id="频移性质">频移性质</h3>

<p>若 <script type="math/tex" id="MathJax-Element-947">L[f(t)] = F(s)</script>，则对任意常数a，有 <br>
<script type="math/tex" id="MathJax-Element-948">L[e^{at}f(t)] = F(s-a)</script></p>

<h3 id="微分性质">微分性质</h3>

<p>若 <script type="math/tex" id="MathJax-Element-1369">L[f(t)] = F(s)</script>，且<script type="math/tex" id="MathJax-Element-1370">f'(t)也是象原函数，则</script> <br>
<script type="math/tex" id="MathJax-Element-1371">L[f'(t)] = sF(s) - f(0^+)</script> <br>
这里，<script type="math/tex" id="MathJax-Element-1372"> \lim_{t\to 0^+}f( t)</script></p>

<p>若 <script type="math/tex" id="MathJax-Element-1373">L[f(t)] = F(s)</script>，则 <br>
<script type="math/tex" id="MathJax-Element-1374">L[ (-t)^n f(t)] = F^{(n)}(s) , n=0,1,2,...</script></p>



<h3 id="积分性质">积分性质</h3>

<p>若 <script type="math/tex" id="MathJax-Element-1631">L[f(t)] = F(s)</script>，则 <br>
<script type="math/tex" id="MathJax-Element-1632">L[\int_0^t f(\tau ) \, d\tau] = \frac{1}{s}F(s)</script></p>

<p>若 <script type="math/tex" id="MathJax-Element-1633">L[f(t)] = F(s)</script>，积分<script type="math/tex" id="MathJax-Element-1634">\int_s^\infty F(u ) \, du</script>收敛，则<script type="math/tex" id="MathJax-Element-1635">\frac{f(t)}{t}</script>的拉普拉斯变换存在，且</p>

<p><script type="math/tex" id="MathJax-Element-1636">L[\frac{f(t)}{t}] = \int_s^\infty F(u ) \, du</script></p>



<h3 id="极限性质">极限性质</h3>

<p>1.初值关系 <br>
若<script type="math/tex" id="MathJax-Element-1973">L[f(t)] = F(s)</script>，则<script type="math/tex" id="MathJax-Element-1974">f(0^+) =  \lim_{s\to \infty} sF( s)</script></p>

<p>2.终值关系 <br>
若<script type="math/tex" id="MathJax-Element-1975">L[f(t)] = F(s),且f(+\infty)存在,sF(s)</script>的所有奇点在半平面<script type="math/tex" id="MathJax-Element-1976">Re(s) < \sigma _0内，其中\sigma是f(t)的增长指数</script>，则</p>

<p><script type="math/tex" id="MathJax-Element-1977">f(+\infty) = lim_{s\to0}sF(s)</script></p>
