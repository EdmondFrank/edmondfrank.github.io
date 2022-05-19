---
layout: post
title: "gdb的简单使用"
date: 2016-08-11 14:26:20 +0800
comments: true
categories: Linux杂谈
---

##gdb的简单使用
###1.单步执行和跟踪函数调用
**测试源码**
```c
 #include <stdio.h>
 int add_range(int low,int high)
 {
    int i,sum;
    for (i=low;i<=high;i++)
    {
        sum = sum + i;
    }
    printf("sum = %d\n",sum);
    return sum;
 }
 int main()
 {
    int result[100];
    result[0]=add_range(1,10);
    result[1]=add_range(1,100);
    printf("result[0]=%d \n result[1]=%d \n",result[0],result[1]);
    return 0;
 }
```
在编译并运行以上代码后,我们会发现最后的输出结果并非如我们预料一样.
无论是result[0]或result[1]基本都是错误的结果,然而代码中的逻辑却没有任何的问题.
在这里心细的朋友可能已经发现问题的所在了.
但既然是练习gdb的使用,那么我们就加上 -g 选项,再重新编译一次代码以便生成的可执行文件可以用gdb进行调试.

> $ gcc -g main.c -o main
> $ gdb main

**注:-g选项的作用是在可执行文件中加入源代码的信息,比如可执行文件中第几条机器指令对应源代码的第几行,但并不是把整个源文件嵌入到可执行文件中,所以在调试时必须保证gdb能找到源文件**


**除此之外**,gdb提供一个类似shell的命令行环境,上面的 (gdb) 就是提示符,在这个提示符下输
入help可以查看命令的类别.

**help命令**
```
(gdb) help
List of classes of commands:

aliases -- Aliases of other commands
breakpoints -- Making program stop at certain points
data -- Examining data
files -- Specifying and examining files
internals -- Maintenance commands
obscure -- Obscure features
running -- Running the program
stack -- Examining the stack
status -- Status inquiries
support -- Support facilities
tracepoints -- Tracing of program execution without stopping the program
user-defined -- User-defined commands

Type "help" followed by a class name for a list of commands in that class.
Type "help all" for the list of all commands.
Type "help" followed by command name for full documentation.
Type "apropos word" to search for commands related to "word".
Command name abbreviations are allowed if unambiguous.
(gdb) 
```

**list命令**
list命令用于列出代码或指定函数,列出的代码行的起始位置为上一次列出的代码的结尾,
第一次使用时,默认从第一行开始.每次默认列出十行代码.list命令也可简写成l.
要是要列出指定函数的代码,直接在list后面加函数名即可.
> eg: list add_range

且可以通过 show listsize  和 show listsize NUM(NUM为整数)来查询和修改每次最大列出代码行数.

**在什么都不输入的情况下敲击回车,gdb默认会执行上一条命令**

**quit命令**
退出gdb环境

**start命令**
开始执行程序,start命令默认停在main函数变量定义后的第一条语句,并等待下一次命令.

**next命令(简写n)**
单步步过

**step命令(简写s)**
单步步入

**backtrace命令(简写bt)**
查看函数调用栈帧

**info命令(简写i)**
查询相关信息
> eg:查询add_range函数局部变量的值,i locals

**print命令(简写p)**
打印指定变量值

**finish命令**
执行到返回

介绍完以上几个简单的命令之后再返回到测试代码的调试之中.
```
(gdb) start
Temporary breakpoint 1 at 0x80484c7: file ./gdbtest2.c, line 13.
Starting program: /home/ef/c/gdbtest2 

Temporary breakpoint 1, main () at ./gdbtest2.c:13
13	{
(gdb) n
15	    result[0]=add_range(1,10);
(gdb) s
add_range (low=1, high=10) at ./gdbtest2.c:5
5	    for (i=low;i<=high;i++)
(gdb) bt
#0  add_range (low=1, high=10) at ./gdbtest2.c:5
#1  0x080484de in main () at ./gdbtest2.c:15
(gdb) i locals
i = -1208074951
sum = -1208114904
```
在以上最后两行输出我们可以明白,程序之所以没有输出我们预期的结果,
其根本原因就是因为局部变量i和sum没有进行初始化.虽然变量i会在for循环之前进行重新赋值,
但作为累积变量的sum由于初始值不是0,导致最后的累加结果错误.

找出错误的所在后,我们即可以退出gdb,修改源码再重新进行调试;或者直接通过gdb的命令
> set var sum = 0

修改局部变量sum的值,然后再寻找下一处Bug.

###2.断点的使用
**测试代码**
```c
 #include <stdio.h>
 int main(void)
 {
  int sum = 0, i = 0;
  char input[5];
  while (1) {
  scanf("%s", input);
  for (i = 0; input[i] != '\0'; i++)
  sum = sum*10 + input[i] - '0';
  printf("input=%d\n", sum);
  }
  return 0;
 }
```
编译以上代码并运行
```
$ gcc main.c -g -o main
$ ./main
123
input=123
234
input=123234

```
我们发现程序第一次运行是对的,但第二次却又出乎了我们意料.
于是我们也把这个程序也放入gdb中调试.

**display命令**
display命令可以使得每次停下来的时候都显示执行变量的值,亦称变量跟踪.

**undisplay命令**
取消之前设置的变量跟踪

**break命令(简称b)**
在指定位置设定断点,参数可以是指定行也可以是某个函数

**i breakpoints**
输出目前已有断点的信息

**enable breakpoints NUM(NUM为断点号)**
启用指定断点(NUM为空时,默认为全部断点)

**disable breakpoints NUM(NUM为断点号)**
禁用指定断点(NUM为空时,默认为全部断点)

**delete breakpoints NUM(NUM为断点号)**
删除指定断点(NUM为空时,默认为全部断点)

**continue命令(简称c)**
继续执行,注意,continue命令是连续运行而非单步执行.

介绍完命令后我们又再次回到调试中,首先我们执行完start命令后,使用
> b 9

在sum的赋值处下一个断点,并用命令
> display sum

来时刻监视变量sum的变化<br/>
这时我们发现在我们输入完"123"后,再次想输入"456"时,gdb显示
"sum = 123" ,这样我们便发现错误所在,即我们并没有在新的循环中清空sum的先值

```
(gdb) start
Temporary breakpoint 1 at 0x804848c: file ./gdbtest3.c, line 3.
Starting program: /home/ef/c/gdbtest3 

Temporary breakpoint 1, main () at ./gdbtest3.c:3
3	{
(gdb) list
1	#include <stdio.h>
2	int main(void)
3	{
4	int sum = 0, i = 0;
5	char input[5];
6	while (1) {
7	scanf("%s", input);
8	for (i = 0; input[i] != '\0'; i++)
9	sum = sum*10 + input[i] - '0';
10	printf("input=%d\n", sum);
(gdb) b 9
Breakpoint 2 at 0x80484c2: file ./gdbtest3.c, line 9.
(gdb) display sum
1: sum = 134514011
(gdb) c
Continuing.
123

Breakpoint 2, main () at ./gdbtest3.c:9
9	sum = sum*10 + input[i] - '0';
1: sum = 0
(gdb) c
Continuing.

Breakpoint 2, main () at ./gdbtest3.c:9
9	sum = sum*10 + input[i] - '0';
1: sum = 1
(gdb) c
Continuing.

Breakpoint 2, main () at ./gdbtest3.c:9
9	sum = sum*10 + input[i] - '0';
1: sum = 12
(gdb) c
Continuing.
input=123
456

Breakpoint 2, main () at ./gdbtest3.c:9
9	sum = sum*10 + input[i] - '0';
1: sum = 123
(gdb) 

```


