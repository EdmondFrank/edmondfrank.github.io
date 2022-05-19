---
layout: post
title: "Python Keras + LSTM 进行单变量时间序列预测"
date: 2018-02-22 16:06:57 +0800
comments: true
categories: 神经网络与深度学习
---

# Python Keras + LSTM 进行单变量时间序列预测

首先，时间序列预测问题是一个复杂的预测模型问题，它不像一般的回归预测模型。时间序列预测的输入变量是一组按时间顺序的数字序列。它既具有延续性又具有随机性，所以在建模难度上相对回归预测更大。

但同时，正好有一种强大的神经网络适合处理这种存在依赖关系的序列问题：RNN（Recurrent neural networks）。在过去几年中，应用 RNN 在语音识别，语言建模，翻译，图片描述等问题上已经取得一定成功，并且应用领域还在扩展。

## LSTM网络

**Long Short-Term Memory 网络**亦称**LSTM 网络**，是一种在深度学习中应用的循环神经网络。可以学习长期依赖信息。LSTM 由Hochreiter & Schmidhuber (1997)提出，并在近期被Alex Graves进行了改良和推广。在很多问题，LSTM 都取得相当巨大的成功，并得到了广泛的使用。LSTM 通过刻意的设计来避免长期依赖问题。记住长期的信息在实践中是 LSTM 的默认行为，而非需要付出很大代价才能获得的能力。

## 具体应用

