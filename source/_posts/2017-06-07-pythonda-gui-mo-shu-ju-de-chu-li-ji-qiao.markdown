---
layout: post
title: "Python大规模数据的处理技巧"
date: 2017-06-07 13:43:01 +0800
comments: true
categories: Python
---
<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. 问题一：大数据量的csv读入到内存中</a>
<ul>
<li><a href="#sec-1-1">1.1. 问题分析：</a></li>
<li><a href="#sec-1-2">1.2. 应对思路：</a></li>
<li><a href="#sec-1-3">1.3. 简易示例：</a></li>
</ul>
</li>
<li><a href="#sec-2">2. 问题二：如何高效读取csv文件成python内部的list结构</a>
<ul>
<li><a href="#sec-2-1">2.1. 问题分析：</a></li>
<li><a href="#sec-2-2">2.2. 应对思路：</a></li>
<li><a href="#sec-2-3">2.3. 简易示例：</a></li>
</ul>
</li>
<li><a href="#sec-3">3. 问题三：数据结构之间合并</a>
<ul>
<li><a href="#sec-3-1">3.1. 问题分析：</a></li>
<li><a href="#sec-3-2">3.2. 应对思路：</a></li>
<li><a href="#sec-3-3">3.3. 简易示例：</a></li>
</ul>
</li>
</ul>
</div>
</div>

目前在数据分析和挖掘领域内，最为热门的莫过于Python和R了，不过这两门语言一直因为不好处理大规模的数据而被人们调侃，
同时，hadoop和spark也因此应运而生。然而，其实Python在大规模的数据处理上也并非像传言所说的那么慢。甚者，其中也蕴含了
挺多的技巧让我们能够利用Python对大规模的数据进行分析计算。

下面就Python操作大规模数据时可能会遇到的问题，给出一些个人的见解。

# 问题一：大数据量的csv读入到内存中<a id="sec-1" name="sec-1"></a>

## 问题分析：<a id="sec-1-1" name="sec-1-1"></a>

当一个csv文件的数据量十分大时，例如，一个电商站点的一个月的流水帐单或交易记录，其中可能有高达几千万条至上亿条
记录文本。这样的文件对于一般性能的计算机来说，若是全部数据一次读入内存的存储就非常吃力了，甚至有崩溃的可能。

## 应对思路：<a id="sec-1-2" name="sec-1-2"></a>

这时一个比较明智的方法就是将这些数据全部读入数据库之中，或者是根据我们的实际数据使用情况将大文件拆分成小块，然后
再按块读入。

## 简易示例：<a id="sec-1-3" name="sec-1-3"></a>
```python
    #使用pandas包的最简示例
     chunker = pd.read_csv(PATH_LOAD, chunksize = CHUNK_SIZE)
    
    #按列按需读取
      columns = ("date_time",  "user_id")
      chunks_train = pd.read_csv(filename, usecols = columns, chunksize = 100000)
    
    #分块分行读取
    for rawPiece in chunker_rawData:
      current_chunk_size = len(rawPiece.index)   #rawPiece 是dataframe
      for i in range(current_chunk_size ):
        timeFlag = timeShape(rawPiece.ix[i])   #获取第i行的数据
```
# 问题二：如何高效读取csv文件成python内部的list结构<a id="sec-2" name="sec-2"></a>

## 问题分析：<a id="sec-2-1" name="sec-2-1"></a>

当仅仅需要对外部大规模的csv做一些的简单的求和，求平均值，等简单的统计描述性分析时，使用pandas包显然是不明智的，因为pd
的读取中包含了各种抽象的转换操作，一但数据规模较大，性能是十分低下的。

## 应对思路：<a id="sec-2-2" name="sec-2-2"></a>

这时应该直接采用python原生的IO读写操作，节省那些多此一举的转换操作。

## 简易示例：<a id="sec-2-3" name="sec-2-3"></a>
```python
    #使用pandas包的方法，转换操作多，效率低下。
        userList = []
        content = pd.read_csv(filename)
        for i in range(len(content)):
            line = content.ix[i]['id']
            userList.append(line)
    
    #直接使用原生操作读取外部数据，效率较高，但数据操作不及pandas方便
        userList = []
        f = open(filename)
        content = f.readlines()
        for line in content:
            line = line.replace('\n', '').split(',')
            userList.append(line)
```
# 问题三：数据结构之间合并<a id="sec-3" name="sec-3"></a>

## 问题分析：<a id="sec-3-1" name="sec-3-1"></a>

当你要对一组数据特征进行建模时，就要用到数据结构的合并功能了。
然而，对于大规模数据来说，任何的操作和合并如何采用的方法不当，其要浪费的时间都是十分致命的。

## 应对思路：<a id="sec-3-2" name="sec-3-2"></a>

纵向的合并使用list并不好，因为需要去拆解list的每一个行元素，并用extend去拓展每一行的纵向元素
最好使用dataframe中的concat函数：c = pd.concat([a, b], axis = 1)，当axis=0时表示合并行（以行为轴）

## 简易示例：<a id="sec-3-3" name="sec-3-3"></a>
```python
    inx1 = DataFrame(np.random.randn(nSample_neg), columns = ['randVal'])
    inx2 = DataFrame(range(nSample_neg), columns = ['inxVal'])
    inx = pd.concat([inx1, inx2], axis = 1)
```
