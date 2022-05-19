---
layout: post
title: "图片截获工具driftnet"
date: 2016-06-12 16:37:33 +0800
comments: true
categories: 安全渗透
---
图片截获工具driftnet
====================

driftnet是一款简单强大的图片捕获工具,可以在网络数据包之中实时判断和离线捕获图片,该工具内置于kali工具包之中.在正常的Linux系统系统也可以在[Github](https://github.com/deiv/driftnet)上下载源码编译使用.

### **语法**:driftnet [options] [fifter code]

### **常用选项**:

 - -h:显示帮助信息
 - -b:捕获到新图片时,发出蜂鸣声.
 - -i interface:选择监听接口(默认为全部接口,*此选项为关键选项*.)
 - -f file:读取一个指定pcap数据包中的图片
 - -p:不让所监听的接口使用混杂模式
 - -a:后台模式,即截取的图片自动保存至临时目录中,不显示在屏幕上.
 - -m number:指定保存图片的最大数目
 - -d directory:指定保存图片的路径
 - -x prefix:指定保存图片的前缀名 
 - -s:尝试解读网络数据包中的音频流或视频流信息,现只支持MPEG格式
 - -S:类似-s

> 使用示例:

Wlan0监听:
```
driftnet -i wlan0
```
读取指定pcap数据包:
```
driftnet -f /home/tmp/tmp.pcapng -a -d /tmp/
```
### 常用组合:

> ettercap + driftnet

> arproof + driftnet

(有关组合的使用以及dirftnet的其他用法,博主会在后期的博客,陆续更新,敬请期待.)

