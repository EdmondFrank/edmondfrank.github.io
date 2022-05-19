---
layout: post
title: "Linux程序开发-命令行选项及参数"
date: 2016-08-09 16:16:57 +0800
comments: true
categories: Linux杂谈
---
##Linux程序开发-命令行选项及参数
###1.C语言中命令行参数的读取与输出
```c
 #include <stdio.h>
 int main(int argc,char* argv[])
 {
    int i;
    for(i=0;i<argc;i++)
    {
        printf("argv[%d] = %s\n",i,argv[i]);
    }
 }
```
**当命令行选项很多时,可以使用库函数获取**
```c
getopt      //短选项
getlongopt  //长选项
```
**函数声明**
```c
 #include <unistd.h>
 int getopt(int argc,char* const argv[],const char *optstring);
```
以下全局变量用于配合getopt函数
> 
extern char *optargr;<br/>
extern int optind;<br/>
extern int optopt;<br/>
extern int opterr;<br/>

###2.有关选项和参数的区别
eg:

-a : 选项,一般表示所有

-h host: 其中,h表示选项,而host代表了一个主机ip也就是选项h的参数

**具体参数的解析,可以参考getopt的第三个参数的使用约定**

###3.getopt的执行过程
当getopt每成功执行一次
```c
extern char *optarg; //指向下一个要扫描的参数
extern int optind;   //索引修改为下一个要处理的指针的下标
extern int optopt;   //用于存储可能的错误
extern int opterr;   //如果opterr == 0;则不将错误输出到标准错误输出设备
```
###4.getopt使用实例
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
int main(int argc,char** argv)
{
    int result;
    opterr = 0;
    while ((result = getopt(argc,argv,"ab:c::"))!= -1)
    {
        switch(result)
        {
        case 'a':
            printf("option=a,optopt=%c,optarg=%s\n",optopt,optarg);
            break;
        case 'b':
            printf("option=b,optopt=%c,optarg=%s\n",optopt,optarg);
            break;
        case 'c':
            printf("option=c,optopt=%c,optarg=%s\n",optopt,optarg);
            break;
        case '?':
            printf("result=?,optopt=%c,optarg=%s\n",optopt,optarg);
        default:
            printf("default,result=%c\n",result);
        }
        printf("argv[%d]=%s\n",optind,argv[optind]);
    }
    printf("result=-1,optind=%d\n",optind);
    return 0;
}
```
> 输入示例
./first -a -b host -cShell


```
输出结果
option=a,optopt=,optarg=(null)
argv[2]=-b
option=b,optopt=,optarg=host
argv[4]=-cShell
option=c,optopt=,optarg=Shell
argv[5]=(null)
result=-1,optind=5
```
###5.长选项判断函数getopt_long的声明
```c
#include <unistd.h>

int getopt(int argc, char * const argv[],
                  const char *optstring);

extern char *optarg;
extern int optind, opterr, optopt;

#include <getopt.h>

int getopt_long(int argc, 
                  char * const argv[],
                  const char *optstring,          //当前支持的短选项列表
                  const struct option *longopts,  //长选项信息
                  int *longindex);                //索引

struct option {
               const char *name;         //选项名
               int         has_arg;      //是否有参数
               int        *flag;         //标志
               int         val;          //返回值
                                };
int getopt_long_only(int argc, char * const argv[],
                  const char *optstring,
                  const struct option *longopts, int *longindex);
```

**第四个参数的使用示例:**
```c
struct optino my_option = {
   {"help",0,NULL,'h'},
   {"output",1,NULL,'o'}
}
```
除了以上区别外,长选项与短选项的使用方法类似.

