---
layout: post
title: "我的算法天梯之路之-穷举搜索"
date: 2018-05-30 22:15:17 +0800
comments: true
categories: 算法笔记
---
<h2 id="穷举搜索的例子google方程式">穷举搜索的例子－Google方程式</h2>



<h3 id="问题描述">　问题描述</h3>

<p>有一个由字符组成的等式，WWWDOT-GOOGLE = DOTCOM，每个字符代表一个0-9之间的数字，WWWDOT、GOOGLE和DOTCOM都是合法的数字，不能以零开头。请找出一组字符与数字的对应关系，使得他们可以互相转换，并且替换之后能够满足等式。</p>



<h3 id="问题分析">问题分析</h3>

<p>从排列组合的角度来看，这道题是一道典型的排列组合问题，题目中一共出现了9个字母。</p>

<p>如果不考虑0开头的情况下，这样的组合应该有10x9x8x7x6x5x4x3x2=3628800种组合。</p>

<p>在这样的情况之下，计算机的穷举处理应该是毫无压力的。</p>



<h3 id="问题求解">问题求解</h3>

<p>首先为了能够表示这样一种可变的字符元素列表，我们需要自定义一种数据结构。首先我们知道这个自定义结构中应该包含有三个属性，分别是 字符本身，字符代表的数字以及是否为数字的最高位，因为最高位是不能为0的，所以在这里我们要区别对待。</p>



```c
typedef struct tagCharItem
{
	char c;
	int value;
	bool leading;
}CHAR_ITEM;
```

<p>接着我们初始化这个列表。=</p>


```c
CHAR_ITEM charItem[]={
{'W',-1,true},{'D',-1,true},{'O',-1,true},
{'T',-1,false},{'G',-1,true},{'L',-1,false},
{'E',-1,false},{'C',-1,false},{'M',-1,false}
};
```
<p>因为这是一个组合问题，那么两个字母就不能被指定为相同的数字，这样我们需要定义额外的占用标识。</p>

```c
typedef struct tagCharValue
{
	bool isused;
	int value;
}CHAR_VALUE;

//穷举算法实现如下
void SearchingResult(CHAR_ITEM ci[],CHAR_VALUE cv[],
int index,CharListReadyFuncPtr callback)
{
if(index == max_char_count)
{
	callback(ci);
	return;
}
for(int i = 0;i<max_number_count;++i)
{
	if(IsValueValid(ci[index],cv[i]))
	{
		cv[i].isused= true;
		ci[index].value = cv[i].value;
		SearchingResult(ci,cv,index+1,callback);
		cv[i].isused = false;
	}
}
}
```

<p>根据题目要求，W，G，D三者都不能为0，为了加快穷举速度可以对他们为0的情况进行剪枝。 <br>
IsValueValid是评估函数，在剪枝操作之后，callback的被调用次数可以减少约30%。</p>


```c
//callback代码实现
void OnCharListReady(CHAR_ITEM ci[])
{
	char * minuend = "WWWDOT";
	char * subtrahend = "GOOGLE";
	char * diff = "DOTCOM";
	int m = MakeIntegerValue(ci,minuend);
	int s = MakeIntegerValue(ci,subtrahend);
	int d = MakeIntegerValue(ci,diff);
	if((m-s)==d){
		std::cout << m << "-" <<s<< "=" << d << std::endl;
	}
}
```

<blockquote>
  <p>问题答案 <br>
  777589 - 188103 = 589486 <br>
  777589 - 188106 = 589483</p>
</blockquote>
