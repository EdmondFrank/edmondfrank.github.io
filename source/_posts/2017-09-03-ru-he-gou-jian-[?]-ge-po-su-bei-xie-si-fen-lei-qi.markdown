---
layout: post
title: "如何构建一个朴素贝叶斯分类器"
date: 2017-09-03 13:22:23 +0800
comments: true
categories: 机器学习
---
<h1 id="如何构建一个朴素贝叶斯分类器">如何构建一个朴素贝叶斯分类器</h1>



<h3 id="概念">概念</h3>

<p><strong>朴素贝叶斯分类</strong>是一些使用概率论来进行分类的方法中的一种代表。之所以成为为<strong>朴素</strong>，是因为整个形式化过程只做了最原始，最简单的假设。其具体表现为：在分类过程中，我们都假设为特征条件都是<strong>相互独立</strong>的，不互相构成影响。而贝叶斯即说明该方法是基于贝叶斯定理的。</p>

<p>朴素贝叶斯是贝叶斯决策理论的一部分，对比其他分类方法而言，其<strong>优点</strong>有：在数据较少的情况下仍然有效，并且可以处理多类别的问题，而不仅仅是二分类。<strong>但</strong>也存在对输入数据的准备方式较为敏感的缺点。</p>



<h3 id="目前在各方面的应用">目前在各方面的应用</h3>

<ol>
<li>垃圾邮件分类，即著名的贝叶斯过滤器</li>
<li>文本自动归类</li>
<li>文本情感的分析，话题分析。</li>
</ol>



<h3 id="核心思想">核心思想</h3>

<p><img src="https://i.loli.net/2017/09/02/59aa6f0d7afce.png" alt="beyas.png" title=""></p>

<p>现在假设我们有一张如上所示的两类数据的统计参数。 <br>
我们现在用p1(x,y)来表示数据点(x,y)属于类别X（图中的蓝点）的概率，用p2(x,y)表示数据点属于类别Y（图中的黄点）的概率。那么对于一个新的数据点(x,y)，我们可以用以下的规则来判断它的类别：</p>

<ul>
<li><script type="math/tex" id="MathJax-Element-24">If p1(x,y) > p2(x,y) then (x,y) -> X</script></li>
<li><script type="math/tex" id="MathJax-Element-25">If p2(x,y) > p1(x,y) then (x,y) -> Y</script></li>
</ul>

<p>也就是说，我们更倾向于选择高概率所对应的类别。这就是<strong>贝叶斯决策理论</strong>的核心思想。用一句话总结即：选择具有最高概率的决策。</p>

<h3 id="使用条件概率进行分类">使用条件概率进行分类</h3>

<p>从上面的推理我们得到两个分类准则：</p>

<ul>
<li><script type="math/tex" id="MathJax-Element-61">If p1(x,y) > p2(x,y) then (x,y) -> X</script></li>
<li><script type="math/tex" id="MathJax-Element-62">If p2(x,y) > p1(x,y) then (x,y) -> Y</script></li>
</ul>

<p>但是，这两个准则只是为了简化描述整个分类问题，而真正来说，在使用贝叶斯方法进行分类时，我们需要计算和比较的应该是 p(X|x,y) 和 p(Y|x,y) ，它们所代表的意义是：在给定某个由（x，y）表示的数据点之后，它来自X类别的概率和它来自Y类别的概率分别是多少？知道这点之后，我们来重新定义我们的分类准则：</p>

<ul>
<li><script type="math/tex" id="MathJax-Element-63">If P(X|x,y) > P(Y|x,y) then (x,y) -> X</script></li>
<li><script type="math/tex" id="MathJax-Element-64">If P(X|x,y) < P(Y|x,y) then (x,y) -> Y</script></li>
</ul>

<p>然后，我们可以再通过贝叶斯公式的转换把未知的概率用已知的概率计算出来。</p>

<p><strong>使用实例：垃圾邮件判别</strong> <br>
首先，我们假设有以下的统计数据：</p>

