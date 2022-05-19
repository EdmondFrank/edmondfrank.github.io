---
layout: post
title: "贝叶斯算法在检测群聊垃圾广告中的应用"
date: 2017-07-03 12:18:45 +0800
comments: true
categories: 机器学习
---

<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. 背景：</a></li>
<li><a href="#sec-2">2. 基本原理：</a></li>
<li><a href="#sec-3">3. 联合概率计算</a></li>
<li><a href="#sec-4">4. 算法实现</a></li>
</ul>
</div>
</div>

# 贝叶斯算法在检测群聊垃圾广告中的应用

# 背景：<a id="sec-1" name="sec-1"></a>

贝叶斯过滤器是一种基于统计学的过滤器方法，是建立在已有的统计结果之上的，所以在实现算法
之前需要先建立历史资料库，即，先提供两组已经标注好的训练数据。
在此附上训练数据链接：<https://pan.baidu.com/s/1nuGW2Ul>

# 基本原理：<a id="sec-2" name="sec-2"></a>

当看到一段文本时，我们先假定它是广告文本的概率为50%，其实整个识别模式就像垃圾邮件过滤
一样，我们先用S来表示垃圾文本，用H来表示正常文本。因此，P(S)和 P(H)的先验概率都是
50%：

<p><script type="math/tex" id="MathJax-Element-115"> P(S) = P(H) = 50% </script></p>

然后我们再对其进行分词解析处理，我们用W来表示其中存在的某个关键词，然后问题就转变成了在
某个词语W存在的情况之下，目标文本为垃圾文本的可能性有多大？而解决这个问题的关键就是计算
P（S|W）的值。根据条件概率公式我们可以写出以下等式。

<p><script type="math/tex" id="MathJax-Element-116"> P(S|W) = P(W|S)P(S)/(P(W|S)P(S)+P(W|H)P(H)) </script></p>

公式之中，P(W|S)和P(W|H)分别代表在正常文本和垃圾文本之中，词语W出现的概率，而这个概率
我们可以根据已经标注的好训练数据中计算得出。这里，我们假设对于词语W来说，上面所提到的两个
概率分别为5%和0.05%，那么我们可以计算出P(S|W)＝ 99.0%

因此，根据我们得出的99%的后验概率，我们可以说词语W的推断能力很强，在垃圾文本和正常文本之
中有十分良好的推断效果。

# 联合概率计算<a id="sec-3" name="sec-3"></a>

但是，一段文本中存在非常多的词汇，我们不能单凭一个单词就推断出这段文本的分类属性。因此，
常规的做法是我们需要选取出整段文本中，P(S|W)最高的15个词，然后计算它们的联合概率。
（其中可能存在的问题有：某些新词在历史数据中都不曾出现过，我们无法计算其P(S|W)的值，
对于这样的问题需要用到贝叶斯平滑的思想进行处理，本文为了从简，我们都先假设这类词的P(S|W)
值为0.4）

对于联合概率的补充解释：联合概率是指在多个事件发生的情况之下，另一个发生的概率有多大。
例如：已知，W<sub>1</sub> 和 W<sub>2</sub>为两个不同的词语，它们都出现在某段文本之中，那么这段文本为广告
文本的概率就是W<sub>1</sub>和W<sub>2</sub>的联合概率。

最后，根据提取的15个词，得出最终的概率计算公式

<p><script type="math/tex" id="MathJax-Element-118"> P ＝ P_1P_2P_3…P_{15}/(P_1P_2…P_{15}+(1-P_1)(1-P_2)…(1-P_{15})) </script></p>

在得出最后的概率后，再对阈值（门槛值）进行比较，如：0.9,若 > 0.9 则15个词联合
认定为90%为垃圾文本！

