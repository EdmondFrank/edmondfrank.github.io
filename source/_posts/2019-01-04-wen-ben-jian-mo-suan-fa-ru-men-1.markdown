---
layout: post
title: "文本建模算法入门-1"
date: 2019-01-04 13:43:27 +0800
comments: true
categories: 统计学习
---
﻿<h1 id="文本建模算法入门一">文本建模算法入门（一）</h1>



<h3 id="0x1-文本建模">0x1 文本建模</h3>

<p>文本并不像其他数值型的数据一样可以比较轻易的通过运算、函数、方程、矩阵等来表达他们之间的相互关系。</p>

<p>在处理一篇文本的时候，假设每一个文本储存为一篇文档，那么从人的视角来看，这可以说是一段有序的词序列<script type="math/tex; mode=display" id="MathJax-Element-1">Document=(w_1,w_2,...,w_n)</script> <br>
统计学家将这些序列的生成，生动地描绘成了一个“上帝的游戏”，即人类产生的所有语料的文本都可以看作是：一个伟大的上帝在天堂中掷骰子形成的。我们所看到的文本其实就是这个游戏掷了若干次后产生的序列。</p>

<p>那么，在这个游戏中，我们<strong>最需要关注的两个核心问题</strong>就出现了：</p>

<ol>
<li>上帝有怎样的骰子？</li>
<li>上帝是怎么掷的骰子？</li>
</ol>

<p>对应这两个问题，各大学家持着不同的观点，于是便有了以下三种模型：</p>

<ol>
<li>Unigram Model</li>
<li>Topic Model(PLSA)</li>
<li>LDA</li>
</ol>



<h4 id="0x11-unigram-model">0x11 Unigram Model</h4>

<p>Unigram Model是非常简单直接的，它假设</p>

<ol>
<li>上帝只有一个V面的骰子，每一个面对应一个词，同时每个面的概率不一样。（这里可以通过“老千骰子”来理解下，有些面因为被做过了“手脚”，所以抛到的几率就大了，那如果没个面都这样，那么每个面抛到的几率也就不一样了）</li>
<li>每抛一次骰子，抛出的面就对应有一个词，那么抛n次骰子后，按抛掷顺序产生的序列就生成了一篇n个词的文档</li>
</ol>

<p><img src="https://ws1.sinaimg.cn/large/a3d23450gy1fyudrkxl14j2086052dg2.jpg" alt="enter image description here" title=""></p>

<p>那现在我们把上帝这个唯一的骰子各个面的概率记为<script type="math/tex" id="MathJax-Element-2">\vec{p}=\{p_1,p_2,...,p_v\}</script>，然后我们把掷这个V面骰子的实验记作<script type="math/tex" id="MathJax-Element-3">w \sim Mult(w|\vec{p}) </script></p>

<p>那么对应一篇文档d，该文档生成的概率就是 <br>
<script type="math/tex; mode=display" id="MathJax-Element-4">p(\vec{w})=p(w_1,w_2,...,w_n)=p(w_1)p(w_2)...p(w_n)</script></p>

<p>而文档和文档之间我们认为是独立的，所以如果语料中有多篇文档<script type="math/tex" id="MathJax-Element-5">W=(\vec{w_1},\vec{w_2},...,\vec{w_m})</script>，则该语料生成的概率就是<script type="math/tex; mode=display" id="MathJax-Element-6">p(W)=p(\vec{w_1})p(\vec{w_2})...p(\vec{w_m})</script></p>

<p>在Unigram Model中，我们又假设了文档之间是独立可交换的。即，词与词之间的顺序对文档表示并不造成影响，一篇文档相当于一个袋子，里面装着一些词。这样的模型也称为<strong>词袋模型（Bag-of-words）。</strong></p>

<p>那么，如果语料中的总词频是N，在N个词中，如果我们关注每一个词<script type="math/tex" id="MathJax-Element-7">w_i</script>的发生次数<script type="math/tex" id="MathJax-Element-8">n_i</script>，则<script type="math/tex" id="MathJax-Element-9">\vec{n}=(n_1,n_2,...,n_V)</script>正好是一个多项分布 <br>
<script type="math/tex; mode=display" id="MathJax-Element-10">p(\vec{n})=Mult(\vec{n}|\vec{p},N)</script> <br>
此时，语料的概率是 <br>
<script type="math/tex; mode=display" id="MathJax-Element-11">p(W)=p(\vec{w_1})p(\vec{w_2})...p(\vec{w_m})=\prod_{k=1}^Vp_k^{n_k}</script></p>



<h4 id="0x110-贝叶斯unigram-model假设">0x110 贝叶斯Unigram Model假设</h4>

<p>在贝叶斯统计学派看来，上帝拥有唯一一个固定的骰子是不合理的。于是他们觉得以上模型的骰子<script type="math/tex" id="MathJax-Element-12">\vec{p}</script>不应该唯一固定，而应该也是一个随机变量。</p>

<p>那这样我们就可以将整个掷骰子的游戏过程更新成以下形式：</p>

<ol>
<li>上帝有一个装着无穷多骰子的罐子，里面有各种骰子，每个骰子有V个面。每一个面对应一个词</li>
<li>上帝从罐子抽出一个骰子，然后用这个骰子不断的抛，每抛一次骰子，抛出的面就对应有一个词，那么抛n次骰子后，按抛掷顺序产生的序列就生成了一篇n个词的文档 <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fyufvboyshj20a106cjs7.jpg" alt="enter image description here" title=""></li>
</ol>

