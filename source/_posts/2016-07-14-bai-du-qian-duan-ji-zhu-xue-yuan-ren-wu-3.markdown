---
layout: post
title: "百度前端技术学院-任务3"
date: 2016-07-14 16:56:41 +0800
comments: true
categories: 学习心得
---

#百度前端技术学院-任务三
**[任务三：三栏式布局](http://ife.baidu.com/task/detail?taskId=3)**

> style.css

```css
body, div {
	border: 1px solid #999;
	padding: 20px;
}
body {
		margin: 0;
}
#box {
	background-color: #eee;
	position: relative;
	overflow: auto;
	zoom: 1;
	min-height: 440px;
}
#leftbox {
	padding: 0;
	background-color: #fff;
	width: 200px;
	position: absolute;
	
}
#leftbox h2 {
	margin-top:20px; 
	font-size: 12px;
	font-weight: normal;
	float: left;
}
#leftbox img{
	margin: 20px;
	float: left;
}
#rightbox {
	background-color: #fff;
	padding: 0;
	margin-bottom:20px; 
	width: 120px;
	position: absolute;
	top: 20px;
	right: 20px;
}
#rightbox div{
    border:0;
	margin: 20px;
	padding: 0;
}
#midbox {
	background-color:#fff; 
	margin-left: 220px; 
	margin-right: 140px;
	float: left;
}
```
> index.html

```html
<!DOCTYPE html>
<html>
<head>
	<title>任务三</title>
    <meta http-equiv="Content-Language" content="zh-ch"/>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
	<link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
	<div id="box">
		<div id="leftbox">
			<img src="pic.png" width="80px" height="80px" border="1">
			<h2>团队名称</h2>
            <p>EdmondFrank</p>
		</div>
		<div id="midbox">
			<h3>关于你们的团队</h3>
            <p>可以给我们解释一下团队名称的来历,或是分别介绍你们团队的成员.</p>
            <p>(以下为示例)</p>
            <p>百度前端技术学院的课程任务是由百度前端工程师专为对前端不同掌握程度的同学设计。我们尽力保证课程内容的质量以及学习难度的合理性，但即使如此，真正决定课程效果的，还是你的每一次思考和实践。
课程多数题目的解决方案都不是唯一的，这和我们在实际工作中的情况也是一致的。因此，我们的要求不仅仅是实现设计稿的效果，更是要多去思考不同的解决方案，评估不同方案的优劣，然后使用在该场景下最优雅的方式去实现。那些最终没有被我们采纳的方案，同样也可以帮助我们学到很多知识。所以，我们列出的参考资料未必是实现需求所必须的。有的时候，实现题目的要求很简单，甚至参考资料里就有，但是背后的思考和亲手去实践却是任务最关键的一部分。在学习这些资料时，要多思考，多提问，多质疑。相信通过和小伙伴们的交流，能让你的学习事半功倍。</p>
		</div>
		<div id="rightbox">
			<div><img src="pic.png" width="80px" height="80px" border="1"></div>
			<div> <img src="pic.png" width="80px" height="80px" border="1"></div>
			<div> <img src="pic.png" width="80px" height="80px" border="1"></div>
            <div> <img src="pic.png" width="80px" height="80px" border="1"></div>
		</div>
	</div>
</body>
</html>
```
