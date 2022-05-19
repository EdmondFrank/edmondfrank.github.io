---
layout: post
title: "百度前端技术学院-任务5"
date: 2016-07-15 17:52:32 +0800
comments: true
categories: 学习心得
---

#百度前端技术学院-任务五
**[任务五：零基础HTML及CSS编码（二）](http://ife.baidu.com/task/detail?taskId=5)**

> style.css

```css
body{
            margin:20;
            padding:0;
            background-color: #CCCCCC;
        }
        .top{
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
            color: #FFFFFF;
        }
        .top ul li a{
            color: white;
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

     aside
     {
         margin: 20px;
         padding: 20px;
         width: 310px;
         background-color: #FFFFFF;
         border: 1px solid #CDCDCD;
         box-shadow: 5px 5px 5px #888888;
         position: absolute;
         right: 0px;
         top: 60px;
     }
.wrap{
    margin: 20px 392px 20px 20px;
    padding: 20px;
    background-color: #FFFFFF;
    border: 1px solid #CDCDCD;
    box-shadow: 5px 5px 5px #888888;
}

span{
    padding: 5px;
    width: 120px;
    height: 120px;
    border: 1px solid #CDCDCD;
    font-size: 14px;
    text-align: center;
    display: inline-block;
}
div.aside_title{
    border-left: 3px solid #CCC;
    padding: 2px 1px 1px 2px;
}

div.aside_title h2{
    margin: 5px;
    font-size: 16px;
}

div.form table{
    width: 100%;
    font-size: 12px;
    text-align: left;

}
footer{
    color: #FFF;
    font-size: 12px;
    background-color: #000;
    margin: 0px;
    width: 100%;
    height: 60px;
    text-align: center;
    padding-top: 25px;
}

.notice{
            color:#8e8e8e;
        }

table.table1{
    width: 100%;
    text-align: left;
    border-collapse:collapse;
}

.table1 th{
    color: #FFF;
    font-size: 14px;
    padding: 5px;
    background-color: #333;
    border: 1px solid #CDCDCD;
}

.table1 td{
    width: 40%;
    font-size: 12px;
    padding: 5px;
    border: 1px solid #CDCDCD;
}



table.table1 tr.last_line{
    background-color: #CCCCCC;
}
.submit{
    width: 100%;
    height: 35px;
    color: #FFF;
    font-size: 14px;
    border-radius: 10px;
    background-color: rgb(51,85,208);
}

```
> index.html

```html
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Language" content="zh-ch"/>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <link rel="stylesheet" href="style.css" type="text/css"/>
    <title>任务五</title>
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

    <div class="wrap">
        <h2>图片</h2>
            <span>
                好看的图片
                <img src="warp.png" width="120px" height="90px"/>
            </span>

            <span>
                好看的图片
                <img src="warp.png" width="120px" height="90px"/>
            </span>

            <span> 
                好看的图片
                <img src="warp.png" width="120px" height="90px" />
            </span>

            <span>
                好看的图片
                <img src="warp.png" width="120px" height="90px" />
            </span>

            <span>
                好看的图片
                <img src="warp.png" width="120px" height="90px" />
            </span>

            <span>
                好看的图片
                <img src="warp.png" width="120px" height="90px" />
            </span>

            <span>
                好看的图片
                <img src="warp.png" width="120px" height="90px" />
            </span>

            <span>
                好看的图片
                <img src="warp.png" width="120px" height="90px" />
            </span>

            <span>
                好看的图片
                <img src="warp.png" width="120px" height="90px" />
            </span>

            <span>
                好看的图片
                <img src="warp.png" width="120px" height="90px" />
            </span>
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
    <table class="table1" >
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
            <tr class ="last_line">
              <td>总计</td>
              <td colspan="2">1000</td>
            </tr>
          </table>
</div>
    <aside>
        <div class="aside_title">
            <h2>这里以后是一个侧栏，这是侧栏的标题</h2>
        </div>
        <div class="form">
                <table>
                <tr>
                    <td>请输入邮箱地址</td>
                    <td></td>
                    <td>,<input type="text" name="email" value="这是一个文本输入框" onclick="if(value==defaultValue){value='';this.style.color='#000'}" onBlur="if(!value){value=defaultValue;this.style.color='#999'}" style="color:#999"/></td>
                </tr>
                <tr>
                    <td></td>
                    <td></td>
                    <td class="notice">邮箱地址请按要求格式输入</td>  
                </tr>
                <tr>
                    <td>请输入密码</td>
                    <td></td>
                    <td><input type="text" name="password" value="这是一个文本输入框" onclick="if(value==defaultValue){value='';this.style.color='#000'}" onBlur="if(!value){value=defaultValue;this.style.color='#999'}" style="color:#999"/></td>
                </tr>
                <tr>
                    <td>请重复输入密码</td>
                    <td></td>
                    <td><input type="text" name="password" value="这是一个文本输入框" onclick="if(value==defaultValue){value='';this.style.color='#000'}" onBlur="if(!value){value=defaultValue;this.style.color='#999'}" style="color:#999"/></td>
                </tr>
                <tr>
                    <td></td>
                    <td></td>
                    <td class="notice">密码请为6-16位英文数字</td>  
                </tr>
                <tr>
                    <td>性别</td>
                    <td></td>
                    <td><input type="radio" name="Male" checked="checked"/>男 <input type="radio" name="Female"/>女</td>
                </tr>
                <tr>
                    <td>城市</td>
                    <td></td>
                    <td>
                        <select>
                            <option>北京</option>
                            <option>广东</option>
                        </select>
                    </td>
                </tr>
                <tr>
                    <td>爱好</td>
                    <td></td>
                    <td><input type="checkbox" value="sports"/>运动 <input type="checkbox" value="art"/>艺术 <input type="checkbox" value="sciense"/>科学</td>
                </tr>
                <tr>
                    <td>个人描述</td>
                    <td></td>
                    <td><textarea cols="20"></textarea></td>
                </tr>
                </table>
        </div>
        <button class="submit">确认提交</button>
    </aside>
</body>

<footer><p>版权所有©</p></footer>
</html>
```
```