<ul>
<li>在74封电子邮件中，有30封为垃圾邮件。</li>
<li>在以上的74封电子邮件中，有51封邮件包含“sex”一词。</li>
<li>其中，20封包含了“sex”一词的邮件被认为是垃圾邮件。</li>
</ul>

<p>知道了以上这些简单的前提条件之后，我们可以尝试通过贝叶斯方法来求解我们下一封收到包含“sex”一词的邮件是垃圾邮件的概率是多少？</p>

<p><script type="math/tex" id="MathJax-Element-65">P (\text{spam}|\text{sex})=\frac{P(spam) * P  (\text{sex}|\text{spam})}{P (\text{sex})}=\frac {(20\div30)* (30\div74)} {51\div74} =\frac{20}{51}=0.39</script></p>

<p>其中，公式中的<strong>spam</strong>代表垃圾邮件的意思（1937年7月5日，美国罐头肉制造商Jay Hormel发布以其名字命名的「Hormel Spiced Ham（荷美尔香料火腿）」，后来通过命名比赛改名为 SPAM(Spiced Pork and Ham)，有添加香料（Spices）的猪肉火腿罐头。至于为何 SPAM演变成垃圾邮件呢？有一说法是源于一部英国喜剧团（Monty Python）曾在一出讽刺剧「spam-loving vikings（爱吃肉罐头的维京人），剧中有对夫妻去餐厅用餐，妻子不想吃SPAM罐头，可是在餐厅里有一大群人，高声地唱讼赞美「SPAM」称颂肉罐头的美味多达120次，让其他的用餐客人无可奈何。从此 SPAM就成为「重复、毫无益处、喧宾夺主、令人厌烦邮件」的代名词。就像当年经济萧条，人们买不起鲜肉，而吃的SPAM 肉罐头一样,没有营养成分。）</p>

<p>上面我们得出了一个词对整篇文章的分类判断情况，接下来我们可以进一步扩展这个问题，通过再增加以下为已知前提：</p>

<ul>
<li>25封邮件包含“money”一词，而其中24封被认为是垃圾邮件。</li>
</ul>

<p>那么现在当收到一封同时包含“sex”，“money”这两个词的邮件，它为垃圾邮件的可能性又有多大呢？</p>

<p><script type="math/tex" id="MathJax-Element-66">\frac{P(\text{spam}) P(\text{money}|\text{spam}) P(\text{sex}|\text{spam}\cap  
   \text{money})}{P(\text{money}) P(\text{sex}|\text{monry})}</script></p>

<p>写到这里我们发现，当我们不断增加相关词进行判定时，上述的贝叶斯推理公式也将越来越复杂。这时，就要开始应用朴素思想了，我们假设“sex”和“money”两者出现的事件都相互独立（虽然现实情况往往并不是这样的，当出现一些广告相关的词词语时，“金钱”一词往往就有很大的几率出现在后文，但这里采用朴素的思想来近似在分类上也足够准确了），这样我们就可以把上面的公式简化成：</p>

<p><script type="math/tex" id="MathJax-Element-67">P(\text{spam}|\text{money},\text{sex})=\frac{P(\text{spam})  
   P(\text{money}|\text{spam}) P(\text{sex}|\text{spam})}{p(\text{money})  
   P(\text{sex})}</script></p>

<p>这样，假如我们需要对一封收到的电子邮件进行分类的话，我们只要计算出在给出邮件内的词汇的情况下，它为一封垃圾邮件的条件概率即可。当然，这里存在的一个较大的缺陷就是，当一些词语极度相关时，而我们通过假设他们相互独立计算出来的概率可能并不是那么准确。</p>

<h3 id="分类实现">分类实现</h3>

