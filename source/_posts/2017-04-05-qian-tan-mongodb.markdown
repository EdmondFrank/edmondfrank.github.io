---
layout: post
title: "浅谈MongoDB"
date: 2017-04-05 14:28:56 +0800
comments: true
categories: 技术杂谈
---
<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. Mongodb 简介</a>
<ul>
<li><a href="#sec-1-1">1.1. 基本概念</a>
<ul>
<li><a href="#sec-1-1-1">1.1.1. 数据库（database）</a></li>
<li><a href="#sec-1-1-2">1.1.2. 文档（document）</a></li>
<li><a href="#sec-1-1-3">1.1.3. 集合（collection）</a></li>
</ul>
</li>
<li><a href="#sec-1-2">1.2. 基本设计</a></li>
</ul>
</li>
<li><a href="#sec-2">2. MongoDB 启动</a></li>
<li><a href="#sec-3">3. MongoDB shell 简介</a>
<ul>
<li><a href="#sec-3-1">3.1. 运行shell</a></li>
</ul>
</li>
<li><a href="#sec-4">4. MongoDB 客户端</a></li>
<li><a href="#sec-5">5. MongoDB 基本操作</a>
<ul>
<li><a href="#sec-5-1">5.1. 创建</a></li>
<li><a href="#sec-5-2">5.2. 读取</a></li>
<li><a href="#sec-5-3">5.3. 更新</a></li>
<li><a href="#sec-5-4">5.4. 删除</a></li>
</ul>
</li>
</ul>
</div>
</div>


# Mongodb 简介<a id="sec-1" name="sec-1"></a>

MongoDB 是一款强大，灵活，且易于扩展的通用型数据库。

它能够扩展出非常多的功能，如二级索引，范围查询，排序，聚合以及本地空间索引的功能

## 基本概念<a id="sec-1-1" name="sec-1-1"></a>

Mongodb 是一个面向文档的NoSQL型数据库，不采用关系模型。
其中mongodb 不再有“行”的概念，而是以文档取而代之。

另外也不存在预定义模式，即文档的键（key）和值（value）不再是固定的类型和大小

### 数据库（database）<a id="sec-1-1-1" name="sec-1-1-1"></a>

Mongodb 的一个实例可以拥有多个相互独立的数据库,

在Mongodb中，多个文档组合成集合，而多个集合即组成数据库。

### 文档（document）<a id="sec-1-1-2" name="sec-1-1-2"></a>

Mongodb 中的数据的基本单元，类似于关系型数据库管理系统中的行

表示形式：

{"greeting":"Hello World",
          &#x2026;.           }

### 集合（collection）<a id="sec-1-1-3" name="sec-1-1-3"></a>

类似的，可以看作是一个拥有动态模式的表

## 基本设计<a id="sec-1-2" name="sec-1-2"></a>

Mongodb 的设计采用横向扩展。同时面向文档的数据模型

使它能够很容易的在多台服务器之间进行数据分割。

# MongoDB 启动<a id="sec-2" name="sec-2"></a>

通常，MongoDB做为网络服务守护进程运行，客户端可连接到该服务器并执行操作。

1.  下载MongoDB（<http://www.mongodb.org/downloads>）

2.  解压，并运行 **mongod** 命令

启动后，在默认情况下服务器会打印版本和系统信息，并监听27017端口。

除此之外 mongod 还会启动一个非常基本的HTTP服务器，监听比主端口号高1000的端口

这意味着你可以访问 <http://localhost:28017> 来获取数据库管理信息

# MongoDB shell 简介<a id="sec-3" name="sec-3"></a>

MongoDB 自带JavaScript Shell，可以在shell中用命令行与Mongodb实例交互

## 运行shell<a id="sec-3-1" name="sec-3-1"></a>

    mongo
    
    MongoDB Shell version: 2.4.0
    
    connecting to :test
    
    >

shell 是一个功能完备的JavaScript解释器，可以运行任意JavaScript程序，另外还可以充分使用JavaScript的标准库

# MongoDB 客户端<a id="sec-4" name="sec-4"></a>

在客户端的shell启动后，如果要查看db当前执行哪个数据库，可以使用 **db** 命令

    > db
    
    test

# MongoDB 基本操作<a id="sec-5" name="sec-5"></a>

## 创建<a id="sec-5-1" name="sec-5-1"></a>

*insert* 函数可以将一个文档添加到集合中。举一个存储文章的例子：首先，

创建一个名为post的局部变量，这是一个JavaScript对象，用于表示我们的文档

他会有几个键：“ **title** ”、“ **content** ”和 “ **date** ”（发布时间）

    > post = {
    "title":"hello",
    "content":"Here's my blog post",
    "date":new Date()
    }
    
    > db.blog.insert(post)

此时，这篇文章已经存到数据库中了，要查看它可用调用集合的 *find* 方法

    > db.blog.find()

## 读取<a id="sec-5-2" name="sec-5-2"></a>

*find* 和 *findOne* 可以接收一个 **查询文档** 为限定条件，并返回符合一定条件的文件，使用

*find* 的默认自动显示最多20个匹配的文档。

## 更新<a id="sec-5-3" name="sec-5-3"></a>

使用 *update* 修改文章：

update接收（至少）两个参数：
-   第一个是限定条件（用于匹配待更新的文档）
-   第二个是新的文档

例如我们为之前的文章增加评论功能，就需要新增一个键，用于保存评论数组。

    > post.comments = []
    [ ]
    
    > db.blog.update({ title : "hello"} , post)

再用 find 查看一下，就可以看见新的键。

    > db.blog.find()

## 删除<a id="sec-5-4" name="sec-5-4"></a>

使用remove方法可以将文档从数据库中永久删除。如果没用任何参数传入，他将删除集合内的全部文档。

同时，它可以接收一个做为限定条件的文档做为参数。例如：

    > db.blog.reomve({ title : "hello" })

---

***有关Mongodb的简单描述到此结束，更多Mongdb使用操作内容，欢迎参考下一篇文章***
