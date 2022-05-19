---
layout: post
title: "JavaScript学习笔记1"
date: 2016-06-24 21:41:53 +0800
comments: true
categories: 学习心得
---
#JavaScript学习笔记1
##一.第一个程序:Hello World的输出
- 控制台下的输出:
> console.log('Hello World!');

- 消息框模式输出:
> alert('Hello World!');

　{% img /images/alert.png %}

##二.变量
- 必须以字母、下划线或美元符号开头，后面可以跟字母、下划线、美元符号和数字.
```javascript
myvar; 
_myvar;
$myvar; //以上均为正确的变量名
1myvar; //错误,不能用数字开头
%myvar; //开头不能用(_,$)以外的特殊字符
var; //错误,不能使用保留字和关键字
```
###变量的声明
> var 变量名;

eg: var mynum = 1;

同时声明多个变量时使用","分割即可.

eg: var myvar,myvar2,myvar3;

**(注:在JavaScript中,未赋值的变量,会自动被使用"undefined"值来初始化)**

##三.数据类型
JavaScript中有5种基本数据类型,分别是:
1. 字符串
2. 数字
3. 布尔值
4. 数组
5. 对象
```javascript
var mychar1 = "双引号包起来的字符串"; // 这是字符串
var mychar2 = '单引号包起来的字符串'; // 这也是字符串
var mynum1 = 6; // 这是数字6
var mynum3 = 123e5; // 这是使用科学（指数）计数法来书写的12300000
var mynum4 = 123e-5; // 这是0.00123
var mybool = true; // 这是布尔值
var myarr = [1, 2, 3]; // 这是数组
var myobject = {"p": "Hello"}; // 这是对象
```

##四.表达式及运算符
与Ruby和Python类似.
**(注:完整语句后面记得加上";")**

##五.数组
数组变量的声明:
> var arr = ['a','b','c'];

**(注:任意一种类型的数据,都可以放进数组里,且数组支持嵌套)**
eg:
```javascript
var arr = [{a:1}, [1, 2, 3], function(){ return true; }];
arr[0]; //对象
arr[1]; //数组
arr[2]; //函数
```
- length属性:返回数组成员的数量

eg:
```javascript
var arr = ['a', 'b'];
arr.length;    // => 2
```
**(注:length 属性是可写的。如果人为设置一个小于当前成员个数的值，该数组的成员会自动减少到 length 设置的长度.即,随意改写length值会造成数据丢失问题.)**
eg:
```javascript
var arr = ['a', 'b', 'c'];
arr.length;    // => 3

arr.length = 2;
arr;    // => ["a", "b"]

arr.length = 0;//快速清空数组
```
###增加新元素
```javascript
var myarr = [1,2,3];
myarr[3] = 4;
console.log(myarr); // =>[1,2,3,4]
//此处特性与Ruby数组相似.
```

##六.对象
JavaScript的对象，是指带有属性和方法的数据类型，一般由若干个“键值对”（key-value）构成

- 键名
键名加不加引号都可以，前面的代码也可以写成这样：
> var o = {
    "p": "Hello"
};

**(注:键名如果不符合标识名的条件，也不是正整数，则必须加上引号)**

###生成方法
```javascript
var obj1 = {};
var obj2 = new Object();
var obj3 = Object.create(null);
```
###对象引用
如果不同的变量名指向同一个对象，那么它们都是这个对象的引用，也就是说指向同一个内存地址。修改其中一个变量的属性，会影响到其他所有变量(此处与C类似)

###内置对象
JavaScript常见的内置对象(built-in object)有: String, Number, Boolean,Date Object

有关更多JavaScript内置对象相关属性及方法可以参考[JavaScript|MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects)


