---
layout: post
title: "计算编辑距离(Levenshtein距离)"
date: 2017-03-22 17:33:02 +0800
comments: true
categories: 算法笔记
---
# 计算字符串的相似度（编辑距离）

## Levenshtein 距离

Levenshtein距离，又称编辑距离，指的是两个字符串之间，由一个转换成另外一个所需的最少编辑量。
编辑距离算法首先由俄国科学家Levenshstance提出，原理可大致举例如下：
> 字符串A：abcdefg
> 字符串B：abcdef

以上两串字符串可以通过添加或是删除字符“g”的方式达到一致的目的。这两种方案都需要一次操作。

## 算法实现
此问题可以采用经典的动态规划求解。

## 计算两字符串的最长公共子序列相似

设Ai为字符串A(a1a2a3 … am)的前i个字符（即为a1,a2,a3 … ai）
设Bj为字符串B(b1b2b3 … bn)的前j个字符（即为b1,b2,b3 … bj）

设 L(i,j)为使两个字符串和Ai和Bj相等的最小操作次数。
当ai==bj时 显然 
> L(i,j) = L(i-1,j-1)

当ai!=bj时 
>
若将它们修改为相等，则对两个字符串至少还要操作L(i-1,j-1)次
若删除ai或在bj后添加ai，则对两个字符串至少还要操作L(i-1,j)次
若删除bj或在ai后添加bj，则对两个字符串至少还要操作L(i,j-1)次
此时L(i,j) = min( L(i-1,j-1), L(i-1,j), L(i,j-1) ) + 1 

所以，L(i,0)=i，L(0,j)=j, 再利用上述的递推公式，可以直接计算出L(i,j)值。

> 算法实现


```c
#include <iostream>
#include <string>
using namespace std;
int calcdistance(string,string);
int minvalue(int,int,int);
int main(int argc, char *argv[])
{
    string str1 ,str2;
    cout<<"Please input 2 strings:"<<endl;
    cin>>str1>>str2;
    cout<<str1<<endl;
    cout<<str2<<endl;
    cout<<calcdistance(str1,str2)<<endl;
    //cout << "Hello World!" << endl;
    return 0;
}
int calcdistance(string s1, string s2)
{
    int len1 = s1.size()+1;
    int len2 = s2.size()+1;
    if(len1==0)
        return len2;
    if(len2==0)
        return len1;

    int ** cnt = new int*[len1];
    for(int i=0;i<len1;i++)
        cnt[i]=new int[len2];

    for(int i=0;i<len1;i++)cnt[i][0]=i;
    for(int j=0;j<len2;j++)cnt[0][j]=j;
    //cnt[0][0] = 0;

    for(int i=1;i<len1;i++)
    {
        for(int j=1;j<len2;j++){
            if(s1[i-1]==s2[j-1])
                cnt[i][j]=cnt[i-1][j-1];
            else
            {
                cnt[i][j]=minvalue(cnt[i-1][j-1],
                        cnt[i-1][j],
                        cnt[i][j-1])+1;
            }
        }
    }
    int ret = cnt[len1-1][len2-1];
    for(int i=0;i<len1;i++)
        delete [] cnt[i];
    delete [] cnt;
    return ret;
}
int minvalue(int a, int b, int c){
 int t = a <=b ? a : b;
 return t <= c? t : c;
}
```


> Vector版本如下


```c
#include <iostream>
#include <string>
#include <vector>
using namespace std;
int calcdistance(string,string);
int main(int argc, char *argv[])
{
    string str1 ,str2;
    cout<<"Please input 2 strings:"<<endl;
    cin>>str1>>str2;
    cout<<str1<<endl;
    cout<<str2<<endl;
    cout<<calcdistance(str1,str2)<<endl;
    //cout << "Hello World!" << endl;
    return 0;
}
int calcdistance(string str1,string str2)
{
    int n = str1.size();
    int m = str2.size();
    if ( n == 0)
        return m;
    if ( m == 0)
        return n;
    vector<int> vec1(n+1);
    vector<int> vec2(m+1);
    for(int i=0;i<=n;i++)
        vec1[i] = i;
    int cost = 0;

    for(int i=1;i<=n;i++)
    {
        vec2[0] = i;
        for(int j=1;j<=m;j++)
        {
            if( str1[i-1] == str2[j-1] )
                cost = 0;
            else
                cost = 1;
            vec2[j] = vec2[j-1]+1 < vec1[j]+1 ? vec2[j-1]+1 : vec1[j]+1;
            vec2[j] = vec2[j] < vec1[j-1]+cost ? vec2[j] : vec1[j-1]+cost;
        }
        vec1 = vec2;
    }
    return vec2.back();
}

```