```ruby
# -*- encoding : utf-8 -*-
class BeyesClassifier::Base
  extend BeyesClassifier::Storage::ActAsStorable
  attr_reader :name
  attr_reader :word_list
  attr_reader :category_list
  attr_reader :training_count

  attr_accessor :tokenizer
  attr_accessor :language

  attr_accessor :thresholds
  attr_accessor :min_prob


  storable :version,:word_list,:category_list,:training_count,:thresholds,:min_prob

  # opts :
  # language
  # stemming : true | false
  # weight
  # assumed_prob
  # storage
  # purge_state ?

  def initialize(name, opts={})
    @version = BeyesClassifier::VERSION

    @name = name

    # This values are nil or are loaded from storage
    @word_list = {}
    @category_list = {}
    @training_count=0

    # storage
    purge_state = opts[:purge_state]
    @storage = opts[:storage] || BeyesClassifier::Base.storage
    unless purge_state
      @storage.load_state(self)
    else
      @storage.purge_state(self)
    end

    # This value can be set during initialization or overrided after load_state
    @thresholds = opts[:thresholds] || {}
    @min_prob = opts[:min_prob] || 0.0


    @ignore_words = nil
    @tokenizer = BeyesClassifier::Tokenizer.new(opts)

  end

  def incr_word(word, category)
    @word_list[word] ||= {}

    @word_list[word][:categories] ||= {}
    @word_list[word][:categories][category] ||= 0
    @word_list[word][:categories][category] += 1

    @word_list[word][:_total_word] ||= 0
    @word_list[word][:_total_word] += 1


    # words count by categroy
    @category_list[category] ||= {}
    @category_list[category][:_total_word] ||= 0
    @category_list[category][:_total_word] += 1

  end

  def incr_cat(category)
    @category_list[category] ||= {}
    @category_list[category][:_count] ||= 0
    @category_list[category][:_count] += 1

    @training_count ||= 0
    @training_count += 1

  end

  # return number of times the word appears in a category
  def word_count(word, category)
    return 0.0 unless @word_list[word] && @word_list[word][:categories] && @word_list[word][:categories][category]
    @word_list[word][:categories][category].to_f
  end

  # return the number of times the word appears in all categories
  def total_word_count(word)
    return 0.0 unless @word_list[word] && @word_list[word][:_total_word]
    @word_list[word][:_total_word].to_f
  end

  # return the number of words in a categories
  def total_word_count_in_cat(cat)
    return 0.0 unless @category_list[cat] && @category_list[cat][:_total_word]
    @category_list[cat][:_total_word].to_f
  end

  # return the number of training item
  def total_cat_count
    @training_count
  end

  # return the number of training document for a category
  def cat_count(category)
    @category_list[category][:_count] ? @category_list[category][:_count].to_f : 0.0
  end

  # return the number of time categories in wich a word appear
  def categories_with_word_count(word)
    return 0 unless @word_list[word] && @word_list[word][:categories]
    @word_list[word][:categories].length
  end

  # return the number of categories
  def total_categories
    categories.length
  end

  # return categories list
  def categories
    @category_list.keys
  end

  # train the classifier
  def train(category, text)
    @tokenizer.each_word(text) {|w| incr_word(w, category) }
    incr_cat(category)
  end

  # classify a text
  def classify(text, default=nil)
    # Find the category with the highest probability
    max_prob = @min_prob
    best = nil

    scores = cat_scores(text)
    scores.each do |score|
      cat, prob = score
      if prob > max_prob
        max_prob = prob
        best = cat
      end
    end

    # Return the default category in case the threshold condition was
    # not met. For example, if the threshold for :spam is 1.2
    #
    #    :spam => 0.73, :ham => 0.40  (OK)
    #    :spam => 0.80, :ham => 0.70  (Fail, :ham is too close)

    return default unless best

    threshold = @thresholds[best] || 1.0

    scores.each do |score|
      cat, prob = score
      next if cat == best
      return default if prob * threshold > max_prob
    end

    return best
  end

  def save_state
    @storage.save_state(self)
  end

  class << self
    attr_writer :storage

    def storage
      @storage = BeyesClassifier::InMemoryStorage.new unless defined? @storage
      @storage
    end

    def open(name)
      inst = self.new(name)
      if block_given?
        yield inst
        inst.save_state
      else
        inst
      end
    end
  end
end

```
<p>完整代码实现可以参考<a href="https://github.com/alexandru/stuff-classifier"><strong>alexandru大神的github项目stuff-classifier</strong></a></p>

