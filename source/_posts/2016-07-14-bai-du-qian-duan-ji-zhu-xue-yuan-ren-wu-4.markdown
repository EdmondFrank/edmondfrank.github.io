---
layout: post
title: "百度前端技术学院-任务4"
date: 2016-07-14 17:52:48 +0800
comments: true
categories: 学习心得
---

#百度前端技术学院-任务四
**[任务四：定位和居中问题](http://ife.baidu.com/task/detail?taskId=4)**

```html
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Language" content="zh-ch"/>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
    <title>任务四</title>
    <style>
        html,body,div {
            text-align:center;
            margin: 0;
            padding: 0;
            width:100%;
            height: 100%;
        }
        .center{
            width: 400px;
            height: 200px;
            margin:0 auto;
            background: #ccc;
            position: relative;
            top: 50%;
            transform: translateY(-50%);
        }
        .center .leftcir {

            background: #fc0;
            position: absolute;
            width: 50px;
            height: 50px;
            border-bottom-right-radius: 50px;
        }
        .center .rightcri {
            background: #fc0;
            position: absolute;
            bottom:0;
            right:0;
            width: 50px;
            height: 50px;
            border-top-left-radius: 50px;
        }
    </style>
</head>
<body>
    <div  class="center">
        <div class="leftcir"></div>
        <div class="rightcri"></div>
    </div>
</body>
</html>
```
