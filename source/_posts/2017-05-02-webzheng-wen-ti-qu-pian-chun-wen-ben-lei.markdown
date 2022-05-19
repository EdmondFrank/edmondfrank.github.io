---
layout: post
title: "Web正文提取(偏純文本类)"
date: 2017-05-02 20:48:47 +0800
comments: true
categories: Python
---

<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. 算法原理介绍</a>
<ul>
<li><a href="#sec-1-1">1.1. 简介</a></li>
<li><a href="#sec-1-2">1.2. 定义</a></li>
</ul>
</li>
<li><a href="#sec-2">2. 基本步骤</a>
<ul>
<li><a href="#sec-2-1">2.1. 前提需求</a></li>
<li><a href="#sec-2-2">2.2. 提取网页正文</a></li>
<li><a href="#sec-2-3">2.3. 算法实现</a></li>
</ul>
</li>
<li><a href="#sec-3">3. 效果测试</a></li>
</ul>
</div>
</div>


# 算法原理介绍<a id="sec-1" name="sec-1"></a>

## 简介<a id="sec-1-1" name="sec-1-1"></a>

本文主要采用基于标记窗的方法来提取网页的正文信息，不仅仅适合于处理一个网页中所有正文
信息均放在一个td的情况，也适合于处理网页正文放在多个td中的情况。即，它能够解决非Table
结构的网页正文提取问题。

## 定义<a id="sec-1-2" name="sec-1-2"></a>

一般称HTML中成对出现的标记为标记对，称HTML格式的网页中出现在Title之后显示内容为非空的
标记对为标记窗

# 基本步骤<a id="sec-2" name="sec-2"></a>

## 前提需求<a id="sec-2-1" name="sec-2-1"></a>

满足规范化网页，即：

-   除了网页标记tag外的地方出现"<",">"用&lt;&gt;替代
-   所有标记的属性值放在引号中，如 a herf="www.baidu.com"。
-   所有标记都是匹配的，即每个开始标记均对应着一个结束，如<body></body>
-   所有标记都是正确嵌套的

## 提取网页正文<a id="sec-2-2" name="sec-2-2"></a>

