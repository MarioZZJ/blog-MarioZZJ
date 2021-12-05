---
title: 机器学习实验：回归 | Leeml-2020-hw1
comments: true
copyright: true
abbrlink: 28d53052
date: 2021-12-05 19:50:23
updated: 2021-12-05 19:50:23
tags: 
    - ML
    - Regression
categories: 数科知识
keywords: Regression
description: 回归实验——来自李宏毅机器学习课程 ML2020-HW1 
top_img: https://download.mariozzj.cn/img/picgo/202111162100438.png
cover: https://download.mariozzj.cn/img/picgo/202111162100438.png
copyright_author: 
copyright_author_href:
copyright_url:
copyright_info:
katex: true
aside: true
---

# Overview

实验已作为 Kaggle 竞赛发布，见 [Kaggle](https://www.kaggle.com/c/ml2020spring-hw1/)。涉及的主要内容：

* 通过完成一次时间序列预测任务，回顾回归相关知识
* 梯度下降的步骤，求解方法
* 学习率参数选择
* 特征缩放对学习效果的影响
* 最小二乘法直接求解回归问题

# EDA

> 任务说明：训练集数据提供丰原气象站某年每月前 20 天每小时的 18 项指标数值。测试集给出余下天数的前 9 小时的所有指标数值，要求预测出第 10 小时的 PM2.5 数值。


## load data


```python
TASK_DIR = ROOT_DIR + 'ml2020spring-hw1/'
data_train = pd.read_csv(TASK_DIR + "train.csv",encoding = 'big5')
data_test  = pd.read_csv(TASK_DIR + "test.csv" ,encoding = 'big5',names = ['日期', '測項', '0', '1', '2', '3', '4', '5', '6', '7', '8'])
```


```python
data_train.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 4320 entries, 0 to 4319
    Data columns (total 27 columns):
     #   Column  Non-Null Count  Dtype 
    ---  ------  --------------  ----- 
     0   日期      4320 non-null   object
     1   測站      4320 non-null   object
     2   測項      4320 non-null   object
     3   0       4320 non-null   object
     4   1       4320 non-null   object
     5   2       4320 non-null   object
     6   3       4320 non-null   object
     7   4       4320 non-null   object
     8   5       4320 non-null   object
     9   6       4320 non-null   object
     10  7       4320 non-null   object
     11  8       4320 non-null   object
     12  9       4320 non-null   object
     13  10      4320 non-null   object
     14  11      4320 non-null   object
     15  12      4320 non-null   object
     16  13      4320 non-null   object
     17  14      4320 non-null   object
     18  15      4320 non-null   object
     19  16      4320 non-null   object
     20  17      4320 non-null   object
     21  18      4320 non-null   object
     22  19      4320 non-null   object
     23  20      4320 non-null   object
     24  21      4320 non-null   object
     25  22      4320 non-null   object
     26  23      4320 non-null   object
    dtypes: object(27)
    memory usage: 911.4+ KB



```python
data_train.head(20)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>日期</th>
      <th>測站</th>
      <th>測項</th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>...</th>
      <th>14</th>
      <th>15</th>
      <th>16</th>
      <th>17</th>
      <th>18</th>
      <th>19</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>AMB_TEMP</td>
      <td>14</td>
      <td>14</td>
      <td>14</td>
      <td>13</td>
      <td>12</td>
      <td>12</td>
      <td>12</td>
      <td>...</td>
      <td>22</td>
      <td>22</td>
      <td>21</td>
      <td>19</td>
      <td>17</td>
      <td>16</td>
      <td>15</td>
      <td>15</td>
      <td>15</td>
      <td>15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>CH4</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>...</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>CO</td>
      <td>0.51</td>
      <td>0.41</td>
      <td>0.39</td>
      <td>0.37</td>
      <td>0.35</td>
      <td>0.3</td>
      <td>0.37</td>
      <td>...</td>
      <td>0.37</td>
      <td>0.37</td>
      <td>0.47</td>
      <td>0.69</td>
      <td>0.56</td>
      <td>0.45</td>
      <td>0.38</td>
      <td>0.35</td>
      <td>0.36</td>
      <td>0.32</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>NMHC</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.13</td>
      <td>0.12</td>
      <td>0.11</td>
      <td>0.06</td>
      <td>0.1</td>
      <td>...</td>
      <td>0.1</td>
      <td>0.13</td>
      <td>0.14</td>
      <td>0.23</td>
      <td>0.18</td>
      <td>0.12</td>
      <td>0.1</td>
      <td>0.09</td>
      <td>0.1</td>
      <td>0.08</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>NO</td>
      <td>0.9</td>
      <td>0.6</td>
      <td>0.5</td>
      <td>1.7</td>
      <td>1.8</td>
      <td>1.5</td>
      <td>1.9</td>
      <td>...</td>
      <td>2.5</td>
      <td>2.2</td>
      <td>2.5</td>
      <td>2.3</td>
      <td>2.1</td>
      <td>1.9</td>
      <td>1.5</td>
      <td>1.6</td>
      <td>1.8</td>
      <td>1.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>NO2</td>
      <td>16</td>
      <td>9.2</td>
      <td>8.2</td>
      <td>6.9</td>
      <td>6.8</td>
      <td>3.8</td>
      <td>6.9</td>
      <td>...</td>
      <td>11</td>
      <td>11</td>
      <td>22</td>
      <td>28</td>
      <td>19</td>
      <td>12</td>
      <td>8.1</td>
      <td>7</td>
      <td>6.9</td>
      <td>6</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>NOx</td>
      <td>17</td>
      <td>9.8</td>
      <td>8.7</td>
      <td>8.6</td>
      <td>8.5</td>
      <td>5.3</td>
      <td>8.8</td>
      <td>...</td>
      <td>14</td>
      <td>13</td>
      <td>25</td>
      <td>30</td>
      <td>21</td>
      <td>13</td>
      <td>9.7</td>
      <td>8.6</td>
      <td>8.7</td>
      <td>7.5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>O3</td>
      <td>16</td>
      <td>30</td>
      <td>27</td>
      <td>23</td>
      <td>24</td>
      <td>28</td>
      <td>24</td>
      <td>...</td>
      <td>65</td>
      <td>64</td>
      <td>51</td>
      <td>34</td>
      <td>33</td>
      <td>34</td>
      <td>37</td>
      <td>38</td>
      <td>38</td>
      <td>36</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>PM10</td>
      <td>56</td>
      <td>50</td>
      <td>48</td>
      <td>35</td>
      <td>25</td>
      <td>12</td>
      <td>4</td>
      <td>...</td>
      <td>52</td>
      <td>51</td>
      <td>66</td>
      <td>85</td>
      <td>85</td>
      <td>63</td>
      <td>46</td>
      <td>36</td>
      <td>42</td>
      <td>42</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>PM2.5</td>
      <td>26</td>
      <td>39</td>
      <td>36</td>
      <td>35</td>
      <td>31</td>
      <td>28</td>
      <td>25</td>
      <td>...</td>
      <td>36</td>
      <td>45</td>
      <td>42</td>
      <td>49</td>
      <td>45</td>
      <td>44</td>
      <td>41</td>
      <td>30</td>
      <td>24</td>
      <td>13</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>RAINFALL</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>...</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
      <td>NR</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>RH</td>
      <td>77</td>
      <td>68</td>
      <td>67</td>
      <td>74</td>
      <td>72</td>
      <td>73</td>
      <td>74</td>
      <td>...</td>
      <td>47</td>
      <td>49</td>
      <td>56</td>
      <td>67</td>
      <td>72</td>
      <td>69</td>
      <td>70</td>
      <td>70</td>
      <td>70</td>
      <td>69</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>SO2</td>
      <td>1.8</td>
      <td>2</td>
      <td>1.7</td>
      <td>1.6</td>
      <td>1.9</td>
      <td>1.4</td>
      <td>1.5</td>
      <td>...</td>
      <td>3.9</td>
      <td>4.4</td>
      <td>9.9</td>
      <td>5.1</td>
      <td>3.4</td>
      <td>2.3</td>
      <td>2</td>
      <td>1.9</td>
      <td>1.9</td>
      <td>1.9</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>THC</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>1.9</td>
      <td>1.9</td>
      <td>1.8</td>
      <td>1.9</td>
      <td>...</td>
      <td>1.9</td>
      <td>1.9</td>
      <td>1.9</td>
      <td>2.1</td>
      <td>2</td>
      <td>1.9</td>
      <td>1.9</td>
      <td>1.9</td>
      <td>1.9</td>
      <td>1.9</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>WD_HR</td>
      <td>37</td>
      <td>80</td>
      <td>57</td>
      <td>76</td>
      <td>110</td>
      <td>106</td>
      <td>101</td>
      <td>...</td>
      <td>307</td>
      <td>304</td>
      <td>307</td>
      <td>124</td>
      <td>118</td>
      <td>121</td>
      <td>113</td>
      <td>112</td>
      <td>106</td>
      <td>110</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>WIND_DIREC</td>
      <td>35</td>
      <td>79</td>
      <td>2.4</td>
      <td>55</td>
      <td>94</td>
      <td>116</td>
      <td>106</td>
      <td>...</td>
      <td>313</td>
      <td>305</td>
      <td>291</td>
      <td>124</td>
      <td>119</td>
      <td>118</td>
      <td>114</td>
      <td>108</td>
      <td>102</td>
      <td>111</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>WIND_SPEED</td>
      <td>1.4</td>
      <td>1.8</td>
      <td>1</td>
      <td>0.6</td>
      <td>1.7</td>
      <td>2.5</td>
      <td>2.5</td>
      <td>...</td>
      <td>2.5</td>
      <td>2.2</td>
      <td>1.4</td>
      <td>2.2</td>
      <td>2.8</td>
      <td>3</td>
      <td>2.6</td>
      <td>2.7</td>
      <td>2.1</td>
      <td>2.1</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2014/1/1</td>
      <td>豐原</td>
      <td>WS_HR</td>
      <td>0.5</td>
      <td>0.9</td>
      <td>0.6</td>
      <td>0.3</td>
      <td>0.6</td>
      <td>1.9</td>
      <td>2</td>
      <td>...</td>
      <td>2.1</td>
      <td>2.1</td>
      <td>1.9</td>
      <td>1</td>
      <td>2.5</td>
      <td>2.5</td>
      <td>2.8</td>
      <td>2.6</td>
      <td>2.4</td>
      <td>2.3</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2014/1/2</td>
      <td>豐原</td>
      <td>AMB_TEMP</td>
      <td>16</td>
      <td>15</td>
      <td>15</td>
      <td>14</td>
      <td>14</td>
      <td>15</td>
      <td>16</td>
      <td>...</td>
      <td>24</td>
      <td>24</td>
      <td>23</td>
      <td>21</td>
      <td>20</td>
      <td>19</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2014/1/2</td>
      <td>豐原</td>
      <td>CH4</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>...</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
    </tr>
  </tbody>
</table>
<p>20 rows × 27 columns</p>
</div>



训练集由每月前 20 天每小时的 18 项观测指标组成，除 `RAINFALL` 观测指标外其余指标均为数值型。下载观测整个数据集发现，`RAINFALL` 指标值不为 `NR` 时，以数值型记录了该小时的降雨量，所以**需要对 `NR` 值进行处理，将其改为 0**。 


```python
data_test
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>日期</th>
      <th>測項</th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>id_0</td>
      <td>AMB_TEMP</td>
      <td>21</td>
      <td>21</td>
      <td>20</td>
      <td>20</td>
      <td>19</td>
      <td>19</td>
      <td>19</td>
      <td>18</td>
      <td>17</td>
    </tr>
    <tr>
      <th>1</th>
      <td>id_0</td>
      <td>CH4</td>
      <td>1.7</td>
      <td>1.7</td>
      <td>1.7</td>
      <td>1.7</td>
      <td>1.7</td>
      <td>1.7</td>
      <td>1.7</td>
      <td>1.7</td>
      <td>1.8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>id_0</td>
      <td>CO</td>
      <td>0.39</td>
      <td>0.36</td>
      <td>0.36</td>
      <td>0.4</td>
      <td>0.53</td>
      <td>0.55</td>
      <td>0.34</td>
      <td>0.31</td>
      <td>0.23</td>
    </tr>
    <tr>
      <th>3</th>
      <td>id_0</td>
      <td>NMHC</td>
      <td>0.16</td>
      <td>0.24</td>
      <td>0.22</td>
      <td>0.27</td>
      <td>0.27</td>
      <td>0.26</td>
      <td>0.27</td>
      <td>0.29</td>
      <td>0.1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>id_0</td>
      <td>NO</td>
      <td>1.3</td>
      <td>1.3</td>
      <td>1.3</td>
      <td>1.3</td>
      <td>1.4</td>
      <td>1.6</td>
      <td>1.2</td>
      <td>1.1</td>
      <td>0.9</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>4315</th>
      <td>id_239</td>
      <td>THC</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.7</td>
      <td>1.7</td>
      <td>1.7</td>
      <td>1.7</td>
      <td>1.7</td>
    </tr>
    <tr>
      <th>4316</th>
      <td>id_239</td>
      <td>WD_HR</td>
      <td>80</td>
      <td>92</td>
      <td>95</td>
      <td>95</td>
      <td>96</td>
      <td>97</td>
      <td>96</td>
      <td>96</td>
      <td>84</td>
    </tr>
    <tr>
      <th>4317</th>
      <td>id_239</td>
      <td>WIND_DIREC</td>
      <td>76</td>
      <td>99</td>
      <td>93</td>
      <td>97</td>
      <td>93</td>
      <td>94</td>
      <td>98</td>
      <td>97</td>
      <td>65</td>
    </tr>
    <tr>
      <th>4318</th>
      <td>id_239</td>
      <td>WIND_SPEED</td>
      <td>2.2</td>
      <td>3.2</td>
      <td>2.5</td>
      <td>3.6</td>
      <td>5</td>
      <td>4.2</td>
      <td>5.7</td>
      <td>4.9</td>
      <td>3.6</td>
    </tr>
    <tr>
      <th>4319</th>
      <td>id_239</td>
      <td>WS_HR</td>
      <td>1.7</td>
      <td>2.8</td>
      <td>2.6</td>
      <td>3.3</td>
      <td>3.5</td>
      <td>5</td>
      <td>4.9</td>
      <td>5.2</td>
      <td>3.6</td>
    </tr>
  </tbody>
</table>
<p>4320 rows × 11 columns</p>
</div>



根据任务描述，训练集记录的是每月前 20 天的数据，那么每月余下的数据便打乱后包含于测试集，同时只保留前 9 小时的数据，目标是根据这 9 个小时的数据预测出第 10 个小时的 PM 2.5 值。同理，**测试集的 `NR` 稍后也需处理为 0**。


```python
data_train = data_train.replace('NR',0.0)
data_test  = data_test.replace('NR',0.0)
```

我们的任务是：根据训练集数据，使用回归的方法，对模型进行训练，使得模型能够根据测试集中前 9 小时的数据预测出第 10 小时的 `PM2.5` 值。

由于涉及的项较多，为简化任务，本次实验的**模型均采用线性模型**，那么考虑模型复杂程度可以分为以下两种模型：
1. 仅考虑 `PM2.5` 指标，根据前 9 小时的 `PM2.5` 指标预测第 10 小时的值；
2. 考虑所有指标对 `PM2.5` 的影响，根据前 9 小时所有指标预测第 10 小时的值。

测试集中，前 9 小时的数据是连续的 9 小时，所以可以从训练集中提取连续的 9 小时作为训练样本，之后的 1 小时的 `PM2.5` 观测值作为标签，获得训练集。




```python
data_train.iloc[:,3:] = data_train.iloc[:,3:].astype(float)
```


```python
X_train = []
y_train = []
x_train_pm25 = []
for date in data_train.日期.unique():
    data_daily = data_train[data_train['日期']==date]
#     display(data_daily)
    properties = data_daily.測項
#     display(properties)
    for i in range(0+3,16+3):
        X_train.append(data_daily.iloc[:,i:(i+9)].values)
        x_train_pm25.append(data_daily.iloc[9,i:(i+9)].values)
        y_train.append(data_daily[data_daily['測項']=='PM2.5'].iloc[:,(i+8)].values[0])
```

就这样，我们获取到了基础的训练数据集，可以开始后续的模型训练。

# Regression - Model1 : Simple Linear Model



## Definition

上面的分析已经提到，我们的模型为线性模型。以最简单的模型 1 为例，最后的模型应为如下形式：

$$
f(\boldsymbol{x}) = w_1 \cdot x_1 + w_2 \cdot x_2 + \cdots + w_9 \cdot x_9 + b
$$

其中 $y$ 为第 10 小时 PM2.5 预测值，$x_1,x_2,\cdots,x_9$ 为前 9 小时的 PM2.5 观测值。

为了后续讨论方便，尽可能将模型的元素使用向量或矩阵的形式表示，原模型可写为：

$$
f(\boldsymbol{x}) = \boldsymbol{w}^T\boldsymbol{x}+b
$$

为便于后续讨论，将模型进一步简化表示，将偏置 $b$ 收入向量形式 $\boldsymbol{\hat{w}}=(\boldsymbol{w},b)$。全体数据集表示为一个矩阵，行列数量对应样本数、属性值数+1，其中每行对应一个样本，最后一个元素恒置为 1：

$$
\boldsymbol{X} = \left(\begin{matrix}
x_{11} & x_{12} & \cdots & x_{1d} & 1 \\
x_{21} & x_{22} & \cdots & x_{2d} & 1 \\
\vdots & \vdots & \ddots & \vdots & \vdots \\
x_{m1} & x_{m2} & \cdots & x_{md} & 1
\end{matrix}\right)
= \left(\begin{matrix}
\boldsymbol{x}_1^T & 1 \\
\boldsymbol{x}_2^T & 1 \\
\vdots & \vdots \\
\boldsymbol{x}_m^T & 1 \\
\end{matrix}\right)
$$

其中 $d=9$。

我们的目标是，学习得到 $f(\boldsymbol{x}_i)$ 使得 $f(\boldsymbol{x}_i)\simeq y_i$


## Loss Function

模型在不断优化的过程中，需要对模型效果进行评估。在回归任务中，均方误差（MSE）是最常见的性能度量。

采用均方误差作为损失函数，我们原来学习函数的问题转化为求取所有样本点到我们拟合的模型曲线上的欧式距离之和，并迭代优化模型使之最小。

为了方便表示，将样本点标签（label，即我们预测的第 10 小时 PM2.5 值）也写成向量形式：$\boldsymbol{y} = (y_1;y_2;\cdots;y_m)$，损失函数可以表示为：

$$
L = \dfrac{1}{m}(\boldsymbol{y} - \boldsymbol{X\hat{w}})^T(\boldsymbol{y} - \boldsymbol{X\hat{w}})
$$

## Gradient Descent

随机下降过程是利用样本学习参数的过程，将参数向梯度下降方向（损失函数值在某一点减少的方向）移动。

梯度下降可以分为以下步骤：

1. 随机选取一个初始点 $\boldsymbol{\hat{w}}^0$ ；
2. 计算在该处参数 $\boldsymbol{\hat{w}}$ 对损失函数 $L$ 的（偏）微分 $\dfrac{\partial L}{\partial \boldsymbol{\hat{w}}}|_{\boldsymbol{\hat{w}}=\boldsymbol{\hat{w}}^i}$；
3. 更新 $\boldsymbol{\hat{w}}^i$：$\boldsymbol{\hat{w}}^{i+1} \leftarrow \boldsymbol{\hat{w}}^i - \eta\dfrac{\partial L}{\partial \boldsymbol{\hat{w}}}|_{\boldsymbol{\hat{w}}=\boldsymbol{\hat{w}}^i}$。其中 $\eta$ 为学习率，微分值为负则增加参数，若微分值为正则减少参数。重复步骤2-3，直至微分值为 $0$，得到 $\boldsymbol{\hat{w}}$ 的最优值 $\boldsymbol{\hat{w}}^\star$ 。

梯度下降较为关键的步骤就是梯度（损失函数偏微分）的计算和参数更新，计算梯度的方法与损失函数的选择相关，前面我们选择了 MSE 作为损失函数。$L$ 对 $\boldsymbol{\hat{w}}$ 求偏导得：

$$
\dfrac{\partial L}{\partial \boldsymbol{\hat{w}}} = \dfrac{2}{m} \boldsymbol{X}^T(\boldsymbol{X}\boldsymbol{\hat{w}}-\boldsymbol{y})
$$




```python
def grad_descent(X,Y,epoch=20,eta=0.0001,w_old=None):
    """
    Args:
        X: 输入数据集, Numpy 数组 (Sample_count, Attr_count)
        Y: 预测标签, Numpy 数组 (Sample_count, 1)
        epoch: 迭代轮数
        eta: 学习率
    """
    X = np.column_stack((X,np.ones(X.shape[0])))
    Y = Y.reshape((Y.shape[0],1))
    w = w_old if w_old is not None else np.random.random(size=(X.shape[1],1)) # 随机初始化参数 w（Step 1）
    loss = []
    for iter in range(epoch):
        print("\r{}/{}".format(iter+1,epoch),end="")
        gradient = 2 * X.T.dot(X.dot(w)-Y) / X.shape[0]                       # 计算 L 对 w 的偏微分，即梯度。（Step 2）
        w = w - eta * gradient                                                # 更新参数 w （Step 3）
        loss.append((Y-X.dot(w)).T.dot(Y-X.dot(w)).reshape(-1)/ X.shape[0])   # 计算损失 L
    return w, loss
```


```python
w,loss = grad_descent(np.array(x_train_pm25,dtype=float),np.array(y_train,dtype=float))
```

    20/20

通过迭代我们学习得到了模型参数 $\boldsymbol{\hat{w}}$ 和每一轮函数的损失值，我们可以通过可视化的方式直观展现函数损失


```python
def plot_loss(loss,title="Loss Value per Iter"):
    fig = plt.figure()
    ax = fig.add_subplot()
    ax.set_title(title)
    ax.set_xlabel("iteration")
    ax.set_ylabel("Loss/MSE")
    ax.plot(range(len(loss)),np.array(loss).reshape(-1))
plot_loss(loss)
print(loss[-1])
```

    [26.05574238]




![plt](https://download.mariozzj.cn/img/picgo/202112051944500.png)
    


可以看到，随着训练轮数的增加，损失值在不断下降，说明我们的模型在不断学习，向数据逼近。

## 参数调整：训练轮数 epoch

如果我们进一步增加训练轮数，可以使得损失函数进一步收敛：


```python
w,loss = grad_descent(np.array(x_train_pm25,dtype=float),np.array(y_train,dtype=float),epoch=80,w_old=w)
print()
plot_loss(loss)
print(loss[-1])
```

    80/80
    [4.08627535]




![plt](https://download.mariozzj.cn/img/picgo/202112051945527.png)
    


函数收敛的速度不断降低，所以如果无限度增大训练轮数确实可以使得损失函数较小，但是有可能带来过拟合现象。所以调整训练轮数在适当值即可。

## 参数调整：学习率 eta

学习率能够控制参数更新速度，过大的学习率可能导致损失函数错过全局最优，甚至无法收敛；而较小的学习率可能导致损失函数下降较慢

![image-20211119233528788](https://download.mariozzj.cn/img/picgo/202111192335868.png)

由于我们的参数矩阵是随机生成，为便于对比不同学习率的效果，我们控制每一次的初始参数都相同


```python
w = np.random.random(size=(len(x_train_pm25[0])+1,1))
w1,loss1 = grad_descent(np.array(x_train_pm25,dtype=float),np.array(y_train,dtype=float),epoch=50,eta=0.0001,w_old=w)
w2,loss2 = grad_descent(np.array(x_train_pm25,dtype=float),np.array(y_train,dtype=float),epoch=50,eta=0.001,w_old=w)
w3,loss3 = grad_descent(np.array(x_train_pm25,dtype=float),np.array(y_train,dtype=float),epoch=50,eta=0.00001,w_old=w)
```

    50/50


```python
plot_loss(loss1,title="Loss per Iter: $\eta=10^{-4}$")
print(loss1[-1])
plot_loss(loss2,title="Loss per Iter: $\eta=10^{-3}$")
print(loss2[-1])
plot_loss(loss3,title="Loss per Iter: $\eta=10^{-6}$")
print(loss3[-1])
```

    [11.36702501]
    [8.90902162e+109]
    [37.52494183]




![plt](https://download.mariozzj.cn/img/picgo/202112051945756.png)
    




![plt](https://download.mariozzj.cn/img/picgo/202112051945668.png)
    




![plt](https://download.mariozzj.cn/img/picgo/202112051945836.png)
    


可以看到，当 $\eta=0.001$ 时，损失函数最终飙升，无法收敛，此时学习率过大；当 $\eta=0.000001$ 时，损失函数最初的收敛速度明显不如 $\eta=0.0001$ 时，且经过同样较大的迭代轮数后，损失函数值的收敛效果不如后者，学习率较小。

所以，需要经过多次测试，比较模型在不同学习率下的表现，方能获得较好的学习效果。

## 模型预测

通过参数调整测试，我们已经确定了较好的参数，这样就可以获取最终的模型，对目标数据进行预测。

$$
f(\boldsymbol{X}) = \boldsymbol{\hat{w}X}
$$


```python
w,loss = grad_descent(np.array(x_train_pm25,dtype=float),np.array(y_train,dtype=float),eta=0.00013,epoch=300)
print("Final Loss: {}".format(loss[-1]))
```

    300/300Final Loss: [0.639463]



```python
X_test_pm25 = data_test[data_test['測項']=='PM2.5'].iloc[:,2:].values.astype(float)
X = np.column_stack((X_test_pm25,np.ones(X_test_pm25.shape[0])))
Y = X.dot(w)
```


```python
result = pd.merge(data_test[data_test['測項']=='PM2.5'].iloc[:,0].reset_index(drop=True),pd.DataFrame(Y),left_index=True,right_index=True,how='outer')
result.columns = ['id','value']
result.to_csv(OUT_DIR + "submission.csv",index=False)
```

## 结果

**最终的 Public Score 是 6.28859（击败 Simple Baseline: 6.55912），Private Score 是 8.53027（击败 Simple Baseline: 8.73773）**

# Regression - Model2 : Take more feature into consideration

## 模型构建

模型方面其实没有变化，但是这一步中，我们将考虑数据中给出的所有特征，并不局限于 PM 2.5 数据。


```python
def grad_descent(X,Y,epoch=20,eta=0.0001,w_old=None):
    """
    Args:
        X: 输入数据集, Numpy 矩阵 (Sample_count, Attr_count)
        Y: 预测标签, Numpy 矩阵 (Sample_count, 1)
        epoch: 迭代轮数
        eta: 学习率
    """
    X = np.column_stack((X,np.ones(X.shape[0])))
    Y = Y.reshape((Y.shape[0],1))
    w = w_old if w_old is not None else np.random.random(size=(X.shape[1],1)) # 随机初始化参数 w（Step 1）
    loss = []
    for iter in range(epoch):
        print("\r{}/{}".format(iter+1,epoch),end="")
        gradient = 2 * X.T.dot(X.dot(w)-Y) / X.shape[0]                       # 计算 L 对 w 的偏微分，即梯度。（Step 2）
        w = w - eta * gradient                                                # 更新参数 w （Step 3）
        loss.append((Y-X.dot(w)).T.dot(Y-X.dot(w)).reshape(-1)/ X.shape[0])   # 计算损失 L
    
    return w, loss

def plot_loss(loss,title="Loss Value per Iter"):
    fig = plt.figure()
    ax = fig.add_subplot()
    ax.set_title(title)
    ax.set_xlabel("iteration")
    ax.set_ylabel("Loss/MSE")
    ax.plot(range(len(loss)),np.array(loss).reshape(-1))
```


```python
w,loss = grad_descent(np.array(X_train,dtype=float).reshape(len(X_train),-1),np.array(y_train,dtype=float),epoch=300,eta=0.0000014)
```

    300/300


```python
plot_loss(loss)
```


![plt](https://download.mariozzj.cn/img/picgo/202112051945774.png)
    


在这部分，我们略去 Model1 中已经涉及的参数调节过程，直接选取我多次实验后选取的最优参数，随后直接进行预测。

## 模型预测


```python
X_test = data_test.iloc[:,2:].values.astype(float)
id = data_test.日期.unique()
X_test = X_test.reshape(len(id),-1)
```


```python
X = np.column_stack((X_test,np.ones(X_test.shape[0])))
Y = X.dot(w)
result = pd.merge(pd.DataFrame(id),pd.DataFrame(Y),left_index=True,right_index=True,how='outer')
result.columns = ['id','value']
result.to_csv(OUT_DIR + "submission.csv",index=False)
```

## 结果

确定 $\eta = 1.4 \times 10^{-6}$ 是学习率的最优参数后，在 Kaggle 上进行了多次提交，发现设定迭代次数在 50000 次以内时，结果得分在 10 ~ 30；而设定迭代次数到 100000 次时， **取得了 Public Score 为 6.406，Private Score 为 8.477，表现优于 Model1** ，但是显然出现了 **参数收敛较慢** 的问题。

考虑更多指标，本质上是增加了模型的复杂度，因为 Model2 的表示能力是大于且包含 Model1 的表示能力的（因为 Model1 的 9 个参数均是 Model2 的参数），所以 Model2 的理论性能是一定要高于 Model1 的。

如果提升学习率，会使得损失函数无法收敛，所以在其他方面对模型还有优化空间。

# Regression - Model3 : Normalize the Feature

在 Model2 中考虑了更多指标，提升了模型表现，但是带来了另一个问题：**参数收敛较慢**，造成该现象的原因可能是，我们引入了新的参数，而新的参数与 Model1 的特征的量纲完全不同，即我们只考虑 PM2.5 指标值，它的范围可能是 0 - 200，而如果考虑 WIND_DIRECTION，它的范围则是 0 ~ 360。对于不同范围的特征，参数的敏感程度是不同的，就可能带来不同参数收敛速度不同，造成整体参数收敛速度较慢。而如果将所有参数都标准化，就可以在一定程度上提升参数的收敛速度。

![avatar](https://download.mariozzj.cn/img/picgo/202112052001819.png)

## Normalizaiton

我们将采用 Min-Max Normalization，需要获取各参数的最大值、最小值。


```python
X_large = np.array(X_train[0])
for i in range(len(X_train)):
    X_large = np.column_stack((X_large,X_train[i]))
X_max = np.max(X_large,axis=1)
X_min = np.min(X_large,axis=1)
```

随后，对数据中的每一个元素都进行标准化操作。Min-Max Normalization 的方法是：

$$
x^\star = \dfrac{x-\min{\boldsymbol({x})}}{\max{\boldsymbol({x})}-\min{\boldsymbol({x})}}
$$


```python
X_norm = []
for i in range(len(X_train)):
    X_norm.append(((X_train[i].T-X_min)/(X_max-X_min)).T)
```

## 模型构建


```python
w,loss = grad_descent(np.array(X_norm,dtype=float).reshape(len(X_norm),-1),np.array(y_train,dtype=float),epoch=1000,eta=0.035)
```

    1000/1000


```python
plot_loss(loss)
print(loss[-1])
```

    [20.63629716]




![plt](https://download.mariozzj.cn/img/picgo/202112051945718.png)
    


可以看到，对数据进行标准化操作后，参数收敛的速度没有比上一轮快，但是损失值远远比不标准化特征还要低，这可能是因为特征值被放缩导致欧式距离减小导致的。但是相比而言，损失值比不放缩数据更早收敛。

## 模型预测

由于我们对训练集输入进行了标准化，那么我们对测试集输入也需要进行相同的操作，而这里标准化的最大值、最小值并不是取决于测试集，而是取决于训练集，这样才能**使得两个数据集具有对等的放缩映射，模型参数才能生效**。


```python
X_test = data_test.iloc[:,2:].values.astype(float)
id = data_test.日期.unique()
attr_count = int(X_test.shape[0]/len(id))
X_test_list = []
for i in range(int(X_test.shape[0]/attr_count)):
    X_i = X_test[i*attr_count:(i+1)*attr_count]
    X_test_list.append(((X_i.T-X_min)/(X_max-X_min)).T)
```


```python
X_test = np.array(X_test_list).reshape(int(X_test.shape[0]/attr_count),-1)
X = np.column_stack((X_test,np.ones(X_test.shape[0])))
Y = X.dot(w)
result = pd.merge(pd.DataFrame(id),pd.DataFrame(Y),left_index=True,right_index=True,how='outer')
result.columns = ['id','value']
result.to_csv(OUT_DIR + "submission.csv",index=False)
```

## 结果

迭代同样次数情况下，取得 **Public Score 6.286，Private Score 8.221 成绩**。

# Regression - Model4 : Ordinary Least Squares

如果数据量较大，能够实现输入矩阵求逆，就可以不使用梯度下降的方法逐次迭代寻找最优，而是利用最小二乘法直接求取最优值。

## 模型构建

前面我们已经得到 
$$
\dfrac{\partial L}{\partial \boldsymbol{\hat{w}}} = \dfrac{2}{m} \boldsymbol{X}^T(\boldsymbol{X}\boldsymbol{\hat{w}}-\boldsymbol{y})
$$
我们可以令 $\dfrac{\partial L}{\partial \boldsymbol{\hat{w}}}=0$ ，即可得到最优解 
$$
\boldsymbol{\hat{w}}^\star = (\boldsymbol{X}^T\boldsymbol{X})^{-1}\boldsymbol{X}^T\boldsymbol{y}
$$


```python
def ols(X,Y):
    X = np.column_stack((X,np.ones(X.shape[0])))
    Y = Y.reshape((Y.shape[0],1))
    w = np.linalg.inv(X.T.dot(X)).dot(X.T.dot(Y))
    return w
```


```python
w = ols(np.array(X_norm,dtype=float).reshape(len(X_norm),-1),np.array(y_train,dtype=float))
```

## 模型预测




```python
Y = X.dot(w)
result = pd.merge(pd.DataFrame(id),pd.DataFrame(Y),left_index=True,right_index=True,how='outer')
result.columns = ['id','value']
result.to_csv(OUT_DIR + "submission.csv",index=False)
```

## 结果

采用最小二乘法直接求取闭式解的结果是：Public Score 6.290，Private Score 8.221，表现均比 Model2、3 好。

# Summary


## Results
本次竞赛就丰原站气象数据进行了回归预测，对模型进行了优化，最终表现总结如下表：

| Model | iteration | parameters & Description | PubScore | PrivScore | 
| ----: | :-------: | :----------------------: | -------: | --------: | 
| GD Model 1 | $10^2$ | $\eta = 1.5 \times 10^{-4}$ ，仅考虑 PM2.5 指标 | 6.289 | 8.530 | 
| GD Model 2 | $10^5$ | $\eta = 1.4 \times 10^{-6}$ ，考虑所有提供的指标 | 6.406 | 8.422 | 
| GD Model 3 | $10^5$ | $\eta = 3.5 \times 10^{-2}$ ，对所有指标标准化 | 6.286 | 8.221 | 
| OLS Model  | 1 | 直接求解 | 6.290 | 8.220 | 
| Simple Baseline | Unknown | Unknown | 6.559 | 8.738 | 
| Strong Baseline | Unknown | Unknown | 5.560 | 7.142 | 

尝试的所有模型均突破了 Simple Baseline，但是距离 Strong Baseline 仍然还有一定距离。

## Highlights

* 相比于大部分他人做法，我**采样获得的样本较多**，获取了大多数连续的 8 小时作为样本，总计 3 840 样本。
* 考虑了学习率、迭代轮数、特征选择、特征放缩等因素优化模型。



## Lowlights
* 可以使用交叉验证进行模型选择
* 没有进行本地测试准确率评估性能，仅参考 Loss
* 考虑的样本、特征过多可能带来了噪声，可以考虑进行降维或进一步特征选择
* 可以将学习率优化为自适应
