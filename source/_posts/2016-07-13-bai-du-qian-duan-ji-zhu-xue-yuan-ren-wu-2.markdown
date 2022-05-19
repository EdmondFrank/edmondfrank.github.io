---
layout: post
title: "百度前端技术学院-任务2"
date: 2016-07-13 17:58:19 +0800
comments: true
categories: 学习心得
---

#百度前端技术学院-任务二
**[任务二：零基础HTML及CSS编码（一）](http://ife.baidu.com/task/detail?taskId=2)**

```html
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Language" content="zh-ch"/>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
    <title>任务二</title>
    <style>
        body{
            margin:20;
            padding:0;
            background-color: #cccccc;
        }
        .top,.footer{
            background-color: #333333;
            height:50px;
        }
        .top h1{
            color: blue;
            display: inline-block;
            margin: 0;
        }
        .top ul{
            float: right;
        }
        .top ul li{
            display: inline-block;
            padding-left: 10px;
            color: #fff;
        }
        .top ul li a{
            color: white;
        }
        .wrap{
            margin: 20px 20px 0 20px;
            background-color: #fff;
            box-shadow: 5px 5px 5px #000;
            padding:10px;
        }
     .wrap2{
            margin: 20px 20px 0 20px;
            background-color: #fff;
            box-shadow: 5px 5px 5px #000;
            padding:10px;
        }
        .wrap h3{
            color: #646464;
        }
        .wrap .author{
            color: #707070;
            text-indent: 0;
        }
        .wrap>p{
            text-indent: 2em;
        }
     .wrap ul li{
         list-style-type:none;
     }
     .wrap2 ul li{
         border: 1px solid #000;
         padding: 10px;
         text-align: center;
         margin: 10px;
         max-width: 10%;
         max-height: 10%;
         width:auto;
         height:auto;
         list-style-type:none;
     }
        .wrap li img{
            max-width: 100%;
            max-height: 100%;
            width:auto;
            height:auto;
        }
      .wrap2 li img{
            max-width: 100%;
            max-height: 100%;
            width:auto;
            height:auto;
        }
        table{
            width:100%;
            padding:0;
            margin:0;
            background-color: #6c6c6c;
        }
        table th{
            color: #fff;
            background-color: #383e41;
            text-align: left;
        }
        table tr:last-child th{
            background-color: #c0c0c0;
            color:black
        }
        table td {
            background-color: white;
        }
        .myform table{
            width:40%;
            background-color: white;
            margin-left: 200px;
        }
     .myform h2{
    height:40px;
    line-height:40px;
    padding-left:10px;
    border-left:3px solid #ccc;
}
        .myform table tr{
            height:30px;
        }
     .notice{
            color:#8e8e8e;
        }
        .myform table tr td:first-child{
            text-align: right;
            margin-right: 5px;
        }
        .myform table tr:last-child td:first-child{
            vertical-align: text-top;
        }
        .myform input[type="button"]{
            width:100%;
            height:40px;
            background-color: #3355dd;
            color: white;
            border:none;
            border-radius: 10px;
        }
        .footer p{
            text-align: center;
            color: white;
            line-height: 45px;
        }
    </style>
</head>
<body>
<div class="top">
    <h1>EdmondFrank</h1>
    <ul>
        <li><a href="">导航链接一</a></li>
        <li><a href="">导航链接二</a></li>
        <li><a href="">导航链接三</a></li>
        <li><a href="">导航链接四</a></li>
    </ul>
</div>
<div class="wrap">
    <h2>文章一级标题</h2>
    <h3>文件二级标题</h3>
    <p class="author">文章作者 文章发布时间</p>
<p>这是一个很长很长的段落，这是一个很长很长的段落，
                  这是一个很长很长的段落，这是一个很长很长的段落，
                  这是一个很长很长的段落，这是一个很长很长的段落，
                  这是一个很长很长的段落，<br/>换行了<br/>这是一个很长很长的段落，
                  这是一个很长很长的段落，这是一个很长很长的段落，
                  这是一个很长很长的段落，这是一个很长很长的段落，
                  这是一个很长很长的段落，换行了<br/>这是一个很长很长的段落，
                  这是一个很长很长的段落，<a href = "http://ife.baidu.com">这里有个链接链接到http://ife.baidu.com</a>
                  这是一个很长很长的段落，<b>这里有个粗体字</b>这是一个很长很长的段落，
                  这是一个很长很长的段落，这是一个很长很长的段落。</p>

                <img src="warp.png">

                <p>这是一个很长很长的段落，这是一个很长很长的段落，
                  这是一个很长很长的段落，这是一个很长很长的段落，
                  这是一个很长很长的段落，这是一个很长很长的段落，
                  这是一个很长很长的段落，<br/>换行了<br/>这是一个很长很长的段落，
                  这是一个很长很长的段落，这是一个很长很长的段落，
                  这是一个很长很长的段落，这是一个很长很长的段落，
                  这是一个很长很长的段落，换行了<br/>这是一个很长很长的段落，
                  这是一个很长很长的段落，<a href = "http://ife.baidu.com"target="_blank">这里有个链接新窗口链接到http://ife.baidu.com</a>
                  这是一个很长很长的段落，<b>这里有个粗体字</b>这是一个很长很长的段落，
                  这是一个很长很长的段落，这是一个很长很长的段落。</p>
</div>
<div class="wrap">
    <h2>另外一篇文章一级标题</h2>
    <h3>文章二级标题</h3>
    <p class="author">文章作者 文章发布时间</p>
    <p>这是一个很长很长的段落，这是一个很长很长的段落，
        这是一个很长很长的段落，这是一个很长很长的段落，
        这是一个很长很长的段落，这是一个很长很长的段落，
        这是一个很长很长的段落，<br/>换行了<br/>这是一个很长很长的段落，
        这是一个很长很长的段落，这是一个很长很长的段落，
        这是一个很长很长的段落，这是一个很长很长的段落，
        这是一个很长很长的段落，换行了<br/>这是一个很长很长的段落，
        这是一个很长很长的段落，<a href = "http://ife.baidu.com">这里有个链接链接到http://ife.baidu.com</a>
        这是一个很长很长的段落，<b>这里有个粗体字</b>这是一个很长很长的段落，
        这是一个很长很长的段落，这是一个很长很长的段落。</p>
    <img src="warp.png">
        <ul class="noli">
        <li>项目列表1</li>
        <li>项目列表2</li>
        <li>项目列表3</li>
    </ul>
</div>
<div class="wrap2">
    <h2>图片</h2>
    <ul style="list-style: none" class="img">
        <li><span>好看的图片</span><img src="warp.png" ></li>
        <li><span>好看的图片</span><img src="warp.png" ></li>
        <li><span>好看的图片</span><img src="warp.png" ></li>
        <li><span>好看的图片</span><img src="warp.png" ></li>
        <li><span>好看的图片</span><img src="warp.png" ></li>
        <li><span>好看的图片</span><img src="warp.png" ></li>
        <li><span>好看的图片</span><img src="warp.png" ></li>
        <li><span>好看的图片</span><img src="warp.png" ></li>
    </ul>
</div>
<div class="wrap">
    <h2>最后一篇文章一级标题</h2>
    <h3>文章二级标题</h3>
    <p class="author">文章作者 发布时间</p>
    <ol>
        <li>排名1</li>
        <li>排名2</li>
        <li>排名3</li>
    </ol>
    <p>一个表格</p>
    <table >
            <tr>
              <th>表头</th>
              <th>表头</th>
              <th>表头</th>
            </tr>
            <tr>
              <td>表内容单元格</td>
              <td>表内容单元格</td>
              <td><a href="http://edmondfrank.github.io">操作</a></td>
            </tr>
            <tr>
              <td>表内容单元格</td>
              <td>表内容单元格</td>
              <td><a href="http://edmondfrank.github.io">操作</a></td>
            </tr>
            <tr>
              <td>表内容单元格</td>
              <td>表内容单元格</td>
              <td><a href="http://edmondfrank.github.io">操作</a></td>
            </tr>
            <tr>
              <td>表内容单元格</td>
              <td>表内容单元格</td>
              <td><a href="http://edmondfrank.github.io">操作</a></td>
            </tr>
            <tr>
              <th>总计</th>
              <th colspan="2">1000</th>
            </tr>
          </table>
</div>
<div class="wrap myform">
	<h2>这里以后是一个侧栏，这是侧栏的标题</h1>
<table>
        <tr>
            <td>请输入邮箱地址</td>
            <td><input type="text" name="email" value="这是一个文本输入框" onclick="if(value==defaultValue){value='';this.style.color='#000'}" onBlur="if(!value){value=defaultValue;this.style.color='#999'}" style="color:#999"/></td>
        </tr>
        <tr>
            <td></td>
            <td class="notice">邮箱地址请按要求格式输入</td>
        </tr>
        <tr></tr>
        <tr>
            <td>请输入密码</td>
            <td><input type="text" name="password" value="这是一个文本输入框" onclick="if(value==defaultValue){value='';this.style.color='#000'}" onBlur="if(!value){value=defaultValue;this.style.color='#999'}" style="color:#999"/></td>
        </tr>
        <tr>
            <td>请重复输入密码</td>
            <td><input type="text" name="password" value="这是一个文本输入框" onclick="if(value==defaultValue){value='';this.style.color='#000'}" onBlur="if(!value){value=defaultValue;this.style.color='#999'}" style="color:#999"/></td>
        </tr>
        <tr>
            <td></td>
            <td class="notice">密码为6-16位英文数字</td>
        </tr>
        <tr></tr>
        <tr>
            <td>性别</td>
            <td><input type="radio" name="Male" value="male" checked>男<input type="radio" name="Female" value="female" checked="false">女</td>
        </tr>
        <tr>
            <td>城市</td>
            <td>
                <select name="">
                    <option value="" aria-checked="true">北京</option>
                    <option value="" aria-checked="false">上海</option>
                </select>
            </td>
        </tr>
        <tr>
            <td>爱好</td>
            <td><input type="checkbox">运动<input type="checkbox">艺术<input type="checkbox">科学</td>
        </tr>
        <tr>
            <td>个人描述</td>
            <td><textarea name="个人描述" onclick="if(value==defaultValue){value=''}" onBlur="if(!value){value=defaultValue}" cols="30" rows="4">这是一个多行输入框，输入个人信息</textarea></td>
        </tr>
    </table>
    <input type="button" value="确认提交">
</div>
<div class="footer">
    <p>版权所有@</p>
</div>
</body>
</html>
```
