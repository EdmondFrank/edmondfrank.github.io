---
layout: post
title: "认识网络爬虫"
date: 2017-03-10 19:39:43 +0800
comments: true
categories: Python
---

## 认识网络爬虫

### 什么是网络爬虫
   
   *传统网络爬虫是一个自动提取网页的程序,它为搜索引擎从万维网上下载网页,是搜索引擎的重要组成。
一般从一个或若干初始网页的URL开始,获得初始网页上的URL,在抓取网页的过程中,不断从当前页面上抽取新的URL放入队列,直到满
足系统的一定停止条件。*

### 爬虫有什么用？
   *随着现代商业元素的发展，网络爬虫也开始呈现出一种多元化发展的趋势，应用在各种不同的领域。例如：商业数据收集、科学数据采集、社会特征分析等等*
   ![](http://p1.bqimg.com/567571/795209d053f87619.png)

1. 通用搜索引擎网页搜集器
2. 垂直搜索引擎（招聘网，二手车，买房网）
3. 科学研究
4. hacking
......

### 一个简单的python爬虫
```python
#！/usr/bin/env python
#coding=utf-8
import requests
from pyquery import PyQuery as pq

url = 'http://zhixing.bjtu.edu.cn/portal.php'
r = requests.get(url)
print r.text
print r.content
p = pq(r.text).find('#portal_block_617 li>a')
for d in p:
    print pq(d).text()
    print 'http://zhixing.bjtu.edu.cn/'+pq(d).attr('href')

```
### 爬虫工作过程解析
    
    网络爬虫框架主要由控制器、解析器和索引库三大部分组成，而爬虫工作原理主要是解析器这个环节，解析器的主要工作是下载网页，进行页面的处理，主要是将一些JS脚本标签、CSS代码内容、空格字符、HTML标签等内容处理掉，爬虫的基本工作是由解析器完成。所以解析器的具体流程是：

> **入口访问->下载内容->分析结构->提取内容**

#### 下面以爬取[落网](http://www.luoo.net/)为例子
**第一步 确定目的**
抓取目标网站的某一期所有音乐

**第二步 分析页面结构**
访问落网的某一期刊，通过Chrome的（F12）开发者模式查看播放列表中的歌曲，右侧选中的是一些需要特别注意的语义结构，见下图所示：

![](http://i1.piimg.com/567571/ed8e842e2bb1bb13.png)

这时我们可以看到在Chrome的开发者模式的Network中看到实际请求的播放文件。
根据以上分析我们可以得到播放清单的位置和音乐文件的路径，接下来我们通过Python来实现这个目的。

**实现爬虫**
```python
#-*- coding: utf-8 -*-
import os
import requests
from bs4 import BeautifulSoup
import random
from faker import Factory
import Queue
import threading

fake = Factory.create()
luoo_site = 'http://www.luoo.net/music/'
luoo_site_mp3 = 'http://luoo-mp3.kssws.ks-cdn.com/low/luoo/radio%s/%s.mp3'

headers = {'Connection': 'keep-alive',
    'User-Agent': fake.user_agent()
    }

def random_proxies():
    ip_index = random.randint(0, len(proxy_ips)-1)
    res = { 'http': proxy_ips[ip_index] }
    return res

def fix_characters(s):
    for c in ['<', '>', ':', '"', '/', '\\\\', '|', '?', '*']:
        s = s.replace(c, '')
    return s


class LuooSpider(threading.Thread):
    def __init__(self, url, vols, queue=None):
        threading.Thread.__init__(self)
        print '[luoo spider]'
        print '=' * 20
        self.url = url
        self.queue = queue
        self.vol = '1'
        self.vols = vols

    def run(self):
        for vol in self.vols:
            self.spider(vol)
        print '\\ncrawl end\\n\\n'
        def spider(self, vol):
        url = luoo_site + vol
        print 'crawling: ' + url + '\\n'
        res = requests.get(url, proxies=random_proxies())
                soup = BeautifulSoup(res.content, 'html.parser')
        title = soup.find('span', attrs={'class': 'vol-title'}).text
        cover = soup.find('img', attrs={'class': 'vol-cover'})['src']
        desc = soup.find('div', attrs={'class': 'vol-desc'})
        track_names = soup.find_all('a', attrs={'class': 'trackname'})
        track_count = len(track_names)
        tracks = []
        for track in track_names:
            _id = str(int(track.text[:2])) if (int(vol) < 12) else track.text[:2]  
            _name = fix_characters(track.text[4:])
            tracks.append({'id': _id, 'name': _name})
            phases = {
                'phase': vol,                         # 期刊编号
                'title': title,                       # 期刊标题
                 'cover': cover,                      # 期刊封面
                 'desc': desc,                        # 期刊描述
                 'track_count': track_count,          # 节目数
                 'tracks': tracks                     # 节目清单(节目编号，节目名称)
            }
            self.queue.put(phases)


class LuooDownloader(threading.Thread):
    def __init__(self, url, dist, queue=None):
        threading.Thread.__init__(self)
        self.url = url
        self.queue = queue
        self.dist = dist
        self.__counter = 0       

     def run(self):
        while True:
            if self.queue.qsize() <= 0:
                pass
            else:
                phases = self.queue.get()
                self.download(phases)

    def download(self, phases):
        for track in phases['tracks']:
            file_url = self.url % (phases['phase'], track['id'])

            local_file_dict = '%s/%s' % (self.dist, phases['phase'])
            if not os.path.exists(local_file_dict):
                os.makedirs(local_file_dict)              

            local_file = '%s/%s.%s.mp3' % (local_file_dict, track['id'], track['name'])
            if not os.path.isfile(local_file):
                print 'downloading: ' + track['name']
                res = requests.get(file_url, proxies=random_proxies(), headers=headers)
                with open(local_file, 'wb') as f:
                    f.write(res.content)
                    f.close()
                print 'done.\\n'
            else:
                print 'break: ' + track['name']


if __name__ == '__main__':
    spider_queue = Queue.Queue()

    luoo = LuooSpider(luoo_site, vols=['680', '721', '725', '720'],queue=spider_queue)
    luoo.setDaemon(True)
    luoo.start()

    downloader_count = 5
    for i in range(downloader_count):
        luoo_download = LuooDownloader(luoo_site_mp3, '/home/luoo', queue=spider_queue)
        luoo_download.setDaemon(True)
        luoo_download.start()
```