下面以一个洗发水销售的例子，来实现LSTM。
首先，你可以在这里[下载](https://datamarket.com/data/set/22r0/sales-of-shampoo-over-a-three-year-period#!ds=22r0&display=line)到本文需要用的数据集。这是一个描述了3年内洗发水的月度销售数量的数据集。

### 数据读取

```python
from pandas import read_csv
from pandas import datetime
from matplotlib import pyplot as plt
def parser(x):
    return datetime.strptime('190'+x,"%Y-%m")
series = read_csv('sales-of-shampoo-over-a-three-ye.csv',
                 header=0,parse_dates=[0],index_col=0, 
                  squeeze=True, date_parser=parser)
print(series.head())
series.plot()
plt.show()
```

### 数据划分

首先我们把数据集划分成两个部分即：训练集和测试集。
那么我们该如何划分呢？因为我们今天研究的是时间序列分析，所以在数据集的划分上我们也应该按照时间来划分。我们可以将前两年的数据作为我们的训练集而将最后一年的数据作为测试集。

```python
# split data into train and test
X = series.values
train, test = X[0:-12], X[-12:]
```

这里我们假设一个滚动预测的情景，又称**前向模型验证（walk-forward model validation）**。其原理很简单，举例来说就像当公司的预测期长达一年时，预测会将已过去的月份排除，而将预测期末的月份补上。好比一月份过去后，我们将其从预测中移除，同时次年的一月份就会作为收尾被添加到预测中以便预测总能保持12个月的完整性。

这样通过使用每月新的洗发水销售量来进行下个月的预测，我们就像模拟了一个更接近于真实世界的场景。

最后，我们将所有在测试集上的预测结果收集起来并计算出他们与真实值的均方根误差（RMSE）以此来作为评估我们模型的基准。

### 持续模型预测(Persistence Model Forecast)

持续性预测的基本思路就是从先前的（t-1）时间序列的结果用于预测当前时间（t）的取值。
那么根据以上的思路，我们可以通过滚动预测的原理从训练集的历史数据中获取最后一次观察值并使用它来预测当前时间的可能取值。


```python
from pandas import read_csv
from pandas import datetime
from sklearn.metrics import mean_squared_error
from math import sqrt
from matplotlib import pyplot
# load dataset
def parser(x):
	return datetime.strptime('190'+x, '%Y-%m')
series = read_csv('sales-of-shampoo-over-a-three-ye.csv', 
                  header=0, parse_dates=[0], index_col=0, 
                  squeeze=True, date_parser=parser)
# split data into train and test
X = series.values
train, test = X[0:-12], X[-12:]
# walk-forward validation
history = [x for x in train]
predictions = list()
for i in range(len(test)):
	# make prediction
	predictions.append(history[-1])
	# observation
	history.append(test[i])
# report performance
rmse = sqrt(mean_squared_error(test, predictions))
print('RMSE: %.3f' % rmse)
# line plot of observed vs predicted
pyplot.plot(test)
pyplot.plot(predictions)
pyplot.show()
```

![persistence_rmse.png](https://i.loli.net/2017/09/07/59b1503007a96.png)


通过持续模型的预测，我们得到了一个最基础的预测模型以及RMSE（baseline）为了提升我们预测模型的效果，下面让我们进入正题来构建LSTM模型来对数据集进行时间序列预测。

### 数据处理

为了能够构建一个LSTM模型对训练集进行训练，我们首先要对数据进行一下处理：

1. 将时间序列问题转化成监督学习问题
2. 平稳时间序列
3. 数据标准化

#### 将时间序列转换成监督学习

对于一个时间序列问题，我们可以通过使用从最后一个（t-1）时刻的观测值作为输入的特征X和当前时刻（t）的观测值作为输出Y来实现转换。

因为，需要转换的是一组时间序列数据，所以无法组合成像真正的监督学习那样有明确一对一映射的输入输出关系。尤其是在数据集的最开始或最后时，两个位置总有一个位置无法在训练集中找到对应关系。为了解决这样的问题，我们通常的做法是，在最开始时将输入特征置为0，而它对应的输出就是时间序列的第一个元素。

```python
from pandas import read_csv
from pandas import datetime
from pandas import DataFrame
from pandas import concat
 
# frame a sequence as a supervised learning problem
def timeseries_to_supervised(data, lag=1):
	df = DataFrame(data)
	columns = [df.shift(i) for i in range(1, lag+1)]
	columns.append(df)
	df = concat(columns, axis=1)
	df.fillna(0, inplace=True)
	return df
 
# load dataset
def parser(x):
	return datetime.strptime('190'+x, '%Y-%m')
series = read_csv('sales-of-shampoo-over-a-three-ye.csv', header=0, parse_dates=[0], index_col=0, squeeze=True, date_parser=parser)
# transform to supervised learning
X = series.values
supervised = timeseries_to_supervised(X, 1)
print(supervised)
```

> 输出结果：
>         0      0
0     0.0  266.0
1   266.0  145.9
2   145.9  183.1
3   183.1  119.3
4   119.3  180.3
5   180.3  168.5
6   168.5  231.8
7   231.8  224.5
8   224.5  192.8
9   192.8  122.9
10  122.9  336.5
11  336.5  185.9
12  185.9  194.3
13  194.3  149.5
14  149.5  210.1
15  210.1  273.3
16  273.3  191.4
17  191.4  287.0
18  287.0  226.0
19  226.0  303.6
20  303.6  289.9
21  289.9  421.6
22  421.6  264.5
23  264.5  342.3
24  342.3  339.7
25  339.7  440.4
26  440.4  315.9
27  315.9  439.3
28  439.3  401.3
29  401.3  437.4
30  437.4  575.5
31  575.5  407.6
32  407.6  682.0
33  682.0  475.3
34  475.3  581.3
35  581.3  646.9

#### 平稳时间序列

虽然不明显，但我们仍可以看出这个洗发水销售数据集在时间上呈上升趋势。因此我们说这个时间序列数据是非平稳的。那么，不平稳怎么办？

答案就是：差分。（有关差分的介绍点击[此处](https://zh.wikipedia.org/wiki/%E5%B7%AE%E5%88%86)）

```python
from pandas import read_csv
from pandas import datetime
from pandas import Series
 
# create a differenced series
def difference(dataset, interval=1):
	diff = list()
	for i in range(interval, len(dataset)):
		value = dataset[i] - dataset[i - interval]
		diff.append(value)
	return Series(diff)
 
# invert differenced value
def inverse_difference(history, yhat, interval=1):
	return yhat + history[-interval]
 
# load dataset
def parser(x):
	return datetime.strptime('190'+x, '%Y-%m')
series = read_csv('sales-of-shampoo-over-a-three-ye.csv', header=0, parse_dates=[0], index_col=0, squeeze=True, date_parser=parser)
print(series.head())
# transform to be stationary
differenced = difference(series, 1)
print(differenced.head())
# invert transform
inverted = list()
for i in range(len(differenced)):
	value = inverse_difference(series, differenced[i], len(series)-i)
	inverted.append(value)
inverted = Series(inverted)
print(inverted.head())
differenced.plot()
plt.show()
```

![diff.png](https://i.loli.net/2017/09/07/59b162fb84ac0.png)

经过一阶差分处理后，从图上看还是挺平稳的。

#### 标准化数据
在数据输入前进行标准化可以非常有效的提升收敛速度和效果。尤其如果我们的激活函数是sigmoid或者tanh，其梯度最大的区间是0附近，当输入值很大或者很小的时候，sigmoid或者tanh的变化就基本平坦了（sigmoid的导数sig（1-sig）会趋于0），也就是进行梯度下降进行优化的时候，梯度会趋于0，而倒是优化速度很慢。

如果输入不进行归一化，由于我们初始化的时候一般都是0均值的的正太分布或者小范围的均匀分布（Xavier），如果输入中存在着尺度相差很大的特征，例如（10000，0.001）这样的，很容易导致激活函数的输入w1*x1+w2*x2+b变的很大或者很小，从而引起梯度趋于0。

而LSTM的默认激活函数就是tanh函数，它的输出范围在-1 到 1 之间，同时这是时间序列数据的首选范围。因此我们可以使用MinMaxScaler类将数据集转换到范围[-1,1]。像其他scikit用于转换数据的方法类一样，它需要以行和列的矩阵格式提供的数据。因此，在转换之前，我们必须重塑NumPy数组。

```python
from pandas import read_csv
from pandas import datetime
from pandas import Series
from sklearn.preprocessing import MinMaxScaler
# load dataset
def parser(x):
	return datetime.strptime('190'+x, '%Y-%m')
series = read_csv('sales-of-shampoo-over-a-three-ye.csv', header=0, parse_dates=[0], index_col=0, squeeze=True, date_parser=parser)
print(series.head())
# transform scale
X = series.values
X = X.reshape(len(X), 1)
scaler = MinMaxScaler(feature_range=(-1, 1))
scaler = scaler.fit(X)
scaled_X = scaler.transform(X)
scaled_series = Series(scaled_X[:, 0])
print(scaled_series.head())
# invert transform
inverted_X = scaler.inverse_transform(scaled_X)
inverted_series = Series(inverted_X[:, 0])
print(inverted_series.head())
```
> 输出结果：
> Month
1901-01-01    266.0
1901-02-01    145.9
1901-03-01    183.1
1901-04-01    119.3
1901-05-01    180.3
Name: Sales of shampoo over a three year period, dtype: float64
0   -0.478585
1   -0.905456
2   -0.773236
3   -1.000000
4   -0.783188
dtype: float64
0    266.0
1    145.9
2    183.1
3    119.3
4    180.3
dtype: float64

### 构建LSTM模型
长短期记忆网络（LSTM）是一种递归神经网络（RNN）。
这类网络的的优点是它能学习并记住较长序列，并不依赖预先指定的窗口滞后观察值作为输入。
在Keras中，这被称为stateful，在定义LSTM网络层时将“stateful”语句设定为“True”。

LSTM层要求输入矩阵格式为：[样本，时间步长，特征]

鉴于训练数据集的形式定义为X输入和y输出，必须先将其转化为样本/时间步长/特征的形式。

#### 完整代码

```python
from pandas import DataFrame
from pandas import Series
from pandas import concat
from pandas import read_csv
from pandas import datetime
from sklearn.metrics import mean_squared_error
from sklearn.preprocessing import MinMaxScaler
from keras.models import Sequential
from keras.layers import Dense
from keras.layers import LSTM
from math import sqrt
from matplotlib import pyplot
import numpy
 
# date-time parsing function for loading the dataset
def parser(x):
	return datetime.strptime('190'+x, '%Y-%m')
 
# frame a sequence as a supervised learning problem
def timeseries_to_supervised(data, lag=1):
	df = DataFrame(data)
	columns = [df.shift(i) for i in range(1, lag+1)]
	columns.append(df)
	df = concat(columns, axis=1)
	df.fillna(0, inplace=True)
	return df
 
# create a differenced series
def difference(dataset, interval=1):
	diff = list()
	for i in range(interval, len(dataset)):
		value = dataset[i] - dataset[i - interval]
		diff.append(value)
	return Series(diff)
 
# invert differenced value
def inverse_difference(history, yhat, interval=1):
	return yhat + history[-interval]
 
# scale train and test data to [-1, 1]
def scale(train, test):
	# fit scaler
	scaler = MinMaxScaler(feature_range=(-1, 1))
	scaler = scaler.fit(train)
	# transform train
	train = train.reshape(train.shape[0], train.shape[1])
	train_scaled = scaler.transform(train)
	# transform test
	test = test.reshape(test.shape[0], test.shape[1])
	test_scaled = scaler.transform(test)
	return scaler, train_scaled, test_scaled
 
# inverse scaling for a forecasted value
def invert_scale(scaler, X, value):
	new_row = [x for x in X] + [value]
	array = numpy.array(new_row)
	array = array.reshape(1, len(array))
	inverted = scaler.inverse_transform(array)
	return inverted[0, -1]
 
# fit an LSTM network to training data
def fit_lstm(train, batch_size, nb_epoch, neurons):
	X, y = train[:, 0:-1], train[:, -1]
	X = X.reshape(X.shape[0], 1, X.shape[1])
	model = Sequential()
	model.add(LSTM(neurons, batch_input_shape=(batch_size, X.shape[1], X.shape[2]), stateful=True))
	model.add(Dense(1))
	model.compile(loss='mean_squared_error', optimizer='adam')
	for i in range(nb_epoch):
		model.fit(X, y, epochs=1, batch_size=batch_size, verbose=0, shuffle=False)
		model.reset_states()
	return model
 
# make a one-step forecast
def forecast_lstm(model, batch_size, X):
	X = X.reshape(1, 1, len(X))
	yhat = model.predict(X, batch_size=batch_size)
	return yhat[0,0]
 
# load dataset
series = read_csv('sales-of-shampoo-over-a-three-ye.csv', header=0, parse_dates=[0], index_col=0, squeeze=True, date_parser=parser)
 
# transform data to be stationary
raw_values = series.values
diff_values = difference(raw_values, 1)
 
# transform data to be supervised learning
supervised = timeseries_to_supervised(diff_values, 1)
supervised_values = supervised.values
 
# split data into train and test-sets
train, test = supervised_values[0:-12], supervised_values[-12:]
 
# transform the scale of the data
scaler, train_scaled, test_scaled = scale(train, test)
 
# fit the model
lstm_model = fit_lstm(train_scaled, 1, 3000, 4)
# forecast the entire training dataset to build up state for forecasting
train_reshaped = train_scaled[:, 0].reshape(len(train_scaled), 1, 1)
lstm_model.predict(train_reshaped, batch_size=1)
 
# walk-forward validation on the test data
predictions = list()
for i in range(len(test_scaled)):
	# make one-step forecast
	X, y = test_scaled[i, 0:-1], test_scaled[i, -1]
	yhat = forecast_lstm(lstm_model, 1, X)
	# invert scaling
	yhat = invert_scale(scaler, X, yhat)
	# invert differencing
	yhat = inverse_difference(raw_values, yhat, len(test_scaled)+1-i)
	# store forecast
	predictions.append(yhat)
	expected = raw_values[len(train) + i + 1]
	print('Month=%d, Predicted=%f, Expected=%f' % (i+1, yhat, expected))

#report performance
rmse = sqrt(mean_squared_error(raw_values[-12:], predictions))
print('Test RMSE: %.3f' % rmse)
# line plot of observed vs predicted
pyplot.plot(raw_values[-12:])
pyplot.plot(predictions)
pyplot.show()
```
![lstm_pred.png](https://i.loli.net/2017/09/07/59b16bc803033.png)

最后运行结果打印出测试数据集12个月份中每个月份的预期和预测销量。示例还打印了所有预测值得均方根误差。该模型显示洗发水月度销量的均方根误差为111.925，好于持续性模型得出的对应结果136.761。

**另外**，神经网络的一个难题是初始条件不同，它们给出结果就不同。一种解决办法是修改Keras使用的随机数种子值以确保结果可复制。另一种办法是使用不同的实验设置控制随机初始条件。
