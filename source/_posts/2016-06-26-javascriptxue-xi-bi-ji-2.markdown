---
layout: post
title: "JavaScript学习笔记2"
date: 2016-06-26 14:52:05 +0800
comments: true
categories: 学习心得
---
#JavaScript学习笔记2
##一.流程控制
###一般选择-if 结构
```javascript
if(表达式1){
执行1
} else if(表达式2) {
执行2
} else {
执行3
}
```
###多重选择-switch 结构
```javascript
switch(表达式){
case 条件1:
  执行1
  break;
case 条件2:
  执行2
  break;
case 条件n:
  执行n
  break;
default:
  ...;
}
```
**(注:switch所依赖的参数必须赋初始值,值与每个case值匹配,满足执行改case后的所以语句,并用break语句来阻止运行下一个case.)**

###循环语句-for
```javascript
for(初始化变量;循环条件;循环迭代)
{
  循环语句;
}
```

###循环语句-while
```javascript
while(判断条件)
{
  循环语句;
}
```
**(break语句可用在循环语句中,跳出循环.
与其类似的有continue语句,用于跳过本次循环,而整个循环体继续执行.)**

##二.函数(function)
```javascript
function fun(){
//函数体
}
fun();//调用函数

//带参函数
function fun2(arg1,arg2){
//函数体
}

//带参数及返回值的函数
function add(a,b){
  return a+b;
}
```
###函数的this
函数体中的this表示当前的方法属于谁.

eg:
```javascript
function add(a,b)
{
    return this+(a+b);
}
console.log(add(3,6));
```
输出结果:
> [object global]9

###函数的call
函数的call方法可以改变函数的this的指向.

eg:
```javascript
function add(a,b)
{
    return this+(a+b);
}
console.log(add.call(2,3,6));
```
输出结果:
> 11

**(输出结果表明:this也是一个数字,所以叫号作为运算符而非连接符处理.)**

###函数的apply
函数的apply方法也可以改变函数的this的指向,但不同的是,apply是将原参数作为一个数组进行传递.

eg:
```javascript
ar arr = [5, 6, 3, 2, 9, 44, 6, 3, 61, 22];
console.log(Math.min.apply(null,arr));
```
输出结果:
> 2

