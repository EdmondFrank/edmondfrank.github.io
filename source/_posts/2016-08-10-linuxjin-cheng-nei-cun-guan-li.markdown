---
layout: post
title: "Linux进程内存管理"
date: 2016-08-10 11:24:22 +0800
comments: true
categories: Linux杂谈
---
##Linux进程内存管理

###1.程序的结构与进程结构
$ size test
>
text	   data	    bss	    dec	    hex	filename <br/>
181617	    432	  18844	 200893	  310bd	./book <br/>

**一个可执行程序包括三个部分** <br/>
代码段:存放指令,操作以及只读的常量<br/>
数据段:全局或者静态的已经初始化的变量<br/>
BSS段 :全局或者静态的未初始化的变量<br/>

###2.进程管理的过程
在执行程序时,系统首先在内核空间中创建一个进程,为这个进程申请一个PCB
(进程控制块task_struct),用于管理整个进程的所有资源,其中mm_struct成员用来管理与当前
进程相关的所有内存资源.
> 
(1)代码段,数据段,BSS段,直接从磁盘拷贝到当前内存空间,大小相等.<br/>
(2)动态的空间:堆,栈空间,mmap段(用于映射其他的库的相关信息)

```
$ cat /proc/7450/maps
08048000-08152000 r-xp 00000000 08:01 36175926   /bin/bash
08152000-08153000 r--p 00109000 08:01 36175926   /bin/bash
08153000-08158000 rw-p 0010a000 08:01 36175926   /bin/bash
08158000-0815d000 rw-p 00000000 00:00 0 
084cf000-08994000 rw-p 00000000 00:00 0          [heap]
b710d000-b7118000 r-xp 00000000 08:01 21496849   /lib/i386-linux-gnu/libnss_files-2.23.so
b7118000-b7119000 r--p 0000a000 08:01 21496849   /lib/i386-linux-gnu/libnss_files-2.23.so
b7119000-b711a000 rw-p 0000b000 08:01 21496849   /lib/i386-linux-gnu/libnss_files-2.23.so
b711a000-b7120000 rw-p 00000000 00:00 0 
b7120000-b712b000 r-xp 00000000 08:01 21496770   /lib/i386-linux-gnu/libnss_nis-2.23.so
b712b000-b712c000 r--p 0000a000 08:01 21496770   /lib/i386-linux-gnu/libnss_nis-2.23.so
b712c000-b712d000 rw-p 0000b000 08:01 21496770   /lib/i386-linux-gnu/libnss_nis-2.23.so
b7151000-b7158000 r--s 00000000 08:01 48245384   /usr/lib/i386-linux-gnu/gconv/gconv-modules.cache
b7158000-b730b000 r--p 002d7000 08:01 48234512   /usr/lib/locale/locale-archive
b730b000-b750b000 r--p 00000000 08:01 48234512   /usr/lib/locale/locale-archive
b750b000-b750c000 rw-p 00000000 00:00 0 
b750c000-b76bb000 r-xp 00000000 08:01 21497064   /lib/i386-linux-gnu/libc-2.23.so
b76bb000-b76bc000 ---p 001af000 08:01 21497064   /lib/i386-linux-gnu/libc-2.23.so
b76bc000-b76be000 r--p 001af000 08:01 21497064   /lib/i386-linux-gnu/libc-2.23.so
b76be000-b76bf000 rw-p 001b1000 08:01 21497064   /lib/i386-linux-gnu/libc-2.23.so
b76bf000-b76c2000 rw-p 00000000 00:00 0 
b76c2000-b76c5000 r-xp 00000000 08:01 21497066   /lib/i386-linux-gnu/libdl-2.23.so
b76c5000-b76c6000 r--p 00002000 08:01 21497066   /lib/i386-linux-gnu/libdl-2.23.so
b76c6000-b76c7000 rw-p 00003000 08:01 21497066   /lib/i386-linux-gnu/libdl-2.23.so
b76c7000-b76e7000 r-xp 00000000 08:01 21496816   /lib/i386-linux-gnu/libtinfo.so.5.9
b76e7000-b76e9000 r--p 0001f000 08:01 21496816   /lib/i386-linux-gnu/libtinfo.so.5.9
b76e9000-b76ea000 rw-p 00021000 08:01 21496816   /lib/i386-linux-gnu/libtinfo.so.5.9
b76ef000-b7706000 r-xp 00000000 08:01 21496768   /lib/i386-linux-gnu/libnsl-2.23.so
b7706000-b7707000 r--p 00016000 08:01 21496768   /lib/i386-linux-gnu/libnsl-2.23.so
b7707000-b7708000 rw-p 00017000 08:01 21496768   /lib/i386-linux-gnu/libnsl-2.23.so
b7708000-b770a000 rw-p 00000000 00:00 0 
b770a000-b7712000 r-xp 00000000 08:01 21496134   /lib/i386-linux-gnu/libnss_compat-2.23.so
b7712000-b7713000 r--p 00007000 08:01 21496134   /lib/i386-linux-gnu/libnss_compat-2.23.so
b7713000-b7714000 rw-p 00008000 08:01 21496134   /lib/i386-linux-gnu/libnss_compat-2.23.so
b7714000-b7715000 r--p 002d4000 08:01 48234512   /usr/lib/locale/locale-archive
b7715000-b7717000 rw-p 00000000 00:00 0 
b7717000-b7719000 r--p 00000000 00:00 0          [vvar]
b7719000-b771a000 r-xp 00000000 00:00 0          [vdso]
b771a000-b773c000 r-xp 00000000 08:01 21497056   /lib/i386-linux-gnu/ld-2.23.so
b773c000-b773d000 rw-p 00000000 00:00 0 
b773d000-b773e000 r--p 00022000 08:01 21497056   /lib/i386-linux-gnu/ld-2.23.so
b773e000-b773f000 rw-p 00023000 08:01 21497056   /lib/i386-linux-gnu/ld-2.23.so
bff73000-bff94000 rw-p 00000000 00:00 0          [stack]
```
**所示地址并不是真正的物理地址,而是虚拟地址.使用虚拟地址是出于对资源的保护**
对系统来说,内存资源是宝贵的,而一个程序执行并不需要立即将所有资源加载到内存,
而实际上可采用写时申请的方法
<br/>
####虚拟地址的优点:
1. 保护系统,避免用户程序非法访问内存造成内核崩溃.
2. 节约资源,采用映射方法,使用时全用缺页的方法申请物理空间,即提高了效率也节约物理空间

