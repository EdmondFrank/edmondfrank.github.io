---
layout: post
title: "emacs+w3m:我的浏览我做主"
date: 2016-07-04 19:23:04 +0800
comments: true
categories: Linux杂谈
---
#如何使用emacs + w3m 上网浏览
##关于w3m的简介:
 w3m与Links一样,是一种基本文本的浏览器.w3m在表格、框架、SSL连接以及颜色方面都具有很强的支持性.甚至在支持图形界面的终端下还可以显示图片.同时w3m具备了浏览速度快、快捷键操作强、隐蔽性高的三大特点.使它成为了在emacs下工作的人的不二浏览器之选.

##如何在emacs下使用w3m:
为了实现在emacs中使用w3m浏览网页,那我们就要通过Emacs-w3m这个emacs的扩展来实现.再次博主首先要说明的一点是Emacs-w3m只是Emacs调用w3m的接口,所以安装w3m浏览器的这个步骤必不可少.如果各位想要在浏览器中能够显示图片,那么则还需要w3m-img.

关于w3m的安装,Ubuntu用户可以直接通过包管理器或是在软件中心中添加软件源即可.其他的Linux用户可以自行在网络上下载w3m的源代码并进行编译安装.

安装完w3m后,接下来便是emacs-w3m这个emacs的扩展的安装与配置了.在这个步骤中需要注意的问题是:**如果发现emacs提示不能加载w3m时,可以使用网络上发布的cvs版本,或是使用linux自带的cvs版本控制工具,将最新的emacs-w3m下载至本地再进行配置**

最后当一切工作都准备就绪之后就可以修改~/.emacs这个配置文件，并加入以下内容: 

---

```
;;w3m的相关设置
(add-to-list'load-path "/usr/local/src/emacs_plugins/emacs-w3m-1.4.4/share/emacs/site-lisp/w3m")
(require 'w3m-load)
(setq w3m-home-page "https://www.google.com.hk") 
```

---

然后重启emacs,按下M-x输入w3m,开始浏览网页即可.

