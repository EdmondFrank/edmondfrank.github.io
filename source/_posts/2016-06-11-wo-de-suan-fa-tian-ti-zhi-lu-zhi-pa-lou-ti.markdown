---
layout: post
title: "我的算法天梯之路之-爬楼梯"
date: 2016-06-11 11:17:42 +0800
comments: true
categories: 算法笔记
---
我的算法天梯之路之-爬楼梯
=========================
最近在计蒜客的算法练习题目之中看到了这样一道比较有意思的题目.

原题如下所示:

>假设你现在正在爬楼梯，楼梯有n级。每次你只能爬1级或者2级，那么你有多少种方法爬到楼梯的顶部？
>格式：
>   第一行输入一个数n(n<=50)，代表楼梯的级数。
>   接下来一行输出你的方法总数。
>样例输入
>5
>样例输出
>8

从题面上看,可以发现这是一道比较典型的递归算法题目.
既然已知阶梯数为N,而最大跨步数为2,那么我们可以先假设有3级阶梯,那么所有的走法就有:

>111 12 21

这样子分析完之后,我们可以发现,我们总共要处理的情况有两种:

1. 剩余阶梯数大于最大步数
2. 剩余阶梯数小于最大步数

而判断走法是否可行的条件就是:**最后的当前阶梯数是否为0**

因此我们可以写出如下的递归函数:

```ruby
def calcSumofMethod(ladder)
  sum = 0
  if ladder == 0 || ladder == 1
    return 1
  else
    [1,2].each{|i|sum+= calcSumofMethod(ladder-i)}
  end
  return sum
end
ladder = gets.to_i
puts calcSumofMethod(ladder)
```
  但是由于计蒜客的算法问题有1000ms运行时间的限制,因此在此处用Ruby的递归算法来实现并非是正解(可能是Ruby的性能问题导致运行超时,博主没试过用C进行递归.有兴趣的人可以试一试.)

  我们再自习观察前10级阶梯数算出的结果会发现:随着阶梯数N的增长,走法的总数符合斐波那契数列规律增长, f(N)=f(N-1)+f(N-2)
  利用这个规律我们可以采取以下循环方法实现:

```ruby
def calcArray
  result=Array.new
  result[0]=1
  result[1]=2
  (2..49).each{|i|result[i]=result[i-1]+result[i-2]}
  return result
end
ladder = gets.to_i
puts calcArray[ladder-1]
```


