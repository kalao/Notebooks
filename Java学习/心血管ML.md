参考资料:

https://tianchi.aliyun.com/notebook-ai/detail?spm=5176.12586969.1002.6.1cd866c2kjZqbn&postId=95457

https://tianchi.aliyun.com/notebook-ai/detail?spm=5176.12586969.1002.3.1cd866c2kjZqbn&postId=95501

## 1. 数据的总览

### 1.1 数据简略观察

> 查看训练数据的头几行和尾几行

```python
Train_data.head().append(Train_data.tail())
```

![image-20210126203234397](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126203234397.png)

### 1.2 数据的类型和含义

> 通过下面代码, 我们可以看到,总共有297条训练数据,其中oldpeak是浮点型,其他都是整型的.

```
Train_data.info()
```

```
RangeIndex: 297 entries, 0 to 296
Data columns (total 14 columns):
 #   Column    Non-Null Count  类型      含义
---  ------    --------------  -----    ------------------------------------
 0   age       297 non-null    int64  	年龄
 1   sex       297 non-null    int64  	性别(1为男性,0为女性)
 2   cp        297 non-null    int64  	胸痛类型,痛感由重到无
 3   trestbps  297 non-null    int64  	静息血压(单位为mmHg)
 4   chol      297 non-null    int64  	血清胆固醇（单位为mg/dl）
 5   fbs       297 non-null    int64  	空腹血糖 (大于120mg/dl的时候为1,否则为0)
 6   restecg   297 non-null    int64  	静息心电图结果
 7   thalach   297 non-null    int64  	达到的最大心率
 8   exang     297 non-null    int64  	是否有运动性心绞痛(1是有,0是没有) 
 9   oldpeak   297 non-null    float64	st段压数值
 10  slope     297 non-null    int64  	ST段峰值的斜率,程度分为down、flat、up
 11  ca        297 non-null    int64  	荧光染色的主要血管数(0-3)
 12  thal      297 non-null    int64  	3=正常；6=固定缺陷；7=可逆缺陷
 13  target    297 non-null    int64    1是有心脏病，0是没有心脏病。
dtypes: float64(1), int64(13)
memory usage: 32.6 KB
```

## 2. 判断数据缺失和异常

### 2.1 缺失值判断

```python
Train_data.isnull().sum()
```

```
age         0
sex         0
cp          0
trestbps    0
chol        0
fbs         0
restecg     0
thalach     0
exang       0
oldpeak     0
slope       0
ca          0
thal        0
target      0
dtype: int64
```

> 我们发现数据没有缺少值

- 可以上面的nan可视化

```python
missing = Train_data.isnull().sum()
missing = missing[missing > 0]
missing.sort_values(inplace=True)
missing.plot.bar()
```

示例:

![image-20210126231304149](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126231304149.png)

```
# 可视化看下缺省值
msno.matrix(Train_data.sample(250))
```

![image-20210126231357312](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126231357312.png)

### 2.2 异常判断

- 统计每个特征的独特值

```
for cat_fea in Train_data.columns: 
    print("{}特征有个{}不同的值".format(cat_fea, Train_data[cat_fea].nunique()))
```

```
age特征有个41不同的值
sex特征有个2不同的值
cp特征有个4不同的值
trestbps特征有个49不同的值
chol特征有个152不同的值
fbs特征有个2不同的值
restecg特征有个4不同的值
thalach特征有个91不同的值
exang特征有个2不同的值
oldpeak特征有个40不同的值
slope特征有个3不同的值
ca特征有个4不同的值
thal特征有个4不同的值
target特征有个2不同的值
```

- 观察可疑的数据的数据取值

```
Train_data['restecg'].value_counts()
```

```
1    152
0    147
2      4
c	   1
Name: restecg, dtype: int64
```

> 这里的c就是异常值

### 2.3 异常值的处理(具体的处理在4)

> 将c替换为nan,后面可以选择删除或者保留,对于一些树模型可以容忍有缺失值

```python
Train_data['restecg'].replace('c', np.nan, inplace=True)
```

## 3. 特征分析

### 3.1 数字特征分析

```xml
thalach     
chol      
trestbps   
age        
oldpeak    
```

#### 3.1.1 相关性分析