###3.进程地址空间的申请
32bit平台下,一个进程拥有4G的虚拟地址空间
> 
映射规则 <br/>
1. 代码段,数据段,BSS段,直接从磁盘拷贝至内存.在linux 32bit平台上起始地址一般为
0x08048000<br/>
2. 堆栈,动态变化.<br/>
3. mmap映射的文件(普通文件以及库),用户需自行调用mmap函数<br/>
4. 高地址1G空间:供内核映射处理,用户空间不能直接访问<br/>

**堆和栈的起始地址是随机产生的,其目的是避免产生安全漏洞,但是可以使用brk/sbrk函数指定在堆中申请空间的起始地址**

brk/sbrk函数声明
```c
       #include <unistd.h>

       int brk(void *addr);  //指定下次申请堆空间的起始地址

       void *sbrk(intptr_t increment);  //在当前地址位置后移increament字节,为0时返回当前地址
```

**代码示例:**
```c
 #include<stdlib.h>
 #include<stdio.h>
 int i = 10;
 int main(int argc,char *argv[])
 {
     brk(0x09e50000);
     printf("brk return:%p\n",sbrk(0));
     char *ptr = malloc(102400);
     printf("new malloc:%p \n",ptr);
     return 0;
 }
```
> 
输出结果 <br/>
brk return:0x9e50000 <br/>
new malloc:0x9e51010 <br/>

**strace工具可以追踪进程空间的加载**

###4.空间分配规则
1. 代码段,数据段,BSS段的地址在编译时已经固定.
2. BSS的结束段与堆的起始地址之间有一定间隙,间隙大小随机.
3. brk调整的仅仅是堆中申请空间的起始值
4. 系统默认认为每个进程分配的堆空间大小是固定的.使用sbrk(0)得到的是我们堆空间的结束值.
5. 真正编程中,很少使用brk/sbrk调整位置

###5.栈的说明
栈:从高地址向低地址增长,起始值也是随机的.主要存放局部变量,
新调用子函数时函数的参数以及返回值,由OS自动管理.
