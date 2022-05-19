---
layout: post
title: "Ruby Shoes-优雅轻量GUI之选"
date: 2016-06-30 20:10:11 +0800
comments: true
categories: 开发资讯
---
#Ruby Shoes：用户界面优雅的轻量之选
##Shoes简介
人们总是在不断在Ruby GUI领域进行尝试。现已知的方法包括：

- 将Ruby绑定到Qt或者GTK这样的用户界面库
- 使用基于JRuby的嵌入式DSL或者API创建Swing界面

--- 

Ruby Shoes实现GUI的方式则略显不同.Ruby Shoes出自Why's (Poignant) Guide to Ruby的作者Why The Lucky Stiff之手,这 Shoes工具箱基于Cairo（绘图）以及Pango（文本）等GTK技术开发,GUI控件的数目受限于设计,而且现有的控件使用到了系统相关的GUI 组件.目前我们可以在MacOSX、Windows以及GTK图形环境下使用它. 

Ruby Shoes用C语言实现,并通过Ruby扩展库同Ruby代码交互。在自述文件中,作者声称受到了HyberCard这样的工具包,Processing以及NodeBox这样的语言的启发.我们可以通过Ruby Shoes自带的样例代码,看出后面几种专门用于完成可视化（Visualization）任务的近代语言对它的影响.

**目前shoes已经更新到第四版,更多有关Shoes的信息可以前往[官网](http://shoesrb.com/)了解.**

*除此之外,最新的[Shoes4](https://github.com/shoes/shoes4)也托管在[github](https://github.com)上.*

##示例代码
```ruby
Shoes.app width: 300, height: 200 do
  background lime..blue
  stack do
    para "Welcome to the world of Shoes!"
    button "Click me" do alert "Nice click!" end
    image "http://shoesrb.com/img/shoes-icon.png",
          margin_top: 20, margin_left: 10
  end
end
```
界面效果如下:

　{% img /images/shoes4.png %}

---
