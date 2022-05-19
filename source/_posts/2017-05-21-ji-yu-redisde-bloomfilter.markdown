---
layout: post
title: "基于Redis的Bloomfilter"
date: 2017-05-21 11:05:15 +0800
comments: true
categories: 算法笔记
---
<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. 背景</a></li>
<li><a href="#sec-2">2. 前言及原理简介</a>
<ul>
<li><a href="#sec-2-1">2.1. 传统弊端</a></li>
<li><a href="#sec-2-2">2.2. bloomfilter</a>
<ul>
<li><a href="#sec-2-2-1">2.2.1. 优点：</a></li>
<li><a href="#sec-2-2-2">2.2.2. 缺点：</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#sec-3">3. 简单图示</a></li>
<li><a href="#sec-4">4. 算法实现</a></li>
</ul>
</div>
</div>


# 背景<a id="sec-1" name="sec-1"></a>

url去重一直是大型分布式爬虫的主题，在一般规模比较大的的情景，去重需要考虑到两个点：

1.  去重的数据量
2.  去重的速度

并且，在一般情况下为了尽量降低去重对爬虫效率的影响一般选择在内存中去重。

-   小数据：直接使用语言的逻辑判断及数据结构去重，如python的set，ruby的Set
-   持续化去重： redis的set
-   中型数据量去重：加密算法压缩url及长字符串在混合使用其他方法去重
-   大型数据去重：使用bloomfilter（布隆过滤器）桉内存位去重

# 前言及原理简介<a id="sec-2" name="sec-2"></a>

## 传统弊端<a id="sec-2-1" name="sec-2-1"></a>

按照以往惯例来说，我们一般判断一个元素是否在一个集合内的通常做法是：先将所有元素保存下来，
然后通过比较判断它是否在集合之中。但是，这样的常规判断方法有一个很大的弊端就是，随着集合内的
元素个数变大，我们需要的空间和时间都呈线性增长，检索速度也越来越慢。

## bloomfilter<a id="sec-2-2" name="sec-2-2"></a>

而Bloom filter 采用的是哈希函数的方法，将一个元素表示为一个点并将他映射到一个长度为m的
阵列上。在检索时如果在阵列上发现对应的映射点为1时，那么这个元素在集合内，反之则不在集合内。

### 优点：<a id="sec-2-2-1" name="sec-2-2-1"></a>

Bloom filter 优点在于它的插入和查询时间都是常数，另外它查询的元素不保存元素本身，具有良好的
安全性。

### 缺点：<a id="sec-2-2-2" name="sec-2-2-2"></a>

最明显的一点是，当插入元素越多，查询元素被错判成“在集合内”的概率就越大。针对这个问题常用的
解决方法有：使用k个哈希函数来对应映射k个点，如果所对应的所有点都是1的话。那么元素在集合内，
如果任何一个有0的话，则元素不在集合内。另外，Bloom filter也不能删除一个元素，因为多个元素的哈希
的结果可能在bloom filter的阵列中都是占用同一个位。如果删除了一个比特位，可能会影响多个元素的检测。

# 简单图示<a id="sec-3" name="sec-3"></a>

实现我们设我们的哈希函数有两个。

开始时集合内没有元素：

![img](http://e.hiphotos.baidu.com/baike/s%3D250/sign=0fd8813a78f0f736dcfe4b043a54b382/7af40ad162d9f2d3f39093aaa9ec8a136227ccf6.jpg)

当来了一个元素a时，进行哈希计算再判断，当计算出对应的比特位上为0时，即a不在集合内，添加a的哈希值进去。

![img](http://g.hiphotos.baidu.com/baike/s%3D250/sign=7debb7818c1001e94a3c130a880e7b06/9d82d158ccbf6c815e2dd280bd3eb13533fa4044.jpg)

之后的元素，要判断是不是在集合内，也是同 a 一样的方法，只有对元素哈希后对应位置上都是 1 才认为这个元素在集合内

![img](http://c.hiphotos.baidu.com/baike/s%3D250/sign=71f00574a18b87d65442ac1a37092860/d6ca7bcb0a46f21f7ce5e916f6246b600d33aea6.jpg)

随着元素的插入，Bloom filter 中修改的值变多，出现误判的几率也随之变大，当新来一个元素时，
满足其在集合内的条件，即所有对应位都是1，这样就可能有两种情况，
一是这个元素就在集合内，没有发生误判；还有一种情况就是发生误判，出现了哈希碰撞，这个元素本不在集合内。

![img](http://a.hiphotos.baidu.com/baike/s%3D250/sign=7dd68ea2912397ddd2799f016983b216/2cf5e0fe9925bc31448eb6dd5edf8db1ca1370a7.jpg)

# 算法实现<a id="sec-4" name="sec-4"></a>
```python
    # encoding=utf-8
    
    import redis
    from hashlib import md5
    
    class SimpleHash(object):
        def __init__(self, cap, seed):
            self.cap = cap
            self.seed = seed
    
        def hash(self, value):
            ret = 0
            for i in range(len(value)):
                ret += self.seed * ret + ord(value[i])
            return (self.cap - 1) & ret
    
    
    class BloomFilter(object):
        def __init__(self, host='localhost', port=6379, db=0, blockNum=1, key='bloomfilter'):
            """
            :param host: the host of Redis
            :param port: the port of Redis
            :param db: witch db in Redis
            :param blockNum: one blockNum for about 90,000,000; if you have more strings for filtering, increase it.
            :param key: the key's name in Redis
            """
            self.server = redis.Redis(host=host, port=port, db=db)
            self.bit_size = 1 << 31  # Redis的String类型最大容量为512M，现使用256M
            self.seeds = [5, 7, 11, 13, 31, 37, 61]
            self.key = key
            self.blockNum = blockNum
            self.hashfunc = []
            for seed in self.seeds:
                self.hashfunc.append(SimpleHash(self.bit_size, seed))
    
        def isContains(self, str_input):
            if not str_input:
                return False
            m5 = md5()
            m5.update(str_input)
            str_input = m5.hexdigest()
            ret = True
            name = self.key + str(int(str_input[0:2], 16) % self.blockNum)
            for f in self.hashfunc:
                loc = f.hash(str_input)
                ret = ret & self.server.getbit(name, loc)
            return ret
    
        def insert(self, str_input):
            m5 = md5()
            m5.update(str_input)
            str_input = m5.hexdigest()
            name = self.key + str(int(str_input[0:2], 16) % self.blockNum)
            for f in self.hashfunc:
                loc = f.hash(str_input)
                self.server.setbit(name, loc, 1)
    
    
    if __name__ == '__main__':
    """ 第一次运行时会显示 not exists!，之后再运行会显示 exists! """
        bf = BloomFilter()
        if bf.isContains('http://www.baidu.com'):   # 判断字符串是否存在
            print 'exists!'
        else:
            print 'not exists!'
            bf.insert('http://www.baidu.com')
```
