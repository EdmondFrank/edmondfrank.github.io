---
layout: post
title: "K-近邻算法-Part2"
date: 2017-08-23 12:19:17 +0800
comments: true
categories: 机器学习
---


# K-近邻算法-Part2

## 使用交叉验证来调整k值

通常来说,一个最优的KNN模型其k参数所对应的预估错误率应该是最低的。因此，在选定模型k值的时候应该反复尝试不同的k值在预估上的效果，对比其错误率。初学者在这里为了降低模型的误差通常会将全部样本数据也作为训练集一起代入模型进行训练。虽然这种做法在训练时确实能够有效降低误差，对现有数据进行更好的拟合。但是，同时带来的后果是：我们会将数据的各种无法避免的真实误差，如测量误差，抽样误差等也训练进了我们的模型之中，使得训练出来的模型在新数据或未知数据上的预估效果特别差，这种现象也被称为**过拟合（overfitting）**。


为了降低预估的错误率以及避免过拟合现象的发生，我们可以在某种意义下将**原始数据(dataset)**进行分组,一部分做为**训练集(train set)**,另一部分做为**验证集(validation set or test set)**，首先用训练集对分类器进行训练,再利用验证集来测试训练得到的**模型(model)**,以此来做为评价分类器的性能指标。这种方法也就是所谓的**交叉验证（Cross Validation）**。


而在交叉验证中，**K折交叉验证（k-fold cross validation）**是比较常用的。其主要思想是：初始采样分割成K个子样本，一个单独的子样本被保留作为验证模型的数据，其他K-1个样本用来训练。交叉验证重复K次，每个子样本验证一次，平均K次的结果或者使用其它结合方式，最终得到一个单一估测。这个方法的优势在于，同时重复运用随机产生的子样本进行训练和验证，每次的结果验证一次，10折交叉验证是最常用的。


![enter image description here](https://kevinzakka.github.io/assets/k_fold_cv.jpg)


为了更好的理解k折交叉验证，我们继续沿用part1部分的训练数据，使用10折交叉验证的方法来调整我们的k值。

```python

import matplotlib.pyplot as plt
from sklearn.cross_validation import cross_val_score
# creating odd list of K for KNN
myList = list(range(1,50))

# subsetting just the odd ones
neighbors = list(filter(lambda x: x % 2 != 0, myList))

# empty list that will hold cv scores
cv_scores = []

# perform 10-fold cross validation
for k in neighbors:
    knn = KNeighborsClassifier(n_neighbors=k)
    scores = cross_val_score(knn, X_train, y_train, cv=10, scoring='accuracy')
    cv_scores.append(scores.mean())
    
# changing to misclassification error
MSE = [1 - x for x in cv_scores]

# determining best k
optimal_k = neighbors[MSE.index(min(MSE))]
print ("The optimal number of neighbors is %d" % optimal_k)

# plot misclassification error vs k
plt.plot(neighbors, MSE)
plt.xlabel('Number of Neighbors K')
plt.ylabel('Misclassification Error')
plt.show()
```
在上面的程序中，我们在1-50的奇数中选取k值，并根据k值训练模型进行预估验证。最后根据不同k值训练出去的模型的**均方误差（Mean Squared Error, MSE）**作出折线图，并求出使得MSE最小的最优k值。

> 输出结果:
> 
> The optimal number of neighbors is 7
![enter image description here](https://kevinzakka.github.io/assets/cv_knn.png)

由此我们可以得出：在这个模型中，10折交叉验证告诉我们最优的k值是7。

## 尝试自己实现KNN算法

到目前为止，我们都是调用sklearn库中的KNN来完成分类任务。那么，下面我们来尝试自己实现一个简单的KNN算法并用它来分类我们之前的数据。

经过上一篇文章的介绍，我们可以知道KNN算法的关键是计算出新的**待分类数据**与现有的样本数据的**“距离”**，而其中较为常用的还是**欧式距离（euclidean distance ）**。然后提取出k个最相邻的点，并根据他们大多数点的分类属性来给待分类点进行归类。


因此核心计算代码我们可以这样写：


```python
def predict(X_train, y_train, x_test, k):
	# create list for distances and targets
	distances = []
	targets = []

	for i in range(len(X_train)):
		# first we compute the euclidean distance
		distance = np.sqrt(np.sum(np.square(x_test - X_train[i, :])))
		# add it to list of distances
		distances.append([distance, i])

	# sort the list
	distances = sorted(distances)

	# make a list of the k neighbors' targets
	for i in range(k):
		index = distances[i][1]
		targets.append(y_train[index])

	# return most common target
	return Counter(targets).most_common(1)[0][0]
```

在上面的代码中，我们首先创建一个保存距离的数组，并在存储完计算出的待测点与各样本点的距离后对数组进行升序排列，然后取出前k个最接近待测点的样本点，返回其出现最多的分类标签。


然后接下来让我继续完成整个KNN算法。


```python
def kNearestNeighbor(X_train, y_train, X_test, predictions, k):

	# loop over all observations
	for i in range(len(X_test)):
		predictions.append(predict(X_train, y_train, X_test[i, :], k))
```


使用我们上面得出最优的 k = 7作为参数生成模型并进行预估。

```python
# making our predictions 
from sklearn.metrics import accuracy_score
predictions = []

kNearestNeighbor(X_train, y_train, X_test, predictions, 7)

# transform the list into an array
predictions = np.asarray(predictions)
#print(y_test,predictions)
# evaluating accuracy
#for i in range(predictions.size):
#    print(predictions.tolist()[i],list(y_test)[i])
accuracy = accuracy_score(y_test, predictions)
print('\nThe accuracy of our classifier is %d%%' % int(accuracy*100))
```
> 输出结果：
> The accuracy of our classifier is 98%

到此，我们基本已经完成了一个类似于sklearn库中的KNN算法了，并且还有不错的准确率。

## 小结

最后，KNN算法介绍性文章到这就结束了。我们来总结一下KNN算法的优点与不足。

#### 优点
+ 易于理解
+ 无需训练
+ 容易迁移至多分类情况

#### 不足
+ 计算量大，时间复杂度随数据规模增大而增大
+ 分类情况容易受高频分类影响

