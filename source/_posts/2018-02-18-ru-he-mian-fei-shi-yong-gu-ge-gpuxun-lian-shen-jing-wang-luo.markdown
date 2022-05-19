---
layout: post
title: "如何免费使用谷歌GPU训练神经网络"
date: 2018-02-18 21:21:44 +0800
comments: true
categories: 神经网络与深度学习
---
﻿<h1 id="完全云端运行免费使用谷歌gpu训练神经网络">完全云端运行：免费使用谷歌GPU训练神经网络</h1>

<h2 id="背景">背景</h2>

<p>对，你没有听错，高大上的ＧＰＵ，现在不花钱也能用上了。这是Google的一项免费云端机器学习服务，全名Colaboratory。</p>

<p><strong>Colaboratory</strong> 是一个 Google 研究项目，旨在帮助传播机器学习培训和研究成果。它是一个 Jupyter 笔记本环境，不需要进行任何设置就可以使用，并且完全在云端运行。Colaboratory 笔记本存储在 Google 云端硬盘中，并且可以共享，就如同您使用 Google 文档或表格一样。Colaboratory 可免费使用，而且最重要的还提供免费的英伟达Tesla K80 GPU。还有这等好事？事不宜迟，本文马上介绍如何使用 Google CoLaboratory 训练神经网络。</p>

<h2 id="准备工作">准备工作</h2>

<p><strong>在Google Drive上创建文件夹</strong></p>

<p><img src="https://ws1.sinaimg.cn/large/a3d23450gy1fokw4gt73cj20au0cdmxt.jpg" alt="" title=""></p>

<p>Colab用的数据都存储在Google Drive云端硬盘上，所以，我们需要先指定要在Google Drive上用的文件夹。</p>

<p>比如说，可以在Google Drive上创建一个“app”文件夹，或者其他什么名字，也可以选择Colab笔记本默认的文件夹。</p>

<h2 id="新建colab笔记本">新建Colab笔记本</h2>

<p>在刚刚创建的app文件夹里点击右键，选择<strong>“更多”</strong>，然后从菜单里选择<strong>“Colaboratory”</strong>，这样就新建出了一个Colab笔记本。</p>

<p>若是更多选项中没有<strong>“Colaboratory”</strong>选项，可以点击<strong>“关联更多应用”</strong>选项，然后在打开的页面中，搜索<strong>“Colaboratory”</strong>，然后再点<strong>关联应用</strong>，再次点击右键就可以在<strong>“更多”</strong>选项中看到<strong>“Colaboratory”</strong>选项了。</p>

<p><img src="https://ws1.sinaimg.cn/large/a3d23450gy1fokw7hbuf6j20je0hpwfn.jpg" alt="" title=""></p>

<h2 id="设置免费gpu">设置免费GPU</h2>

<p>新建Colaboratory成功后，在笔记本里点Edit&gt;Notebook settings（编辑&gt;笔记本设置），或者Runtime&gt;Change runtime type（运行时&gt;改变运行时类型），然后在Hardware accelerator（硬件加速器）一栏选择GPU。</p>

<p><img src="https://ws1.sinaimg.cn/large/a3d23450gy1fokwett0eij209b091jrn.jpg" alt="" title=""></p>

<p>然后，Google Colab就可以用了。</p>

<h2 id="关联google-drive">关联Google Drive</h2>

<p>为了能让Colaboratory使用到你的Google Drive的文件，我们需要先运行下面这些代码，来安装必要的库、执行授权。</p>