```python
price_numeric = Train_data[numeric_features]
correlation = price_numeric.corr()
print(correlation['target'].sort_values(ascending = False),'\n')
```

```
target      1.000000
thalach     0.421741
chol       -0.085239
trestbps   -0.144931
age        -0.225439
oldpeak    -0.430696
Name: target, dtype: float64 
```

- 热力图

```
f, ax = plt.subplots(figsize = (7, 7))
plt.title('Correlation of Numeric Features with Price',y=1,size=16)
sns.heatmap(correlation,square = True,  vmax=0.8)
```

![image-20210126220356570](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126220356570.png)



#### 3.1.2 数值特征分布

```
f = pd.melt(Train_data, value_vars=numeric_features)
g = sns.FacetGrid(f, col="variable",  col_wrap=2, sharex=False, sharey=False)
g = g.map(sns.distplot, "value")
```

![image-20210126221919659](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126221919659.png)

#### 3.1.3 数字特征相互之间的关系可视化

![image-20210126222242034](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126222242034.png)



![image-20210126222615186](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126222615186.png)



### 3.2 类型特征分析

```
'cp',
'restecg',
'slope', 
'ca',
'thal'
'sex'
'fbs'
'exang'
```



#### 3.2.1 类别特征的柱形图可视化

```python
def bar_plot(x, y, **kwargs):
    sns.barplot(x=x, y=y)
    x=plt.xticks(rotation=90)

f = pd.melt(Train_data, id_vars=['target'], value_vars=categorical_features)
g = sns.FacetGrid(f, col="variable",  col_wrap=2, sharex=False, sharey=False, size=5)
g = g.map(bar_plot, "value", "target")
```

![image-20210126223004102](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126223004102.png)

> 可以看类别特征对target的影响(以平均值),比如cp=1的时候,那么诊断为心脏病的概率是偏高的.再比如是否有运动性心绞痛(exang)这个为1的时候,诊断为心脏病的概率是很高的.

----------------------------------------------------------One week---------------------------------------------------------

## 4. 特征工程

### 4.1 异常的处理

- 通过箱线图（或 3-Sigma）分析删除异常值；
- BOX-COX 转换（处理有偏分布）；
- 长尾截断；

1. 使用箱线法

![image-20210126225425299](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126225425299.png)

```python
#这里我包装了一个异常值处理的代码，可以随便调用。
def outliers_proc(data, col_name, scale=3):
    """
    用于清洗异常值，默认用 box_plot（scale=3）进行清洗
    :param data: 接收 pandas 数据格式
    :param col_name: pandas 列名
    :param scale: 尺度
    :return:
    """
    def box_plot_outliers(data_ser, box_scale):
        """
        利用箱线图去除异常值
        :param data_ser: 接收 pandas.Series 数据格式
        :param box_scale: 箱线图尺度，
        :return:
        """
        iqr = box_scale * (data_ser.quantile(0.75) - data_ser.quantile(0.25))
        val_low = data_ser.quantile(0.25) - iqr#下限
        val_up = data_ser.quantile(0.75) + iqr#上界
        rule_low = (data_ser < val_low)
        rule_up = (data_ser > val_up)
        return (rule_low, rule_up), (val_low, val_up)

    data_n = data.copy()
    data_series = data_n[col_name]
    rule, value = box_plot_outliers(data_series, box_scale=scale)
    index = np.arange(data_series.shape[0])[rule[0] | rule[1]]
    print("Delete number is: {}".format(len(index)))
    data_n = data_n.drop(index)
    data_n.reset_index(drop=True, inplace=True)
    print("Now column number is: {}".format(data_n.shape[0]))
    index_low = np.arange(data_series.shape[0])[rule[0]]
    outliers = data_series.iloc[index_low]
    print("Description of data less than the lower bound is:")
    print(pd.Series(outliers).describe())
    index_up = np.arange(data_series.shape[0])[rule[1]]
    outliers = data_series.iloc[index_up]
    print("Description of data larger than the upper bound is:")
    print(pd.Series(outliers).describe())

    fig, ax = plt.subplots(1, 2, figsize=(10, 7))
    sns.boxplot(y=data[col_name], data=data, palette="Set1", ax=ax[0])
    sns.boxplot(y=data_n[col_name], data=data_n, palette="Set1", ax=ax[1])
    return data_n
```
```
Train_data=outliers_proc(Train_data, 'chol', scale=3)
```

