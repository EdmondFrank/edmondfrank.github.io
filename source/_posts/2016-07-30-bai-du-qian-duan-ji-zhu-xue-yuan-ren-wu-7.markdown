---
layout: post
title: "百度前端技术学院-任务7"
date: 2016-07-30 21:26:22 +0800
comments: true
categories: 学习心得
---

#百度前端技术学院-任务七
**[任务七：实现常见的技术产品官网的页面架构及样式布局](http://ife.baidu.com/task/detail?taskId=7)**

> style.css

```css
* {
	padding: 0;
	margin: 0;
}

body {
	min-width: 1024px;
	font-family: 微软雅黑 ,sans-serif;
}

nav {
	height: 60px;
	width: 90%;
	margin: 0 auto;
}

a {
	text-decoration: none;
}

li {
	list-style: none;
}

.brand {
	float: left;
	height: 60px;
	line-height: 60px;
	font-size: 22px;
}

.brand em {
	display: inline-block;
	background: url(img/logo-middle.png) center center no-repeat;
	background-size:60%;
	width: 60px;
	height: 60px; 
	float: left;
}

#nav-ul {
	float: right;
	height: 60px;
	line-height: 60px;
}


.nav-list {
	display: inline-block;
	width: 80px;
	text-align: center;
	height: 56px;
}

.fix {
	border-bottom: 4px solid red;
}

.nav-list a {
	text-decoration: none;
	color: #9E9E9E;
	font-size: 14px;
}

.nav-list:first-child a {
	color: red;
}

.nav-list:hover {
	border-bottom: 4px solid red; 
}

.login {
	display: inline-block;
	background: url(img/login-icon.png) left center no-repeat;
	text-indent: 20px;
	color: red !important;
}

.poster {
	position: relative;
	background: url(img/back-img.jpg)no-repeat;
	background-size: 100%;
	height: 800px;
}

.slogan {
	position: absolute;
	left: 150px;
	top: 250px;
}

h2 {
	color: white;
	font-size: 70px;
	font-weight: normal;
	margin-bottom: 10px;
}

.slogan p {
	text-indent: 6px;
	color: white;
	font-size: 30px;
	margin-bottom: 5px;
}

.join-btn {
	margin-top: 40px;
	width: 300px;
	height: 50px;
	line-height: 50px;
}

.join-btn a {
	display: block;
	width: 100%;
	height: 100%;
	padding: 5px 0 5px 0;
	color: white;
	font-size: 25px;
	text-align: center;
	background-color: #e74f4d;
	letter-spacing: 3px;
	transition: background .3s;
}

.join-btn a:hover {
	background-color: #FF9800;
}

.effect {
	margin-top: 50px;
	overflow: hidden;
	margin-bottom: 50px;
}

.section-list {
	width: 25%;
	float: left;
	text-align: center;
}

.section-list p {
	padding-left: 55px;
	padding-right: 55px;
	color: #999;
	margin: 15px auto;
	letter-spacing: 1px;
	font-size: 15px;
	border-right: 2px solid #999;
}

.section-list:nth-child(4) p {
	border-right:0; 
}

.introduce {
	text-align: center;
	background-color: #edecec;
	padding-top: 80px;
	padding-bottom: 80px;
}

.introduce h3 {
	padding-top: 30px;
	text-align: center;
	color: #a1a1a1;
	font-size: 50px;
	font-weight: normal;
	letter-spacing: 1px;
}

.map {
	height: 340px;
	background: url(img/map.jpg) no-repeat;
	background-size: 100%;
}

.map-introduce {
	padding-top: 50px;
	text-align: center;
	color: white;
}

.map-introduce p {
	font-size: 20px;
	padding-bottom: 3px;
}

.map-introduce span{
	margin: 0 auto;
	display: block;
	width: 20px;
	height: 2px;
	background-color:white; 
	margin-bottom: 10px;
}

.map-introduce i{
	display: block;
	font-style: normal;
	font-size: 13px;
}

.select-con {
	width: 80%;
	margin: 0 auto;
	margin-top:50px; 
}

.select-list {
	float: left;
	width: 20%;
	margin-right: 5%;
	position: relative;
	cursor: pointer;
}

.select-title {
	height: 35px;
	line-height: 35px;
	background-color: white;
	color: #000;
	text-indent: 10px;
	position: relative;
}

.email {
	display: block;
	position: absolute;
	top: 50%;
	right: 10px;
	width: 25px;
	height: 20px;
	margin-top: -10px;
	background-color: #be4e48;
}

.email em {
	position: absolute;
	top: 50%;
	left: 50%;
	margin-left: -4px;
	margin-top: -6px;
	z-index: 3;
	display: inline-block;
	width: 6px;
	height: 6px;
	border-left: 2px solid white;
    border-bottom: 2px solid white;
    transform:rotate(-45deg);
}

.select-list ul {
	display: none;
	width: 100%;
	position: absolute;
	top: 35px;
	left: 0;	
	background-color: #e4e3e5;
	padding-bottom: 15px;
	color: black;
}

.select-list:hover ul {
	display: block;
}

.select-list li {
	height: 30px;
	line-height: 30px;
	cursor: pointer;
}

.select-list li:hover {
	color: white;
	background-color: #be4e48;
}

.select-list li:hover i{
	opacity: 1;
}

.select-list i {
	opacity: 0;
	width: 20px;
	height: 20px;
	margin-right: 5px;
	margin-left: 5px;
	display: inline-block;
}

.select-list i:after{
	content: '√';
}

.select-btn {
	height: 35px;
	line-height: 35px;
	background-color: #be4e48;
	text-align: center;
	color: white;
	letter-spacing: 15px;
}

.activity {
	overflow: hidden;
	padding: 100px 0 100px 0; 
	background-color: #f7f7f7;
}

.acvivity-con {
	width: 80%;
	text-align: center;
	margin: 0 auto;
}

.activity-list {
	width: 25%;
	float: left;
}

.activity-list img {
	border-width: 10px 7px;
	border-color: white;
	border-style: solid;
}

.act-site {
	padding-top: 15px;
	font-size: 15px;
}

.act-site-top {
	text-align: center;
	font-size: 20px;
	letter-spacing: 20px;
	text-indent: 20px;
}

.act-site-top + p{
	padding-bottom: 20px;
	color: #b5b5b5;
	font-size: 14px;
}

.activity-list p:nth-child(3) {
	color: #b5b5b5;
	padding-top: 5px;
	font-size: 14px;
}

.rule-1 {
	overflow: hidden;
}

.socks {
	text-align: center;
	color: white;
	position: relative;
	width: 66.7%;
	height: 44vw;
	min-height: 450px;
	background: url(img/socks.jpg) center center no-repeat;
	background-size: cover;
	float: left;
}

.socks-text {
	width: 400px;
	height: 300px;
	position: absolute;
	top: 50%;
	left: 50%;
	margin-left: -200px;
	margin-top: -150px;
}

.socks-text p:nth-child(1) {
	font-size: 3em;
}

.socks-text p:nth-child(2) {
	font-size: 8em;
	font-weight: lighter;
}

.socks-text p:nth-child(3),.socks-text p:nth-child(4) {
	font-size: 1.5em;
}

.trangle {
	width: 0;
    height: 0;
    border-top: 20px solid transparent;
    border-right: 40px solid white;
    border-bottom: 20px solid transparent;
    position: absolute;
    top: 50%;
    right: 0;
    margin-top: -20px;
}

.socks-right {
	position: relative;
	float: left;
	text-align: center;
	height: 44vw;
	min-height: 450px;
	width: 33.3%;
}

.world-content {
	position: absolute;
	height: 300px;
	width: 300px;
	top: 50%;
	left: 50%;
	margin-left: -150px;
	margin-top: -150px;
}

.world-content-title {
	font-size: 24px;
	height: 40px;
	line-height: 40px;
}

.world-content-title span:nth-child(2):after {
	content: '/';
	color: #666;

}

.world-content-title span:nth-child(3) {
	color: red;
}

.world-content p {
	margin-top: 20px;
	color: #aaaaaa;
	font-size: 14px;
	line-height: 30px;
}

.world-content a {
	display: block;
	width: 110px;
    height: 35px;
    line-height: 35px;
    margin: 40px auto 0 auto;
    text-align: center;
    border: 1px solid #e74f4d;
    color: #e74f4d;
    font-size: 13px;
    font-weight: bold;
}

.world-btn {
	text-align: center;
	margin-top: 10px;
}

.world-btn  span{
	display: inline-block;
    width: 10px;
    height: 10px;
    margin: 0 8px;
    border-radius: 5px;
    box-sizing: border-box;
    border: 1px solid #919292;
}

.selected {
	background-color: #919292;
}

.rule-2 {
	overflow: hidden;
}

.rule-part {
	float: left;
	width: 33.3%;
	min-height: 400px;
	height: 40vw;
	position: relative;
}

.new-time {
	text-align: center;
	width: 300px;
	height: 200px;
	position: absolute;
	top: 50%;
	left: 50%;
	margin-left: -150px;
	font-size: 1.3em;
	margin-top: -100px;
}

.new-time > * {
	margin-bottom: 20px;
}

.new-time span {
	display: inline-block;
	width: 30px;
	height: 2px;
	background-color: white;
} 

.new-time a {
	display: block;
	color: white;
	width: 150px;
	height: 40px;
	line-height: 40px;
	margin: 0 auto;
	border: 2px solid white; 
}

.red-bj {
	background-color: #e7504d;
	color: white;
}

.white-bj span {
	background-color: #abaaaa;
}

.white-bj a {
	border-color: #e7504d;
	color: #e7504d;
}

.photo-bj {
	background: url(img/woman.jpg) no-repeat;
	background-size: cover;
}

.left-trangle {
	width: 0;
    height: 0;
    border-top: 20px solid transparent;
    border-left: 40px solid white;
    border-bottom: 20px solid transparent;
    position: absolute;
    top: 50%;
    left: 0;
    margin-top: -20px;
} 

.invitation {
	margin-top: 100px !important;
	overflow: hidden;

}

.invitation-title {
	margin-bottom: 50px !important;
	text-align: center;
}

.invitation-title h4 {
	font-weight: normal;
	font-size: 20px;
}

.invitation-title span {
	display: inline-block;
	width: 20px;
	height: 2px;
	background-color: #ea6563;
}

.invitation-title p {
	margin-top: 15px;
	color: #b7b7b7;
	font-size: 12px;
}

.invition-area {
	width: 80%;
	margin: 30px auto;
}

.invitation-protocol {
	float: left;
	width: 40%;
}

.invitation-login {
	width: 60%;
	float: left;
}

.invitation-protocol h4 {
	color: #807c7c;
	font-weight: normal;
	margin-bottom: 15px;
}

.invitation-protocol h4:nth-child(7) {
	margin-bottom: 0;
}

.invitation-protocol p {
	width: 80%;
	color:#6e6e6e;
	font-size: 12px;
}

.invitation-protocol span {
	display: inline-block;
	line-height: 15px;
	margin-right: 4px;
	width: 5px;
    height: 5px;
    border-left: 2px solid #ccc;
    border-bottom: 2px solid #ccc;
    transform: rotate(-45deg);
}

.invitation-protocol  a{
	width: 80%;
	height: 15px;
	line-height: 15px;
	display: block;
	text-align: right;
	color: #adabab;
	font-size: 10px;
	margin-bottom: 40px;
}

.input-group {
	padding-left: 3px;
    display: inline-block;
	width: calc(50% - 20px);
    margin: 5px;
    text-indent: 0px;
    background-color: #ededef;
    position: relative;
    cursor: pointer;
    font-size: 14px;	
}

.input-group:before {
	content: attr(data-label);
	position:absolute;
	height: 20px;
	line-height: 20px;
	top: 50%;
	margin-top: -12px;
}

.input-group input {
	text-indent: 80px;
	width: 100%;
	display: inline;
	background-color: #ededef;
	border: 0;
	height: 35px;
    line-height: 35px;
}

.textarea {
	width: calc(100% - 20px);
	font-size: 13px;
}

.textarea:before {
	height: 20px;
	line-height: 20px;
	top: 19px;
}

textarea {
	padding-top: 9px;
	width: 100%;
	height: 120px;
	background-color: #ededef;
	border:0;
	text-indent: 160px;
}

.login-row a {
	padding-left: 3px;
	text-align: center;
	width: calc(100% - 20px);
	height: 30px;
	line-height: 30px;
	display: block;
	margin: 5px;
	background-color: #ededef;
	color: black;
}

footer {
	margin-top: 100px;
}

.top-f {
	background-color: #32353e;
	height: 300px;
	position: relative;
}


.contact {
	width: 500px;
	height: 150px;
	position: absolute;
	top: 50%;
	left: 50%;
	margin-left: -250px;
	margin-top: -100px;
	text-align: center;
	color: white;	
}

.contact h4 {
	font-weight: lighter;
	font-size: 20px;
	padding-bottom: 8px;
}

.contact p {
	font-size: 14px;
}

.grid-email {
	position: relative;
	width: 80%;
	height: 45px;
	line-height: 45px;
	margin: 20px auto;
}

.grid-email:after {
	content: attr(data-label);
	background-color: #6f7278;
	position: absolute;
	top: 1px;
	right: 0;
	width: 70px;
	height: 45px;
	line-height: 45px;
	cursor: pointer;
}

.grid-email input {
	height: 45px;
	border: 0;
	width: 100%;
	text-indent: 15px;
	font-size: 18px;
}

.tools {
	text-align: center;
}

.tools a {
	display: inline-block;
	height: 30px;
	width: 30px;
	margin: 10px;
}

#qq {
	background: url(img/qq.png) center center no-repeat;
}

#weibo {
	background: url(img/weibo.png) center center no-repeat;
}

#twitter {
	background: url(img/twitter.png) center center no-repeat;
}

#earth {
	background: url(img/earth.png) center center no-repeat;
}

.bot-f {
	background-color: #2f2f38;
}

.links {
	width: 85%;
	height: 50px;
	margin: 0 auto;
}

.links a {
	color: white;	
	line-height: 50px;
	font-size: 12px;
}

.links a:nth-child(2) {
	float: right;
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
	<title>任务七</title>
</head>
<body>
	<header>
		<nav>
			<div class="brand">
				<em></em>
				新世界
			</div>
			<ul id="nav-ul">
				<li class="nav-list fix"><a href="#">首页</a></li>
				<li class="nav-list"><a href="#">最新活动</a></li>
				<li class="nav-list"><a href="#">项目介绍</a></li>
				<li class="nav-list"><a href="#">爱心社区</a></li>
				<li class="nav-list"><a href="#" class="login">登录</a></li>
			</ul>
		</nav>
		<div class="poster">
			<div class="slogan">
				<h2 class="slogin-title">Time of new life</h2>
				<p>新时代，年轻的人们让我们一起</p>
				<p>体验新生活，享受新生活</p>

				<div class="join-btn">
					<a href="#">开始体验</a>
				</div>
			</div>
		</div>
	</header>
	
	<div class="container">
		<section class="effect">
			<div class="section-list">
				<img src="img/icon-1.png" alt="">
				<p>打造全新世界，让你更爱你的生活</p>
			</div>
			<div class="section-list">
				<img src="img/icon-2.png" alt="">
				<p>丰富多彩的公益活动,发挥新世界的主人公意识</p>
			</div>
			<div class="section-list">
				<img src="img/icon-3.png" alt="">
				<p>时尚的新理念,超前体验未知的生活</p>
			</div>
			<div class="section-list">
				<img src="img/icon-4.png" alt="">
				<p>完善的培养机制,培养你的全新的世界观</p>
			</div>
		</section>

		<section class="introduce">
			<img src="img/logo-middle.png" alt="">
			<h3>关于新世界，你不知道的还有些什么</h3>	
		</section>

		<section class="map">
			<div class="map-introduce">
				<p>查找新世界城市活动信息</p>
				<span></span>
				<i>每个城市的有不同的信息,请自主查询您需要了解的城市</i>				
			</div>
			<div class="select-con">
				<div class="select-list">
					<div class="select-title">中国
						<em class="email"><em></em></em>
					</div>
					<ul>
						<li><i></i>美国</li>
						<li><i></i>法国</li>
						<li><i></i>德国</li>
						<li><i></i>新西兰</li>
						<li><i></i>俄罗斯</li>
						<li><i></i>日本</li>
					</ul>
				</div>
				<div class="select-list">
					<div class="select-title">省份
						<em class="email"><em></em></em>
					</div>
					<ul>
						<li><i></i>北京市</li>
						<li><i></i>天津市</li>
						<li><i></i>河北省</li>
						<li><i></i>山西省</li>
						<li><i></i>内蒙古自治区</li>
						<li><i></i>辽宁省</li>
					</ul>
				</div>
				<div class="select-list">
					<div class="select-title">城市
						<em class="email"><em></em></em>
					</div>
                    <ul>
                        <li><i></i>广州</li>
                        <li><i></i>肇庆</li>
                        <li><i></i>增城</li>
                        <li><i></i>江门</li>
                    </ul>
				</div>
				<div class="select-list">
					<div class="select-btn">搜索</div>
				</div>
			</div>
		</section>
		
		<section class="activity">
			<div class="acvivity-con">
				<div class="activity-list">
					<img src="img/pic1.jpg" alt="">
					<p class="act-site">北京活动</p>
					<p>新社区大联盟</p>						
				</div>
				<div class="activity-list">
					<p class="act-site-top">上海活动</p>
					<p>新社区大联盟</p>						
					<img src="img/pic2.jpg" alt="">
				</div>
				<div class="activity-list">
					<img src="img/pic3.jpg" alt="">
					<p class="act-site">深圳活动</p>
					<p>全新海岸线观点站</p>						
				</div>
				<div class="activity-list">
					<img src="img/pic4.jpg" alt="">
					<p class="act-site">香港活动</p>
					<p>奢侈品消费大派送</p>						
				</div>
				
			</div>
		</section>

		<section class="rules">
			<div class="rule-1">
				<div class="socks">
					<div class="socks-text">
						<p>新世界</p>
						<p>TIME</p>
						<p>@新世界-北京</p>
						<p>2016.04.01</p>
					</div>
					<div class="trangle">	
					</div>
				</div>
				<div class="socks-right">
					<div class="world-content">
						<div class="world-content-title">
							<span>新世界</span>
							<span></span>
							<span>01</span>
						</div>
						<p>新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界新世界</p>

						<a href="#">点击详情</a>

						<div class="world-btn">
							<span class="selected"></span>
							<span></span>
							<span></span>
						</div>
					</div>
				</div>
			</div>

			<div class="rule-2">
				<div class="rule-part red-bj">
					<div class="new-time">
						<p>新时代</p>
						<p>关于爱生活的我们</p>
						<span></span>
						<a href="#">查看更多</a>
					</div>					
				</div>	
				<div class="rule-part white-bj">
					<div class="new-time">
						<p>新时代</p>
						<p>关于爱生活的我们</p>
						<span></span>
						<a href="#">查看更多</a>
					</div>					
					
				</div>	
				<div class="rule-part photo-bj">
					<div class="left-trangle">	
					</div>
				</div>	
			</div>
		</section>

		<section class="invitation">
			<div class="invitation-title">
				<h4>成为我们的志愿者</h4>
				<span></span>
				<p>新世界的大家庭需要每一个爱生活的人的加入，如果你够年轻，有梦想，有激情<br>那就不要犹豫快来计入我们，改变所有人的生活</p>
			</div>
			<div class="invition-area">
				<div class="invitation-protocol">
					
						<h4>新世界志愿者协议</h4>
						<p>加入新世界志愿者的人员必须遵守中华人民共共和国的
							相关法律法规，并且本着平等资源的原则
						</p>
						<a href="#"><span></span>more</a>

						<h4>新世界志愿者协议</h4>
						<p>加入新世界志愿者的人员必须遵守中华人民共共和国的
							相关法律法规，并且本着平等资源的原则
						</p>
						<a href="#"><span></span>more</a>

						<h4>更多条款</h4>
						<a href="#"><span></span>more</a>
				</div>

				<div class="invitation-login">
					<div class="login-row">
						<div class="input-group" data-label="姓名">	
							<input type="text" name="name" id="name">							
						</div>
						<div class="input-group" data-label="年龄">	
							<input type="text" name="name" id="name">							
						</div>
					</div>
					<div class="login-row">
						<div class="input-group" data-label="联系方式">	
							<input type="text" name="name" id="name">							
						</div>
						<div class="input-group" data-label="联系地址">	
							<input type="text" name="name" id="name">							
						</div>
					</div>
					<div class="login-row">
						<div class="input-group textarea" data-label="请简单描述您梦想的生活">	
							<textarea></textarea>						
						</div>
					</div>
					<div class="login-row">
						<a href="#">提交申请</a>	
					</div>
				</div>
				
			</div>
		</section>
	</div>
	
	<footer>
		<div class="top-f">
			<div class="contact">
				<h4>联系我们</h4>
				<p>为了更好的获取我们最新的产品资讯，您可以留下您的电子邮箱快速订阅我们的产品资讯
				也可以通过以下任何方式关注我们动态</p>
				<div class="grid-email" data-label="提交">
					<input type="email" placeholder="someone's@email.com">
				</div>
				<div class="tools">
					<a href="#" id="qq"></a>
					<a href="#" id="weibo"></a>
					<a href="#" id="twitter"></a>
					<a href="#" id="earth"></a>
				</div>
			</div>
		</div>
		<div class="bot-f">
			<div class="links">
				<a href="">@2016新世界</a>
				<a href="">back to top</a>
			</div>
		</div>
	</footer>
</body>
</html>
```