<h3 id="算法训练和使用">算法训练和使用</h3>

```ruby
# 训练函数
def train(category, text)
  each_word(text) {|w| increment_word(w, category) }
  increment_cat(category)
end

# 使用
classifier.train :spam, "Grow your penis to 20 inches in just 1 week"
classifier.train :ham,  "I'm hungry, no I don't want your penis"
```

<p>由于本文主要的目标是讲解贝叶斯算法，所以为了方便分词，主要使用英文语料训练，对于中文邮件分类而言，只要采用分词库分词即可，其他部分不变。</p>

<h3 id="提高准确度与算法优化">提高准确度与算法优化</h3>

<h4 id="计算优化">计算优化</h4>

<p>由于浮点数计算的一些先天性问题，如计算效率慢，精度不准确等。因此我们可以用自然对数来代替原本概率的形式。 <br>
<script type="math/tex" id="MathJax-Element-3265">claaify(word_1,word_2,…word_n)=\text{argmax}\sum _{i=1}^n \frac{\log  
   \left(P\left(\left.\text{word}_i\right|\text{spam}\right)\right)}{\log  
   (e)}+\frac{\log (P(\text{spam}))}{\log (e)}</script></p>

<h4 id="拉普拉斯平滑">拉普拉斯平滑</h4>

<p>由于贝叶斯公式依赖的都是用已知概率求未知概率，但是在分类中无法避免的一个情况就是当我们计算<script type="math/tex" id="MathJax-Element-4127">P(word_i|spam)</script>即：在已有的训练的垃圾邮件样本中出现<script type="math/tex" id="MathJax-Element-4128">word_i</script>的概率是多少时，我们遇到了一个从未在训练样本中出现过的词，那么此时就会导致<script type="math/tex" id="MathJax-Element-4129">P(word_i|spam)=0</script>。这样就会影响整体的判别。</p>

<p>这样由于训练样本不足导致分类器整体质量大大下降的问题很常见，为了解决这个问题，我们可以引入<strong>Laplace校准</strong>（即我们要讲的拉普拉斯平滑公式），它的原理非常简单，给未出现特征值，赋予一个“小”的值而不是0。</p>

<p>具体平滑方法如下： <br>
假设离散型随机变量z有{1,2,…,k}个值，我们用<script type="math/tex" id="MathJax-Element-4130">\phi _i=P(z=i)</script>来表示每个值的概率。假设有m个训练样本中，z的观察值是<script type="math/tex" id="MathJax-Element-4131">\left\{z^1,\text{...},z^m\right\}</script>其中每一个观察值对应k个值中的一个。那么根据原来的估计方法可以得到</p>

<p><script type="math/tex" id="MathJax-Element-4132">\phi _j=\frac{\sum _{i=1}^m 1 \left\{z^i=j\right\}}{m}</script></p>

<p>简单来说就是<script type="math/tex" id="MathJax-Element-4133">z=j</script>出现的比例。</p>

<p>拉普拉斯平滑法将每个k值出现次数事先都加1，即假设他们都出现过一次。 <br>
那么修改后的表达式为：</p>

<p><script type="math/tex" id="MathJax-Element-4134">\phi _j=\frac{\sum _{i=1}^m 1 \left\{z^i=j\right\}+1}{m+k}</script></p>

<p>每个z=j的分子都加1，分母加k。可见<script type="math/tex" id="MathJax-Element-4135">\sum _{j=1}^k \phi _j=1</script>。</p>

<p>这样在保持总体事件发现概率比例基本不变的同时，又避免了0概率的问题。</p>