<pre class="prettyprint"><code class="language-sh hljs lasso"><span class="hljs-subst">!</span>apt<span class="hljs-attribute">-get</span> install <span class="hljs-attribute">-y</span> <span class="hljs-attribute">-qq</span> software<span class="hljs-attribute">-properties</span><span class="hljs-attribute">-common</span> python<span class="hljs-attribute">-software</span><span class="hljs-attribute">-properties</span> module<span class="hljs-attribute">-init</span><span class="hljs-attribute">-tools</span>
<span class="hljs-subst">!</span>add<span class="hljs-attribute">-apt</span><span class="hljs-attribute">-repository</span> <span class="hljs-attribute">-y</span> ppa:alessandro<span class="hljs-attribute">-strada</span>/ppa <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&gt;</span> /dev/<span class="hljs-built_in">null</span>
<span class="hljs-subst">!</span>apt<span class="hljs-attribute">-get</span> update <span class="hljs-attribute">-qq</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&gt;</span> /dev/<span class="hljs-built_in">null</span>
<span class="hljs-subst">!</span>apt<span class="hljs-attribute">-get</span> <span class="hljs-attribute">-y</span> install <span class="hljs-attribute">-qq</span> google<span class="hljs-attribute">-drive</span><span class="hljs-attribute">-ocamlfuse</span> fuse
from google<span class="hljs-built_in">.</span>colab <span class="hljs-keyword">import</span> auth
auth<span class="hljs-built_in">.</span>authenticate_user()
from oauth2client<span class="hljs-built_in">.</span>client <span class="hljs-keyword">import</span> GoogleCredentials
creds <span class="hljs-subst">=</span> GoogleCredentials<span class="hljs-built_in">.</span>get_application_default()
<span class="hljs-keyword">import</span> getpass
<span class="hljs-subst">!</span>google<span class="hljs-attribute">-drive</span><span class="hljs-attribute">-ocamlfuse</span> <span class="hljs-attribute">-headless</span> <span class="hljs-attribute">-id</span><span class="hljs-subst">=</span>{creds<span class="hljs-built_in">.</span>client_id} <span class="hljs-attribute">-secret</span><span class="hljs-subst">=</span>{creds<span class="hljs-built_in">.</span>client_secret} <span class="hljs-subst">&lt;</span> /dev/<span class="hljs-built_in">null</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">|</span> grep URL
vcode <span class="hljs-subst">=</span> getpass<span class="hljs-built_in">.</span>getpass()
<span class="hljs-subst">!</span>echo {vcode} <span class="hljs-subst">|</span> google<span class="hljs-attribute">-drive</span><span class="hljs-attribute">-ocamlfuse</span> <span class="hljs-attribute">-headless</span> <span class="hljs-attribute">-id</span><span class="hljs-subst">=</span>{creds<span class="hljs-built_in">.</span>client_id} <span class="hljs-attribute">-secret</span><span class="hljs-subst">=</span>{creds<span class="hljs-built_in">.</span>client_secret}
</code></pre>

<p>运行的时候应该会看到下图所示的结果： <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fokwi71w3uj20jv07q41s.jpg" alt="" title=""></p>

<p>看见那个链接之后，点击它，复制验证码并粘贴到文本框里。（这里其实是调用了Google Drive的SDK来访问你的Google Drive，而这个验证码就相当于access_key了）</p>

<p>授权完成后，就可以挂载Google Drive了：</p>



<pre class="prettyprint"><code class="language-sh hljs lasso"><span class="hljs-subst">!</span>mkdir <span class="hljs-attribute">-p</span> drive
<span class="hljs-subst">!</span>google<span class="hljs-attribute">-drive</span><span class="hljs-attribute">-ocamlfuse</span> drive</code></pre>

<h2 id="测试gpu">测试GPU</h2>

<p>这时，我们在本地电脑上创建一个.py文件来测试一下，挂载是否成功以及GPU是否在工作吧。</p>



<pre class="prettyprint"><code class="language-sh hljs bash"><span class="hljs-built_in">echo</span> <span class="hljs-string">"import tensorflow as tf\nprint(tf.test.gpu_device_name())"</span> &gt; test.py</code></pre>

<p>然后将test.py上传到我们开始时创建的app的文件夹里。</p>

<p>然后在Colaboratory笔记本中运行一下代码：</p>



<pre class="prettyprint"><code class="language-sh hljs diff"><span class="hljs-change">!python3 drive/app/test.py</span></code></pre>

<p>不出意外的话，就会输出类似以下的结果：</p>

<pre class="prettyprint"><code class="language-sh hljs applescript">/usr/<span class="hljs-keyword">local</span>/lib/python3<span class="hljs-number">.6</span>/dist-packages/h5py/__init__.py:<span class="hljs-number">36</span>: FutureWarning: Conversion <span class="hljs-keyword">of</span> <span class="hljs-keyword">the</span> <span class="hljs-keyword">second</span> argument <span class="hljs-keyword">of</span> issubdtype <span class="hljs-keyword">from</span> `float` <span class="hljs-keyword">to</span> `np.floating` <span class="hljs-keyword">is</span> deprecated. In future, <span class="hljs-keyword">it</span> will be treated <span class="hljs-keyword">as</span> `np.float64 == np.dtype(float).type`.
  <span class="hljs-keyword">from</span> ._conv import register_converters <span class="hljs-keyword">as</span> _register_converters
