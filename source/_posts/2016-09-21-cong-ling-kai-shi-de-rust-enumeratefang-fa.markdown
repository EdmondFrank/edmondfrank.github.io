---
layout: post
title: "从零开始的Rust-Enumerate方法"
date: 2016-09-21 19:37:14 +0800
comments: true
categories: 学习心得
---
##从零开始的Rust学习之旅-Enumerate方法
Enumerate方法
当你需要记录你已经循环了多少次了的时候,你可以使用 .enumerate()函数。
###对范围(On ranges):

```rust
for(i,j) in (5..10).enumerate()	
{
	println!("i	=	{}	and	j	=	{}",	i,	j);
}
```
>
输出:<br/>
i = 0 and j = 5 <br/>
i = 1 and j = 6 <br/>
i = 2 and j = 7 <br/>
i = 3 and j = 8 <br/>
i = 4 and j = 9 <br/>
**别忘了在范围外面加上括号**

###对迭代器(On iterators):
```rust
let lines = "hello\nworld".lines();
for (linenumber, line) in lines.enumerate() {
	println!("{}:{}", linenumber, line);
}
```
>
输出:<br/>
0:hello <br/>
1:world <br/>
