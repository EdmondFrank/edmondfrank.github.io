---
layout: post
title: "2016-10-12:天翼校园网客户端登录协议更新"
date: 2016-10-12 22:14:07 +0800
comments: true
categories: Linux杂谈
---
##天翼校园网客户端登录协议更新
**PS:此登录协议仅供参考,未必适合所有高校.**
**本项目源码不遵循任何协议,各位可以随意下载及修改,但请勿用作非法 or 商业用途!**

###主要变化
**[原登录协议](https://github.com/EdmondFrank/Esurfing) 步骤为:challange -> 获取rescode -> login,现登录协议取消了rescode认证,因此可以省略challange可以直接login**

###登录协议主要源码如下:
```ruby
#coding:utf-8
require 'json'
require 'net/http'
require 'uri'
require 'digest/md5'
body = File.read("./config.json")
config = JSON.parse(body)
username = config["username"].to_s
password = config["password"].to_s
clientip = config["clientip"].to_s
nasip = config["nasip"].to_s
mac = config["mac"].to_s
iswifi="1050"
def get_timestamp()
  return Time.now.to_i.to_s+'444'
end
def get_md5(str)
  return  Digest::MD5.hexdigest(str+'Eshore!@#').upcase
end

def active(username,clientip,nasip,mac)
  timestamp = get_timestamp
  authenticator= get_md5(clientip+nasip+mac+timestamp)
  puts username,clientip,nasip,mac,timestamp,authenticator
  url="http://enet.10000.gd.cn:8001/hbservice/client/active?username=#{username}&clientip=#{clientip}&nasip=#{nasip}&mac=#{mac}&timestamp=#{timestamp}&authenticator=#{authenticator}"
  uri = URI(url)
  resp = Net::HTTP.get(uri)
  #resp = `curl -A "" -H "Accept:" -H "Host:enet.10000.gd.cn:8001" #{url}`
  result = JSON.parse(resp)
  puts result
  return result["rescode"].to_s
end

def login(username,password,clientip,nasip,mac,iswifi)
  url = "http://enet.10000.gd.cn:10001/client/login"
  timestamp =  get_timestamp
  authenticator=get_md5(clientip+nasip+mac+timestamp)
  postdata={}
  postdata["username"]=username
  postdata["password"]=password
  postdata["clientip"]=clientip
  postdata["nasip"]=nasip
  postdata["mac"]=mac
  postdata["timestamp"]=timestamp
  postdata["authenticator"]=authenticator
  postdata["iswifi"]=iswifi
  data=postdata.to_json

  headers = {"Content-Type" => "application/json"}
  headers["Host"]="enet.10000.gd.cn:10001"
  headers["Content-Length"]=data.bytesize.to_s
  headers["Expect"]="100-continue"
  headers["Connection"]="Keep-Alive"
  puts `curl -d '#{data}' -A "" -H "Accept:" -H "Content-Type:application/json" -H "Host:enet.10000.gd.cn:10001" -H "Content-Length:#{data.bytesize.to_s}" -H "Expect:100-continue" -H "Connection:Keep-Alive" -X POST #{url}`
end
code = active(username,clientip,nasip,mac)
if code != "0"
  login(username,password,clientip,nasip,mac,iswifi)
end
```

**注:源码中才用了curl发送post包,使用前需先确认系统中是否用curl工具.若是改回Ruby的net/http发包请去掉[Expect]="100-continue"头**

**目前仅提供登录协议供大家参考,有能力者可自行实现完整程序及Gui界面,完整的程序(Cli版本/Gui版本),博主也会后续在我的[Github](https://github.com/EdmondFrank)上发布**