```
Delete number is: 1
Now column number is: 302
Description of data less than the lower bound is:
count    0.0
mean     NaN
std      NaN
min      NaN
25%      NaN
50%      NaN
75%      NaN
max      NaN
Name: chol, dtype: float64
Description of data larger than the upper bound is:
count      1.0
mean     564.0
std        NaN
min      564.0
25%      564.0
50%      564.0
75%      564.0
max      564.0
Name: chol, dtype: float64
```

![image-20210126230049275](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126230049275.png)

> 这里删除了chol的超上界的异常值



### 4.2 缺失值处理

- 不处理（针对类似 XGBoost 等树模型）；
- 删除（缺失数据太多）；
- 插值补全，包括均值/中位数/众数/建模预测/多重插补/压缩感知补全/矩阵补全等；

1. 直接导出

   ```
   Train_data.to_csv('../data/data_for_tree.csv', index=0)
   
   ```

   2.删除
   
   
   
   ![image-20210126231818450](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126231818450.png)



### 4.3  数据分桶

- 等距分桶；

```python
# 为什么要做数据分桶呢，原因有很多，= =
# 1. 离散后稀疏向量内积乘法运算速度更快，计算结果也方便存储，容易扩展；
# 2. 离散后的特征对异常值更具鲁棒性，如 age>30 为 1 否则为 0，对于年龄为 200 的也不会对模型造成很大的干扰；
# 3. LR 属于广义线性模型，表达能力有限，经过离散化后，每个变量有单独的权重，这相当于引入了非线性，能够提升模型的表达能力，加大拟合；
# 4. 离散后特征可以进行特征交叉，提升表达能力，由 M+N 个变量编程 M*N 个变量，进一步引入非线形，提升了表达能力；
# 5. 特征离散后模型更稳定，如用户年龄区间，不会因为用户年龄长了一岁就变化
```

```python
bin = [i*10 for i in range(31)]
data['power_bin'] = pd.cut(data['power'], bin, labels=False)
data[['power_bin', 'power']].head()
```

### 4.4 特征的归一化/标准化

- 标准化（转换为标准正态分布）；
- 归一化（抓换到 [0,1] 区间）；
- 针对幂律分布，可以采用公式： ![image-20210126232031807](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126232031807.png)

1. 对age归一化

```python
from sklearn import preprocessing
min_max_scaler = preprocessing.MinMaxScaler()
data['age'] = np.log(data['age'] + 1) 
data['age'] = ((data['age'] - np.min(data['age'])) / (np.max(data['age']) - np.min(data['age'])))
data['age'].plot.hist()
```

![image-20210126232301897](https://github.com/kalao/Images/blob/master/心血管ML.md/20210126232301897.png)

<img src="https://github.com/kalao/Images/blob/master/心血管ML.md/20210126232220204.png" alt="image-20210126232220204"  />

### 4.5 类别特征OneEncoder

```
# 对类别特征进行 OneEncoder
data = pd.get_dummies(data, columns=['cp', 'restecg', 'slope', 'ca','thal'])
```

```
(296, 27)
Index(['age', 'sex', 'trestbps', 'chol', 'fbs', 'thalach', 'exang', 'oldpeak',
       'target', 'cp_0', 'cp_1', 'cp_2', 'cp_3', 'restecg_0', 'restecg_1',
       'restecg_2', 'slope_0', 'slope_1', 'slope_2', 'ca_0', 'ca_1', 'ca_2',
       'ca_3', 'thal_0', 'thal_1', 'thal_2', 'thal_3'],
      dtype='object')
```

> 现在已经生成27个特征

### 4.6 特征过滤

- 过滤式（filter）：先对数据进行特征选择，然后在训练学习器，常见的方法有 Relief/方差选择发/相关系数法/卡方检验法/互信息法；
- 包裹式（wrapper）：直接把最终将要使用的学习器的性能作为特征子集的评价准则，常见方法有 LVM（Las Vegas Wrapper） ；
- 嵌入式（embedding）：结合过滤式和包裹式，学习器训练过程中自动进行了特征选择，常见的有 lasso 回归；
