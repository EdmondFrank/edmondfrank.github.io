---
layout: post
title: "标准化configure文件的生成"
date: 2016-06-11 20:16:12 +0800
comments: true
categories: Linux杂谈
---
标准化configure文件的生成
=========================                 
作为一个Linux系统下的开发者,都会或多或少地使用make这个命令来编译自己的程序，既然要用到make命令的话，一个简易的Makefile文件是必不可少的。平时大家自己编译时，如果要使用make命令编译的话，应该都是自己手写一个简单实用的Makefile文件。但一般软件需要发布的时候，由于自己手写的Makefile文件，不符合GNU的标准，且没有考虑各种诸多因素，导致程序在编译时也会出现各种问题。
因此为了解决如何生成符合CNU标准的configure和Makefile文件，我们可以使用Linux中autotools系统的工具来进行自动生成configure脚本来创建Makefile文件。

下面我以一个简单的HelloWorld程序作为例子：
一、建立项目文件夹
------------------
```
$ mkdir /tmp/hello
$ cd /tmp/hello
```
二、编写源文件(博主此处以C++程序为例）
-------------------------------------
```
$ vim hello.cpp
(此处大家可以自己喜欢的编辑器编写）
```

```C++
//hello.cpp
#include<iostream>
using namespace std;
int main (int argc,char** argv)
{
     cout<<"Hello World!"<<endl;
     return 0;
}
```
保存并退出后，当前目录下就有了一个hello.cpp的程序源文件了。
三、生成初步configure文件
-------------------------

```
$ autoscan
```

大家用man来查看autoscan这个命令就会知道，这个命令使用扫描当前源代码目录并生成configure.in模板的，这个命令的输出文件是configure.scan,我们只要将它重命名为configure.in再编辑其中的内容即可。
```
$ mv configure.scan configure.in
$ vim configure.in
```

**注："#"在脚本语言或linux的大部分配置文件中代表注释，与C&C++的“//”意义相同**
现在将configure.in修改下一下内容

```
# -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.
AC_PREREQ([2.63])
AC_INIT(hello.cpp,1.0)  #此项代表源代码的路径，1.0为版本号（可省略）
AM_INIT_AUTOMAKE(hello,1.0)#此项为必需项，代表生成软件包的名字和版本号
#AC_CONFIG_SRCDIR(hello.cpp) #为了简化操作，我们注释掉这两项内容
#AC_CONFIG_HEADERS([config.h])
# Checks for programs.
AC_PROG_CXX   #此项代表使用C++编译器
# Checks for libraries.
# Checks for header files.
# Checks for typedefs, structures, and compiler characteristics.
# Checks for library functions.
AC_OUTPUT(Makefile) #此项填写我们要输出的Makefile的名字
```


保存退出后。
我们再来执行aclocal和autoconf这两个命令，来分别生成aclocal.m4和configure文件
```
$ aclocal && autoconf
```
其中生成的两个文件

**aclocal.m4**: 是由aclocal这个perl脚本程序自动生成的，而aclocal的定义是："aclocal - create aclocal.m4 by scanning configure.ac"

**configure**:则是由autoconf根据GNU m4宏处理器处理aclocal.m4文件，生成的一个脚本。


**所以，以上命令的顺序不可颠倒。**

四、手建一个Makefile.am文件
---------------------------

```
$ vim Makefile.am
```

Makefile.am文件中主要包括三个项：

###1.AUTOMAKE_OPTIONS：
这个是automake 的选项。在执行automake 时，它会检查目录下是否存在标准GNU 软件包中应具备的各种文件，例如AUTHORS 、ChangeLog 、NEWS 等文件。我们将其设置成foreign 时，automake 会改用一般软件包的标准来检查。除此之外，可选的条目还有如下所示：

 - --cygnus
        assume program is part of Cygnus-style tree
 - --foreign
           set strictness to foreign
 - --gnits
              set strictness to gnits
 - --gnu
              set strictness to gnu

 
###2.bin_PROGRAMS:
这个是指定我们所要产生的可执行文件的文件名。如果你要产生多个可执行文件，那么在各个名字间用空格隔开。

###3.hello_SOURCES:
这个是指定产生“hello” 时所需要的源代码。如果它用到了多个源文件，那么请使用空格符号将它们隔开。

*eg*:
hello_SOURCES=test.cpp test.h test1.cpp test2.h

```
//编辑Makefile.am文件内容如下：
AUTOMAKE_OPTIONS=foreign
bin_PROGRAMS=hello
hello_SOURCES=hello.cpp
```

五、执行automake
----------------
```
$ automake --add-missing 
（或 automake -a，命令意义相同）
```
其中以上的命令选项

 - -a, --add-missing
           add missing standard files to package

代表自动添加软件包所需要的标准文件。

六、执行configure脚本来生成我们的目标Makefile文件
---------
```
$ ./configure
```

至此我们想要的标准的configure和Makefile文件都已经生成了。

最后只要执行make命令就可以编译我们的hello程序了
```
$ make

$ ./hello
```
输出如下:
> Hello World!