1.  找出所有的标记窗Tw，对每一个标记窗Tw<sub>i</sub>(i=1,&#x2026;,N),去掉其中的HTML标记，得到不含任何HTML

标记的字符串。
2.  对每个标记窗Tw<sub>i</sub> 内的字符串分词，得到字符串序列。取出S<sub>twi</sub> 中的实词，得到S<sub>twi</sub> ＝{

W<sub>tw1</sub>,W<sub>tw2</sub>,&#x2026;,W<sub>twq</sub>}。使用Levenshtein Distance公式计算标题词序列S<sub>title与字符串</sub>
序列S<sub>twi的距离（即主题相关性）</sub>
3.  比较计算结果和閥值大小，如果小于则为正文信息，将其提取，否则，舍弃。

## 算法实现<a id="sec-2-3" name="sec-2-3"></a>

```python
    #encoding=utf-8
    import jieba
    from bs4 import BeautifulSoup
    import re
    import requests
    #虚词列表
    excludeWords = ()
    with open('excludeWords','r+') as f:
        excludeWords=f.read()
        f.close()
    excludeWords = eval(excludeWords)
    class domNode:
        parentNode=None
        currNode=None
        innerText=None
        posi=0
        def __init__(self,parentNode,currNode,innerText,posi):
            self.parentNode=parentNode
            self.currNode=currNode
            self.innerText=innerText
            self.posi=posi
    def levenshtein(a,b):
        "相似度计算."
        n, m = len(a), len(b)
        if n > m:
            # Make sure n <= m, to use O(min(n,m)) space
            a,b = b,a
            n,m = m,n
    
        current = range(n+1)
        for i in range(1,m+1):
            previous, current = current, [i]+[0]*n
            for j in range(1,n+1):
                add, delete = previous[j]+1, current[j-1]+1
                change = previous[j-1]
                if a[j-1] != b[i-1]:
                    change = change + 1
                current[j] = min(add, delete, change)
        return current[n]
    
    def filter_words(sourList,filterList):
        dest=[]
        if type(sourList)!="<type 'set'>":
            dest=list(sourList)
        else:
            dest=sourList
        if filterList==None or len(filterList)==0:
            return dest
        dest = [ i for i in dest if not(i in filterList) ]
        return dest
    
    def getContextByNode(htmlSoup,titleKeyWordList,filterList):
        sentenceList=jieba.cut(htmlSoup.string)
        sentenceList=filter_words(sentenceList,excludeWords)
        sentenceList=filter_words(sentenceList,filterList)
        if levenshtein(sentenceList,titleKeyWordList)>=len(sentenceList):
            return None
        return sentenceList
    
    def getHTMLContext(html,filterList):
        "获取html的正文内容，不包含tag标签"
        if html==None:
            return ""
        if len(html)==0:
            return ""
        encodeHtml=encodeSpecialTag(html)
        htmlSoup=BeautifulSoup(encodeHtml)
    
        #去除script和style内容
        if  htmlSoup.script!=None:
            htmlSoup.script.replaceWith("")
        if htmlSoup.style!=None:
            htmlSoup.style.replaceWith("")
    
        if htmlSoup==None or htmlSoup.html==None or htmlSoup.html.head==None or htmlSoup.html.head.title==None:
            return ""
    
        #提取标题
        title=htmlSoup.html.head.title.string
        #print(title)
        if title==None:
            return ""
    
        titleKeyWordList=jieba.cut(title)
        titleKeyWordList = list(filter(lambda w:w.strip(),titleKeyWordList))
        titleKeyWordList=filter_words(titleKeyWordList,excludeWords)
        titleKeyWordList=filter_words(titleKeyWordList,filterList)
        #print(titleKeyWordList)
        # 实词是否为空
        if len(titleKeyWordList)==0:
            return ""
    
        markWindowsList=getMarkWindowsList(htmlSoup.html.body)
        #print(type(markWindowsList))
        #print(markWindowsList)
        markWindowsList = sorted(markWindowsList,key=cmp_to_key(lambda x,y: cmp(x.posi, y.posi)))
    
        #print(titleKeyWordList)
        context=""
        for item in markWindowsList:
            innerText=item.innerText
            markWindowKeyWordList=jieba.cut(innerText)
            markWindowKeyWordList=filter_words(markWindowKeyWordList,excludeWords)
            markWindowKeyWordList=filter_words(markWindowKeyWordList,filterList)
            kl=len(markWindowKeyWordList)
            if kl==0:
                continue
    
            l=levenshtein(titleKeyWordList,markWindowKeyWordList)
            #print(markWindowsList,l)
            if l<kl:
                context+=innerText.strip()+'.\n'
    
        return  decodeSpecialTag(context)
    def cmp(x,y):
        return x-y
    def cmp_to_key(mycmp):
        'Convert a cmp= function into a key= function'
        class K(object):
            def __init__(self, obj, *args):
                self.obj = obj
            def __lt__(self, other):
                return mycmp(self.obj, other.obj) < 0
            def __gt__(self, other):
                return mycmp(self.obj, other.obj) > 0
            def __eq__(self, other):
                return mycmp(self.obj, other.obj) == 0
            def __le__(self, other):
                return mycmp(self.obj, other.obj) <= 0
            def __ge__(self, other):
                return mycmp(self.obj, other.obj) >= 0
            def __ne__(self, other):
                return mycmp(self.obj, other.obj) != 0
        return K
    
    
    def repl(m):
        contents = m.group(3)
        if contents == '</p>':
            return '[[p]]'
        return contents
    def encodeSpecialTag(html):
        "这里的规则有助于正文内容的识别,还可以保留部分标签，如下面就保留了P，br等标签"
        comment=re.compile(r"<!--[.\s\S]*?-->")
        html=comment.sub('', html)
    
    
        #h tag
        hPre=re.compile(r"<[\t ]*?[hH][1-6][^<>]*>")
        html=hPre.sub('[[h4]]', html)
        hAfter=re.compile(r"<[\t ]*?/[hH][1-6][^<>]*>")
        html=hAfter.sub('[[/h4]]', html)
    
        br=re.compile(r"<[ \t]*?br[^<>]*>", re.IGNORECASE)
        html=br.sub('[[br /]]', html)
    
        hr=re.compile(r"<[ \t]*?hr[^<>]*>", re.IGNORECASE)
        html=hr.sub('[[hr /]]', html)
    
    
        strongPre=re.compile(r"<[\t ]*?strong[^<>]*>", re.IGNORECASE)
        html=strongPre.sub('[[strong]]', html)
        strongAfter=re.compile(r"<[\t ]*?/strong[^<>]*>", re.IGNORECASE)
        html=strongAfter.sub('[[/strong]]', html)
    
    #    labelPre=re.compile(r"<[\t ]*?label[^<>]*>", re.IGNORECASE)
    #    html=labelPre.sub('[[label]]', html)
    #    labelAfter=re.compile(r"<[\t ]*?/label[^<>]*>", re.IGNORECASE)
    #    html=labelAfter.sub('[[/label]]', html)
    #
    #    spanPre=re.compile(r"<[\t ]*?span[^<>]*>", re.IGNORECASE)
    #    html=spanPre.sub('[[span]]', html)
    #    spanAfter=re.compile(r"<[\t ]*?/span[^<>]*>", re.IGNORECASE)
    #    html=spanAfter.sub('[[/span]]', html)
        pPre=re.compile(r"<[\t ]*?/p[^<>]*>[^<>]*<[\t ]*?p[^<>]*>", re.IGNORECASE)
        html=pPre.sub('[[/p]][[p]]', html)
        pAfter=re.compile(r"<[\t ]*?p[^<>]*>(?=[^(\[\[)]*\[\[/p\]\]\[\[p\]\])", re.IGNORECASE)
        html=pAfter.sub('[[p]]', html)
    #
    #    pAfter=re.compile(r"(\[\[p\]\].*>)([^<>]*)(</p>)", re.IGNORECASE)
    #    html=pAfter.sub(repl,html)
    
    
    
        aPre=re.compile(r"<[\t ]*?a[^<>]*>", re.IGNORECASE)
        html=aPre.sub('', html)
        aAfter=re.compile(r"<[\t ]*?/a[^<>]*>", re.IGNORECASE)
        html=aAfter.sub('', html)
    
    #    chardet.detect(html)['encoding']
    
        js=re.compile(r"<(script)[\w\s\S.\u4e00-\u9fa5\uac00-\ud7ff\u30a0-\u30ff\u3040-\u309f]*?</\1>(?s)", re.IGNORECASE)
        html=js.sub('', html)
        css=re.compile(r"<(style)[\w\s\S.\u4e00-\u9fa5\uac00-\ud7ff\u30a0-\u30ff\u3040-\u309f]*?</\1>(?s)", re.IGNORECASE)
        html=css.sub('', html)
    
    
        return html
    def decodeSpecialTag(html):
        html=html.replace("[[strong]]","<strong>")
        html=html.replace("[[/strong]]","</strong>")
        html=html.replace("[[hr /]]","<hr />")
        html=html.replace("[[br /]]","<br />")
        html=html.replace("[[h4]]","<h4>")
        html=html.replace("[[/h4]]","</h4>")
        html=html.replace("[[label]]","<label>")
        html=html.replace("[[/label]]","</label>")
        html=html.replace("[[span]]","<span>")
        html=html.replace("[[/span]]","</span>")
        html=html.replace("[[p]]","<p>")
        html=html.replace("[[/p]]","</p>")
        return html
    
    def getMarkWindowsList(bodySoup):
        #过滤a标签
        nodeQueue=[]
        innerTextNodeList=[]
        nodeQueue.append(bodySoup)
        i=0
        while len(nodeQueue)>0:
            currNode=nodeQueue[0]
            del nodeQueue[0]
            for childNode  in currNode:
                if childNode.string!=None:
                    innerText=childNode.string
                    innerText=innerText.replace('\r\n','').replace('\r','').replace('\n','')
                    tmp=innerText.replace('\t','').replace('  ',' ').replace('  ',' ')
                    tmpInt=len(tmp.replace(" ",""))
                    if tmpInt==0:continue
                    if len(tmp.split(' '))<=2 and len(innerText)-tmpInt>tmpInt:continue
                    if len(tmp)>1  and innerText.find("©")==-1 and innerText.find("&copy;")==-1 :
                        dn=domNode(childNode.parent,childNode,innerText,i)
                        innerTextNodeList.append(dn)
                        i+=1
    #                    print "i",
    #                    print i,
    #                    print "=",
    #                    print childNode
                else:
                    #这里的规则可能有助于垃圾信息的排除
                    if childNode.name!=None and childNode.name=='style':continue
                    if childNode.name!=None and childNode.name=='script':continue
                    if childNode.name!=None and childNode.name=="a" and len(childNode.text)<=2:continue
                    if childNode.name!=None and childNode.parent.name!=None   and  childNode.name=="span" and ( childNode.parent.name=="li" )and len(childNode.text)<=3:
                        continue
                    nodeQueue.append(childNode)
        return innerTextNodeList
    
    #soup=BeautifulSoup(html)
    def test():
        headers={
        'User-Agent':'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36'
        }
        c=requests.get('http://www.xfocus.net/articles/200808/984.html',headers=headers)
        content=c.content.decode('gbk')
        #content=c.text
        # #print(content)
        print(getHTMLContext(content,['']))
    if __name__ == '__main__':
        test()
```
# 效果测试<a id="sec-3" name="sec-3"></a>

{% img /images/webcrawl.png %}
