---
layout: post
title: "Ubuntu下Web服务器Apache的安装及配置"
date: 2016-06-19 16:34:50 +0800
comments: true
categories: Linux杂谈
---

#Ubuntu下Web服务器Apache的安装及配置
## 一.安装

###1. 终端下安装Apache:
```
$ sudo apt-get install apache2
```
回车后等待安装完成即可.

###2. 源码编译安装
> 1.前往 [Apache官方网站](http://httpd.apache.org) 下载并解压源码包.
```
$ tar zxvf httpd-2.4.20.tar.gz
$ cd httpd-2.4.20
```
> 2.配置安装路径
```
$ ./configure --prefix=/usr/local/apache
```
> 3.编译并安装
```
$ make
$ make install
```
> 4.启动服务器
```
$ cd /usr/local/apache/bin
$ ./apachectrl start
```
##二.配置
(1)默认页面的路径
Apache安装完成后,默认的网站根目录是"/var/www/html"
```
$ ls /var/www/html
=> index.html //该文件即为apache的默认页面
```
(2)Apache配置文件的路径
apache2.conf的路径:
```
$ ls /etc/apache2
```
000-default.conf的路径:
```
$ ls /etc/apache2/sites-available
```
(3)修改网站根目录
有关网页根目录的设置以上讲述的两个配置文件的设置有关

1)
```
$ sudo vim /etc/apache2/apache2.conf
-->找到"<Directory /var/www/>"的位置-->更改"/var/www/"为新的根目录就可以了
```
2)
```
$ sudo vim /etc/apache2/sites-available/000-default.conf
-->找到"DocumentRoot /var/www/html"的位置-->更改"/var/www/html"为新的根目录就可以了
```
##三.重启Apache使设置生效
```
$ sudo /etc/init.d/apache2 restart
```
重启完毕后,往修改后的新目录添加你的自定义index.html主页文件,再在浏览器的地址栏键入127.0.0.1就可以查看apache服务器的运行效果了.