<span class="hljs-number">2018</span>-<span class="hljs-number">02</span>-<span class="hljs-number">18</span> <span class="hljs-number">12</span>:<span class="hljs-number">37</span>:<span class="hljs-number">05.172726</span>: I tensorflow/stream_executor/cuda/cuda_gpu_executor.cc:<span class="hljs-number">898</span>] successful NUMA node <span class="hljs-command">read</span> <span class="hljs-keyword">from</span> SysFS had negative value (-<span class="hljs-number">1</span>), <span class="hljs-keyword">but</span> there must be <span class="hljs-keyword">at</span> least one NUMA node, so <span class="hljs-keyword">returning</span> NUMA node zero
<span class="hljs-number">2018</span>-<span class="hljs-number">02</span>-<span class="hljs-number">18</span> <span class="hljs-number">12</span>:<span class="hljs-number">37</span>:<span class="hljs-number">05.172988</span>: I tensorflow/core/common_runtime/gpu/gpu_device.cc:<span class="hljs-number">1208</span>] Found device <span class="hljs-number">0</span> <span class="hljs-keyword">with</span> properties: 
<span class="hljs-property">name</span>: Tesla K80 major: <span class="hljs-number">3</span> minor: <span class="hljs-number">7</span> memoryClockRate(GHz): <span class="hljs-number">0.8235</span>
pciBusID: <span class="hljs-number">0000</span>:<span class="hljs-number">00</span>:<span class="hljs-number">04.0</span>
totalMemory: <span class="hljs-number">11.17</span>GiB freeMemory: <span class="hljs-number">503.62</span>MiB
<span class="hljs-number">2018</span>-<span class="hljs-number">02</span>-<span class="hljs-number">18</span> <span class="hljs-number">12</span>:<span class="hljs-number">37</span>:<span class="hljs-number">05.173016</span>: I tensorflow/core/common_runtime/gpu/gpu_device.cc:<span class="hljs-number">1308</span>] Adding visible gpu devices: <span class="hljs-number">0</span>
<span class="hljs-number">2018</span>-<span class="hljs-number">02</span>-<span class="hljs-number">18</span> <span class="hljs-number">12</span>:<span class="hljs-number">37</span>:<span class="hljs-number">05.457665</span>: I tensorflow/core/common_runtime/gpu/gpu_device.cc:<span class="hljs-number">989</span>] Creating TensorFlow device (/device:GPU:<span class="hljs-number">0</span> <span class="hljs-keyword">with</span> <span class="hljs-number">243</span> MB memory) -&gt; physical GPU (device: <span class="hljs-number">0</span>, <span class="hljs-property">name</span>: Tesla K80, pci bus <span class="hljs-property">id</span>: <span class="hljs-number">0000</span>:<span class="hljs-number">00</span>:<span class="hljs-number">04.0</span>, compute capability: <span class="hljs-number">3.7</span>)
/device:GPU:<span class="hljs-number">0</span></code></pre>

<p>到这里的话，那么恭喜你，你的GPU环境基本可以用了，只要把你的项目文件夹上传到你的app文件夹下，搭建好深度学习的库环境，就可以通过类似上面的操作进行神经网络训练了。</p>



<h2 id="tips">Tips</h2>



<h3 id="如何安装库">如何安装库？</h3>

<p>安装Keras：</p>

<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-subst">!</span>pip install <span class="hljs-attribute">-q</span> keras
<span class="hljs-keyword">import</span> keras</code></pre>

<p>安装PyTorch：</p>



<pre class="prettyprint"><code class=" hljs avrasm">!pip install -q http://download<span class="hljs-preprocessor">.pytorch</span><span class="hljs-preprocessor">.org</span>/whl/cu75/torch-<span class="hljs-number">0.2</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.post</span>3-cp27-cp27mu-manylinux1_x86_64<span class="hljs-preprocessor">.whl</span> torchvision
import torch</code></pre>

<p>安装OpenCV：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-subst">!</span>apt<span class="hljs-attribute">-get</span> <span class="hljs-attribute">-qq</span> install <span class="hljs-attribute">-y</span> libsm6 libxext6 <span class="hljs-subst">&amp;&amp;</span> pip install <span class="hljs-attribute">-q</span> <span class="hljs-attribute">-U</span> opencv<span class="hljs-attribute">-python</span>
<span class="hljs-keyword">import</span> cv2</code></pre>

<p>安装XGBoost：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-subst">!</span>pip install <span class="hljs-attribute">-q</span> xgboost<span class="hljs-subst">==</span><span class="hljs-number">0.4</span>a30
<span class="hljs-keyword">import</span> xgboost</code></pre>

<p>安装GraphViz：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-subst">!</span>apt<span class="hljs-attribute">-get</span> <span class="hljs-attribute">-qq</span> install <span class="hljs-attribute">-y</span> graphviz <span class="hljs-subst">&amp;&amp;</span> pip install <span class="hljs-attribute">-q</span> pydot
<span class="hljs-keyword">import</span> pydot</code></pre>

<p>安装7zip Reader：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-subst">!</span>apt<span class="hljs-attribute">-get</span> <span class="hljs-attribute">-qq</span> install <span class="hljs-attribute">-y</span> libarchive<span class="hljs-attribute">-dev</span> <span class="hljs-subst">&amp;&amp;</span> pip install <span class="hljs-attribute">-q</span> <span class="hljs-attribute">-U</span> libarchive
<span class="hljs-keyword">import</span> libarchive</code></pre>

<p>安装其他库：</p>



<pre class="prettyprint"><code class=" hljs cmake">用!pip <span class="hljs-keyword">install</span>或者!apt-get <span class="hljs-keyword">install</span>命令。</code></pre>
