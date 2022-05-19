---
layout: post
title: "百度前端技术学院-任务6"
date: 2016-07-21 21:07:36 +0800
comments: true
categories: 学习心得
---

#百度前端技术学院-任务六
**[任务六：通过HTML及CSS模拟报纸排版](http://ife.baidu.com/task/detail?taskId=6)**

> style.css

```css
* {
  margin: 0;
  padding: 0;
}
header {
  border-bottom: 2px solid #938e3c;
  height: 61px;
  position: relative;
}
header div {
  height: 61px;
  width: 110px;
  background-color: #e74457;
}
header div p {
  position: absolute;
  left: 6px;
  bottom: 2px;
  font-size: 12px;
  color: #fff;
  font-family: "黑体";
}
header > p {
  position: absolute;
  right: 2px; 
  bottom: 2px;
  font-size: 12px;
  color: #d45d5c;
  font-family: "黑体";
}
#column1 {
  margin-top: 20px;
}
#column1 #img1,
#column1 img {
  width: 641px;
  height: 301px;
}
#img1 {
  position: relative;
  display: inline-block;
}
#img1 div {
  width: 191px;
  height: 301px;
  position: absolute;
  opacity: 0.6;
  bottom: 0;
}
#img1 .green {
  background-color: #20a176;
  left: 0;
}
#img1 .red {
  background-color: #a6435a;
  right: 0;
}
#right1 {
  float: right;
  display: inline-block;
  width: 203px;
  border-top: 2px solid #938e8c;
}
#right1 .about {
  height: 26px;
  display: inline-block;
  border-bottom: 2px solid #808080;
}
#right1 .about > p {
  font-family: "Microsoft YaHei";
  font-size: 18px;
  color: #000;
}
#right1 .about > p:first-letter {
  font-size: 24px;
}
#column2 {
  margin-top: 20px;
  overflow: hidden;
}
#column2 div {
  float: left;
  display: inline-block;
  width: 200px;
  margin-right: 20px;
}
#column2 div p:first-child {
  font: bolder 16px "微软雅黑";
  text-decoration: underline;
  margin: 10px 0;
}
#column2 div span {
  font-style: italic;
  color: #cd4a48;
}
#column3 {
  margin-top: 20px;
  border-bottom: 1px solid #938e8c;
}
#column3 #left3 {
  width: 415px;
  display: inline-block;
}
#column3 #left3 #leftUp3 {
  border-bottom: 2px solid #11456b;
}
#column3 #left3 #leftUp3 p {
  font: bolder 42px "黑体";
  color: #11456b;
  line-height: 42px;
  margin-bottom: 16px;
}
#column3 #left3 #leftUp3 p span {
  font-size: 72px;
  font-style: italic;
  color: #f5e327;
  line-height: 72px;
}
#leftDown3 {
  margin: 30px 0;
}
#leftDown3 p {
  font: 12px "宋体";
  color: #767777;
  line-height: 16px;
}
#leftDown3 p:first-child:first-letter {
  font: 70px "微软雅黑";
  color: #f5e327;
  float: left;
  margin: 0 10px;
}
#leftDown3 img {
  width: 200px;
  height: 300px;
  float: right;
  margin: 20px 0 20px 20px;
}
#right3 {
  display: inline-block;
  width: 458px;
  float: right;
}
#right3 #rightUp3 {
  height: 275px;
  position: relative;
}
#right3 #rightUp3 img {
  width: 458px;
  height: 275px;
}
#right3 #rightUp3 div {
  width: 458px;
  height: 61px;
  background-color: #717171;
  opacity: 0.4;
  position: absolute;
  bottom: 0;
}
#right3 #rightUp3 p {
  height: 44px;
  border-left: 3px solid #72b16a;
  font: 26px "微软雅黑";
  color: #fff;
  position: absolute;
  bottom: 0;
  line-height: 44px;
  margin: 0 0 8px 30px;
  padding: 0 10px;
}
#right3 #rightUp3 p span {
  font: italic 12px "微软雅黑";
  color: #72b16a;
  padding: 0 8px;
}
#rightDown3 {
  margin-top: 30px;
  background-color: rgba(172,118,92,0.42);
}
#rightDown3 #downUp3 {
  margin: 20px 0 20px 20px;
}
#rightDown3 #downUp3 p {
  font: 16px;
  font-family:"宋体";
  color: #5a5b5b;
  padding: 20px 10px 0 10px;
}
#rightDown3 #downUp3 p span {
  font: italic 12px "宋体";
  text-align: right;
}
#down3 {
  height: 160px;
  margin-top: 30px;
}
#down3 #downL3 {
  width: 260px;
  height: 160px;
  display: inline-block;
  background-color: #d45d5c;
  position: relative;
}
#down3 #downL3 p {
  color: #fff;
}
#down3 #downR3 {
  display: inline-block;
  overflow: hidden;
  float: right;
  width: 198px;
  height: 160px;
}
#down3 #downR3 p {
  font: italic 14px "黑体";
  color: #5a5b5b;
  text-indent: 3em;
  padding: 16px 0 0 16px;
  position: relative;
}
#down3 #downR3 p span {
  font-size: 72px;
  color: #d45d5c;
  position: absolute;
  font-family:"黑体";
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
    <title>任务六</title>
</head>
<body>
  <div style="width: 980px;height: 1386px;margin: 0 auto;">
    <div style="margin: 0 auto;width: 892px;">
        <header>
            <div><p>ife.baidu.com</p></div>
            <p>2016.03</p>
        </header>
        <div id="column1">
            <div id="img1">
                <img src="pic1.png">
                <div class="green"></div>
                <div class="red"></div>
            </div>
            <div id="right1">
                <div class="about">
                    <p>ABOUT</p>
                </div>
                <br/>
                <div class="about">
                    <p>TECHNOLOGE</p>
                </div>
                <p style="font: 12px 楷体;color: #676767;line-height: 10px; padding-top: 4px;">About technologe about technoge about technoge</p>
                <p style="font: italic 116px 微软雅黑;color: #75b86b;">700</p>
                <div style="display: inline-block;"><p style="font: 55px 微软雅黑;color: #cc8091;">3.2</p></div>
                <div style="display: inline-block;">
                    <p style="font: 21px 微软雅黑;color: #cc8091;">css</p>
                    <p style="font: 12px 微软雅黑;color: #cc8091;">cssssssssss</p>
                </div>
        </div>
    </div>
  </div>
  <div id="column2">
            <div><p style="color: #418c59;">What</p>
                <p style="font: 12px 宋体;color: #767777;line-height: 16px;">
前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端端前端前端前端前端前端前端前端</p>
            </div>
            <div><p style="color: #d2994f;">When</p>
                <p style="font: 12px 微软雅黑;color: #231815;line-height: 16px;opacity: 0.7;">
                    前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端</p></div>
            <div><p style="color: #cc7680;">How</p>
                <p style="font: 12px 微软雅黑;color: #231815;line-height: 20px;opacity: 0.7;">
                    前端前端前前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端端前端前端前端前端前端前端前端</p>
                <br>
                <br>
                <p style="font: bolder 12px 微软雅黑;color: #231815;line-height: 20px;opacity: 0.7;">
                    What--------<span>40%</span></p>
                <p style="font: bolder 12px 微软雅黑;color: #231815;line-height: 20px;opacity: 0.7;">
                    What--------<span>30%</span></p>
                <p style="font: bolder 12px 微软雅黑;color: #231815;line-height: 20px;opacity: 0.7;">
                    What--------<span>30%</span></p>
            </div>
  </div>
  <div id="column3">
            <div id="left3">
                <div id="leftUp3">
                    <p><span>THE</span> TECHNOLOGE OF FRONT</p>
                    <p>前端技术领域</p></div>
                <div id="leftDown3">
                    <p>前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端<img src="pic2.png" alt="">前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端</p>
                    <p style="text-indent: 2em;">
                        前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端</p>
                    <p style="text-indent: 2em;">
                        前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端前端
                    </p>
                </div>
            </div>
            <div id="right3">
                <div id="rightUp3">
                    <img src="pic3.png" alt="">
                    <div></div>
                    <p>前端技术<span>前端技术前端技术</span></p>
                </div>
                <div id="rightDown3">
                    <div id="downUp3">
                        <p style="padding-top: 30px;">▲ 前端前端前端前端前端前端 ...................<span>前端</span></p>
                        <p>▲ 前端前端前端前端 .........................<span>前端前</span></p>
                        <p>▲ 前端前端前端前端前端前端 ...................<span>前端</span></p>
                    </div>
                    <div id="down3">
                        <div id="downL3">
                            <p style="font: 110px 微软雅黑;display: inline-block;line-height: 160px;">0</p>
                            <div style="display: inline-block;height: 86px;border-left: 2px solid #ffffff;position: absolute;top: 50%;margin-top: -43px;">
                                <p style="font: italic 21px 黑体;padding-left: 3px;">ONE TWO</p>
                                <P style="font: italic 21px 黑体;padding-left: 3px;">THREE FOUR FIVE</P>
                                <P style="font: 12px 黑体;padding-left: 3px;">hello world hello world</P>
                                <p style="font: 12px 黑体;padding-left: 3px;">hello word</p>
                            </div>
                        </div>
                        <div id="downR3">
                            <p><span style="left:-35px;top: 0;">“</span> world
                                hello world hello world hello world hello world
                                hello world hello world hello world hello world
                                hello world hello world <span style="right: 0;">”</span></p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <p style="float: right;font: 12px 黑体;color: #d45d5c;">ife.baidu.com</p>
    </div>
  </div>
</body>
</html>
```
