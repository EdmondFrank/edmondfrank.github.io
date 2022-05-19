---
layout: post
title: "我的算法天梯之路之-归并排序"
date: 2016-08-12 10:44:05 +0800
comments: true
categories: 算法笔记
---
##我的算法天梯之路之-归并排序
插入排序算法采取增量式(Incremental)的策略解决问题,每次添一个元素到已排序的子序列
中,逐渐将整个数组排序完毕,它的时间复杂度是Θ(n^2)。下面介绍另一个典型的排序算法--
归并排序,它采取分而治之(Divide-and-Conquer)的策略,时间复杂度是Θ(nlgn),优于插入
排序算法。归并排序的步骤如下:

1. Divide: 把长度为n的输入序列分成两个长度为n/2的子序列。
2. Conquer: 对这两个子序列分别采用归并排序。
3. Combine: 将两个排序好的子序列合并成一个最终的排序序列。

在描述归并排序的步骤时又调用了归并排序本身,可见这是一个递归的过程。

**示例代码**
```c
 #include <stdio.h>
 #define LEN 8
 int a[LEN] = {5,2,4,7,1,3,2,6};
 void merge(int start,int mid,int end){
     int n1 = mid - start + 1;
     int n2 = end - mid;
     int left[n1],right[n2];
     int i,j,k;
     for (i = 0;i < n1;i++)
     left[i] = a[start+i];
     for (j = 0;j < n2;j++)
     right[j] = a[mid+1+j];
     i = j = 0;
     for (k = start; i < n1 && j < n2; k++){
         if (left[i] < right[j]){
             a[k] = left[i];
             i++;
         }else{
             a[k] = right[j];
             j++;
         }
     }
     if (i < n1)
     for(;i < n1;i++){
         a[k] = left[i];
         k++;
     }
     if (j < n2)
     for (;j < n2;j++){
         a[k] = right[j];
         k++;
     }
 }
 void sort(int start,int end){
     int mid;
     if (start < end){
         mid = (start + end)/2;
         printf("sort (%d-%d,%d-%d) %d,%d,%d,%d,%d,%d,%d,%d\n",
                start,mid,mid+1,end,
                a[0],a[1],a[2],a[3],a[4],a[5],a[6],a[7]);
         sort(start,mid);
         sort(mid+1,end);
         merge(start,mid,end);
         printf("merge (%d-%d,%d-%d) %d,%d,%d,%d,%d,%d,%d,%d\n",
                start,mid,mid+1,end,
                a[0],a[1],a[2],a[3],a[4],a[5],a[6],a[7]);
     }
 }
 int main(){
     sort(0,LEN-1);
     return 0;
 }
```
> 在merge函数中演示了C99的新特性--可变长数组,当然
也可以避免使用这一特性,比如把left和right都按最大长度LEN分配。

###程序耗时
```
$ time ./sort
sort (0-3,4-7) 5,2,4,7,1,3,2,6
sort (0-1,2-3) 5,2,4,7,1,3,2,6
sort (0-0,1-1) 5,2,4,7,1,3,2,6
merge (0-0,1-1) 2,5,4,7,1,3,2,6
sort (2-2,3-3) 2,5,4,7,1,3,2,6
merge (2-2,3-3) 2,5,4,7,1,3,2,6
merge (0-1,2-3) 2,4,5,7,1,3,2,6
sort (4-5,6-7) 2,4,5,7,1,3,2,6
sort (4-4,5-5) 2,4,5,7,1,3,2,6
merge (4-4,5-5) 2,4,5,7,1,3,2,6
sort (6-6,7-7) 2,4,5,7,1,3,2,6
merge (6-6,7-7) 2,4,5,7,1,3,2,6
merge (4-5,6-7) 2,4,5,7,1,2,3,6
merge (0-3,4-7) 1,2,2,3,4,5,6,7

real	0m0.001s
user	0m0.000s
sys	0m0.000s
```

**相比之下冒泡排序在时间复杂度上就较为逊色了,且要排序的元素越多时,差别越大!**

**示例代码**
```c
 #include <stdio.h>
 #define LEN 8
 int a[LEN] = { 5, 2, 4, 7, 1, 3, 2, 6 };
 void sort(int start, int end)
 {
     int i,j,temp;
     if (start < end) {
         for (i = start;i<end;i++){
             for (j=i+1;j<end;j++){
                 if(a[i] > a[j]){
                 temp = a[i];
                 a[i] = a[j];
                 a[j] = temp;
                 }
             }
         }
         printf("sort (%d-%d) %d %d %d %d %d %d %d %d\n",
                start, end,
                a[0], a[1], a[2], a[3], a[4],a[5], a[6], a[7]);

     }
 }
 int main(void)
 {
 sort(0, LEN);
 return 0;
 }
```
###程序耗时
```
$ time ./sort2
sort (0-8) 1 2 2 3 4 5 6 7

real	0m0.002s
user	0m0.000s
sys	0m0.000s
```
