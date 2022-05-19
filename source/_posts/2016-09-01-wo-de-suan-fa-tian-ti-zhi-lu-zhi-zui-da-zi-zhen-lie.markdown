---
layout: post
title: "我的算法天梯之路之-最大子阵列"
date: 2016-09-01 17:51:01 +0800
comments: true
categories: 算法笔记
---
##我的算法天梯之路之-最大子阵列
>
在一个数组中找出和最大的连续几个数。（至少包含一个数）<br/>
例如：<br/>
数组A[] = [−2, 1, −3, 4, −1, 2, 1, −5, 4]，则连续的子序列[4,−1,2,1]有最大的和6.<br/>
输入格式<br/>
第一行输入一个不超过1000的整数n。<br/>
第二行输入n个整数A[i]。<br/>
输出格式<br/>
第一行输出一个整数，表示最大的和。<br/>

**样例输入**

>
3<br/>
1 1 -2<br/>
**样例输出**
<br/>
2<br/>

**示例代码**
```c
 #include <stdio.h>
 #define N 1001
 int maxsum(int a[],int n){
    int sum = a[0];
    int b = 0;
    for (int i = 0;i < n;i++){
        if(b>=0) b+=a[i]; else b=a[i];
        if(b>sum)
        sum = b;
    }
    return sum;
 }
 int main(){
    int n,a[N];
    scanf("%d",&n);
    for(int i=0;i<n;i++)
    scanf("%d",&a[i]);

    printf("%d",maxsum(a,n));
    return 0;
 }
```