<h4 id="去除停用词">去除停用词</h4>

<p><strong>停用词</strong>是指在信息检索中，为节省存储空间和提高搜索效率，在处理自然语言数据（或文本）之前或之后会自动过滤掉某些字或词，这些字或词即被称为<strong>Stop Words</strong>（停用词）。这些停用词都是人工输入、非自动化生成的，生成后的停用词会形成一个停用词表。但是，并没有一个明确的停用词表能够适用于所有的工具。</p>

<p>通常意义上，Stop Words大致为如下两类：</p>

<ol>
<li>第一类是那些应用十分广泛的词，在Internet上随处可见，比如“Web”一词几乎在每个网站上均会出现，对这样的词搜索引擎无 法保证能够给出真正相关的搜索结果，难以帮助缩小搜索范围，同时还会降低搜索的效率。</li>
<li>第二类就更多了，包括了语气助词、副词、介词、连接词等，通常自身并无明确的意义，只有将其放入一个完整的句子中才有一定作用，如常见的“的”、“在”之类。</li>
</ol>

<p>为了使得最后用于判别邮件类型的词向量能够更加贴近邮件本身真正想表达的含义，我们可以在读取扫描文本并生成词列表时，先<strong>剔除停用词</strong>，再进行下一步的概率计算。</p>

<p>常用中文停用词表： <br>
链接: <a href="https://pan.baidu.com/s/1o8hw5Cm">https://pan.baidu.com/s/1o8hw5Cm</a> 密码: crti</p>

<p>常用英文停用词表： <br>
链接: <a href="https://pan.baidu.com/s/1jIOOfgM">https://pan.baidu.com/s/1jIOOfgM</a> 密码: 7g95</p>

<h4 id="阈值选取">阈值选取</h4>

<p>假设我们计算出一封邮件的<script type="math/tex" id="MathJax-Element-4368">P(spam) = 0.6 </script>那么是否我们因为 <script type="math/tex" id="MathJax-Element-4369">P(spam) = 0.6 > P(not spam)=1-0.6=0.4</script> 就可以把它定义为垃圾邮件呢？显然这样做是不合理的，一个好的概率阈值应该是在训练及测试样本上使得发生误判情况最少时所取得的值。通常我们可以定义一个误差评判函数再通过不同阈值在样本上的评分来选取出最佳的阈值。简单实现就如下面的代码所示：</p>

```ruby
def classify(text, default=nil)
  # Find the category with the highest probability

  max_prob = 0.0
  best = nil
  
  scores = cat_scores(text)
  scores.each do |score|
    cat, prob = score
    if prob > max_prob
      max_prob = prob
      best = cat
    end
  end

  # Return the default category in case the threshold condition was
  # not met. For example, if the threshold for :spam is 1.2
  #
  #    :spam => 0.73, :ham => 0.40  (OK)
  #    :spam => 0.80, :ham => 0.70  (Fail, :ham is too close)

  return default unless best
  threshold = @thresholds[best] || 1.0

  scores.each do |score|
    cat, prob = score
    next if cat == best
    return default if prob * threshold > max_prob
  end
  return best
end
```

<p>最后，写到这里，贝叶斯分类器构建的讲解基本就结束了。通观全篇，我们依旧有个问题并没有解决：朴素贝叶斯分类有一个限制条件，就是特征属性必须有条件独立或基本独立（实际上在现实应用中几乎不可能做到完全独立）。当这个条件成立时，朴素贝叶斯分类法的准确率是最高的，但不幸的是，现实中各个特征属性间往往并不条件独立，而是具有较强的相关性，这样就限制了朴素贝叶斯分类的能力。然而，这个问题也并不是无解的，贝叶斯分类中有一种更高级、应用范围更广的一种算法——<strong>贝叶斯网络（又称贝叶斯信念网络或信念网络）</strong>，信念网络在一定程度上使得模型更接近真实的实际情况，有兴趣的读者可以进一步深入了解。</p>