<p>在以上的假设之下，由于我们事先并不知道上帝用了哪个骰子<script type="math/tex" id="MathJax-Element-13">\vec{p}</script>，所以每个骰子都是有可能被使用的，只是使用的概率由先验分布<script type="math/tex" id="MathJax-Element-14">p(\vec{p})</script>决定，对每一个具体的骰子<script type="math/tex" id="MathJax-Element-15">\vec{p}</script>，由该骰子产生数据的概率是<script type="math/tex" id="MathJax-Element-16">p(W|\vec{p})</script>，所以最终数据产生的概率就是对每个骰子上产生的数据概率进行积分累加求和 <br>
<script type="math/tex; mode=display" id="MathJax-Element-17">P(W)=\int p(W|\vec{p})p(\vec{p})d\vec{p}</script> <br>
在贝叶斯分析的框架之下，此处的先验分布概率其实就是一个多项分布的概率，其中一个比较好的选择即<strong>多项分布对应的共轭分布：Dirichlet分布</strong></p>



<h4 id="0x12-plsa-topic-model">0x12 PLSA Topic Model</h4>

<p>再来回顾Unigram Model我们发现：这个模型的假设过于简单，和人类真实的书写有着较大的差距</p>

<p>从人类视角看，我们在日常构思文章中，我们往往要先确定自己文章的主旨，包含了哪些主题，然后再围绕着这些主题展开阐述。</p>

<p>例如，一篇关于现代教育的文章，它可能就包含了这些主题：教育方法、多媒体技术、互联网等。篇幅上可能以教育方法为主，而其他为辅。然后，在不同的主题里面就包含了许多主题领域内常见的关键词。例如，谈到互联网时，我们会提及Web、Tcp等。</p>

<p>这样一种直观的想法就在<strong>PLSA模型中进行了明确的体现</strong>，如果我们再利用这个想法更新“掷骰子”的游戏就有以下情形：</p>

<ol>
<li>上帝有两种类型骰子，一类是doc-topic骰子，骰子共k个面代表了k个主题；一类是topic-word骰子，骰子V个面，每面对应主题内的一个词</li>
<li>生成文章的时候，首先要先创造一个特定的doc-topic骰子，使得骰子内的主题围绕文章要阐述的主题</li>
<li>投掷doc-topic骰子，得到一个主题z</li>
<li>根据得到的主题z，找到对应的topic-word骰子，投掷它得到一个词</li>
<li>不断重复3，4步，直至文章生成完成</li>
</ol>

<p><img src="https://ws1.sinaimg.cn/large/a3d23450gy1fyugrx9ybdj20a50810tj.jpg" alt="enter image description here" title=""></p>

<p>在上面的游戏规则中，文档与文档之间顺序无关，同一个文档内的词的顺序也是无关的。所以还是一个bag-of-words模型。</p>

<p>那么，在第m篇文档Dm中每个词的生成概率为： <br>
<script type="math/tex; mode=display" id="MathJax-Element-18">p(w|D_m)=\sum^K_{z=1}p(w|z)p(z|D_m)=\sum^K_{z=1}\phi_{zw_i}\theta_{dz}</script> <br>
<script type="math/tex" id="MathJax-Element-19">\phi_{zw_i}</script>:对应游戏中K个topic-word骰子中第z个骰子对应的词列表</p>

<p><script type="math/tex" id="MathJax-Element-20">\theta_{dz}</script>:文档对应的第z个主题，即对应的doc-topic</p>

<p>所以整篇文档的生成概率就为： <br>
<script type="math/tex; mode=display" id="MathJax-Element-21">p(w|D_m)=\prod^n_{i=1}\sum^K_{z=1}p(w_i|z)p(z|D_m)</script></p>



<h4 id="0x13-ldalatent-dirichlet-allocation">0x13 LDA(Latent Dirichlet Allocation)</h4>

<p>就像Unigram Model 加入贝叶斯框架那样，doc-topic和topic-word都是模型的参数，即随机变量。于是类似对Unigram Model的改造对以上两个骰子模型加入先验分布。然后由于<script type="math/tex" id="MathJax-Element-22">\vec{\theta}</script>，<script type="math/tex" id="MathJax-Element-23">\vec{\phi}</script>都对应着多项分布，因此先验分布依旧选择Drichlet分布。于是得到的这个新模型就是<strong>LDA(Latent Dirichlet Allocation)模型</strong> <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fyuhfhrza5j20cx09bwgm.jpg" alt="enter image description here" title=""></p>

<p>在LDA模型中，上帝的游戏规则就又被更新成如下情形：</p>

<ol>
<li>上帝有两个罐子，第一个装着都哦doc-topic骰子，第二个装着topic-word骰子</li>
<li>上帝随机的从第二个罐子中独立抽出K个topic-doc骰子，编号1-K</li>
<li>每次生成新文档时，从第一个罐子随机抽一个doc-topic骰子</li>
<li>投掷得到的doc-topic骰子。得到一个主题编号z</li>
<li>在K个主题骰子里面选择编号为z的骰子，投掷骰子，得到一个词</li>
<li>重复4，5步，直至文档生成完成</li>
</ol>



<h3 id="0x2-后记">0x2 后记</h3>

<p>至此，入门篇（一）的内容就写到这了。由于是第一篇，文章中避过了许多较为复杂的数学证明和计算，尤其是关于LDA模型的。这样是为了，先建立对文本建模思路和过程的直观认识，而不是一上来就深究细节。</p>

<p>加上笔者目前也是在学习阶段，后面的细节再慢慢地一一补充，大家共同进步 !! <br>
                        \(^_^)/</p>