# 算法实现<a id="sec-4" name="sec-4"></a>
```python
    #encoding=utf-8
    import re
    import pickle
    #spam类对象
    import jieba;
    import os;
    class spamEmailBayes:
        #获得停用词表
        def getStopWords(self):
            stopList=[]
            for line in open("../data/stopwords"):
                stopList.append(line[:len(line)-1])
            return stopList;
        #获得词典
        def get_word_list(self,content,wordsList,stopList):
            #分词结果放入res_list
            res_list = list(jieba.cut(content))
            for i in res_list:
                if i not in stopList and i.strip()!='' and i!=None:
                    if i not in wordsList:
                        wordsList.append(i)
    
        #若列表中的词已在词典中，则加1，否则添加进去
        def addToDict(self,wordsList,wordsDict):
            for item in wordsList:
                if item in wordsDict.keys():
                    wordsDict[item]+=1
                else:
                    wordsDict.setdefault(item,1)
    
        def get_File_List(self,filePath):
            filenames=os.listdir(filePath)
            return filenames
    
        #通过计算每个文件中p(s|w)来得到对分类影响最大的15个词
        def getTestWords(self,testDict,spamDict,normDict,normFilelen,spamFilelen):
            wordProbList={}
            for word,num  in testDict.items():
                if word in spamDict.keys() and word in normDict.keys():
                    #该文件中包含词个数
                    pw_s=spamDict[word]/spamFilelen
                    pw_n=normDict[word]/normFilelen
                    ps_w=pw_s/(pw_s+pw_n)
                    wordProbList.setdefault(word,ps_w)
                if word in spamDict.keys() and word not in normDict.keys():
                    pw_s=spamDict[word]/spamFilelen
                    pw_n=0.01
                    ps_w=pw_s/(pw_s+pw_n)
                    wordProbList.setdefault(word,ps_w)
                if word not in spamDict.keys() and word in normDict.keys():
                    pw_s=0.01
                    pw_n=normDict[word]/normFilelen
                    ps_w=pw_s/(pw_s+pw_n)
                    wordProbList.setdefault(word,ps_w)
                if word not in spamDict.keys() and word not in normDict.keys():
                    #若该词不在脏词词典中，概率设为0.4
                    wordProbList.setdefault(word,0.4)
            sorted(wordProbList.items(),key=lambda d:d[1],reverse=True)[0:15]
            return (wordProbList)
    
        #计算贝叶斯概率
        def calBayes(self,wordList,spamdict,normdict):
            ps_w=1
            ps_n=1
    
            for word,prob in wordList.items() :
                print(word+"/"+str(prob))
                ps_w*=(prob)
                ps_n*=(1-prob)
            p=ps_w/(ps_w+ps_n)
    #         print(str(ps_w)+"////"+str(ps_n))
            return p
    
        #计算预测结果正确率
        def calAccuracy(self,testResult):
            rightCount=0
            errorCount=0
            for name ,catagory in testResult.items():
                if (int(name)<1000 and catagory==0) or(int(name)>1000 and catagory==1):
                    rightCount+=1
                else:
                    errorCount+=1
            return rightCount/(rightCount+errorCount)
    
    spam=spamEmailBayes()
    #保存词频的词典
    spamDict={}
    normDict={}
    testDict={}
    #保存每封邮件中出现的词
    wordsList=[]
    wordsDict={}
    #保存预测结果,key为文件名，值为预测类别
    testResult={}
    #分别获得正常邮件、垃圾邮件及测试文件名称列表
    normFileList=spam.get_File_List("../data/normal")
    spamFileList=spam.get_File_List("../data/spam")
    testFileList=spam.get_File_List("../data/test2")
    #获取训练集中正常邮件与垃圾邮件的数量
    normFilelen=len(normFileList)
    spamFilelen=len(spamFileList)
    #获得停用词表，用于对停用词过滤
    stopList=spam.getStopWords()
    #获得正常邮件中的词频
    for fileName in normFileList:
        wordsList.clear()
        for line in open("../data/normal/"+fileName,encoding='gbk'):
            #过滤掉非中文字符
            rule=re.compile(r"[^\u4e00-\u9fa5]")
            line=rule.sub("",line)
            #将每封邮件出现的词保存在wordsList中
            spam.get_word_list(line,wordsList,stopList)
        #统计每个词在所有邮件中出现的次数
        spam.addToDict(wordsList, wordsDict)
    normDict=wordsDict.copy()
    
    output = open('norm.pkl','wb')
    pickle.dump(normDict,output,-1)
    output.close()
    
    #获得垃圾邮件中的词频
    wordsDict.clear()
    for fileName in spamFileList:
        wordsList.clear()
        for line in open("../data/spam/"+fileName,encoding='gbk'):
            rule=re.compile(r"[^\u4e00-\u9fa5]")
            line=rule.sub("",line)
            spam.get_word_list(line,wordsList,stopList)
        spam.addToDict(wordsList, wordsDict)
    spamDict=wordsDict.copy()
    
    output = open('spam.pkl','wb')
    pickle.dump(spamDict,output,-1)
    output.close()
    
    output = open('model.pkl','wb')
    pickle.dump(spam,output,-1)
    output.close()
    
    # 测试邮件
    for fileName in testFileList:
        testDict.clear( )
        wordsDict.clear()
        wordsList.clear()
        for line in open("../data/test2/"+fileName):
        #for line in open("../data/test/"+fileName,encoding='gbk'):
            rule=re.compile(r"[^\u4e00-\u9fa5]")
            line=rule.sub("",line)
            spam.get_word_list(line,wordsList,stopList)
        spam.addToDict(wordsList, wordsDict)
        testDict=wordsDict.copy()
        #通过计算每个文件中p(s|w)来得到对分类影响最大的15个词
        wordProbList=spam.getTestWords(testDict, spamDict,normDict,normFilelen,spamFilelen)
        #对每封邮件得到的15个词计算贝叶斯概率
        p=spam.calBayes(wordProbList, spamDict, normDict)
        if(p>0.9):
            testResult.setdefault(fileName,1)
        else:
            testResult.setdefault(fileName,0)
    #计算分类准确率（测试集中文件名低于1000的为正常邮件）
    testAccuracy=spam.calAccuracy(testResult)
    for i,ic in testResult.items():
        print(i+"/"+str(ic))
    print(testAccuracy)
```
