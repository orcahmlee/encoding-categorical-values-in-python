[Encoding Categorical Values in Python](https://hackmd.io/4ssrTi9pSmWLPDyqXPlLfQ)
===

## Introduction
資料科學專案中的資料集(Data set)通常會擁有非常多樣性的數據型態(Data type)，數字、文字、空值、NaN...etc.。這些數據型態又可分為數值型變數(Numeric)與類別型變數(Categorical)，數值型變數分為連續型(Continuous)與分散型(Discrete)，類別型變數分為順序型(Ordinal)與名義型(Nominal)。:confused::confused::confused:
<table style='text-align: center;'>
    <tr>
        <td colspan='2'><b>Numeric</b></td>
        <td colspan='2'><b>Categorical</b></td>
    </tr>
    <tr>
        <td>Continuous</td>
        <td>Discrete</td>
        <td>Ordinal</td>
        <td>Nominal</td>
    </tr>
</table>

- Continuous: 連續的數值，如時間、高度、溫度、壓力、年齡...etc.。
- Discrete: 總數的概念，如1台車、2間分店、3個小孩...etc.。
- Ordinal: 雖然不是數值，但是邏輯上具有順序性或大小區別，如成績(A、B、C、F)、衣服尺寸(大、中、小)...etc.。
- Nominal: 性別、顏色、區域、國家...etc.。

雖然許多機器學習演算法不需要經過特別處理就可以直接使用 Categorical variables，但是仍然有許多演算法不支援，因此處理 Categorical values 的技巧也是需要學習的(~~不然我幹嘛寫這篇~~:wink:)。

在資料科學與開源的世界中並不會只有一種答案，有很多方法可以處理這件事。不同處理方法有可能會對分析結果造成潛在的影響，因此必須瞭解`為什麼`要選擇某種方法，而不是一昧的使用 API。

## Data Set & Data Preparation
資料集取自於[參考資料](http://pbpython.com/categorical-encoding.html)中，資料集取得後通常無法直接使用，一般都要進行 ETL(Extract,Transform and Load)。

```python
import pandas as pd
import numpy as np

df = pd.read_csv('http://mlr.cs.umass.edu/ml/machine-learning-databases/autos/imports-85.data')
df.head()
```

原始資料中的各欄沒有標題，且有些欄位為問號 '?'。

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>3</th>
      <th>?</th>
      <th>alfa-romero</th>
      <th>gas</th>
      <th>std</th>
      <th>two</th>
      <th>convertible</th>
      <th>rwd</th>
      <th>front</th>
      <th>88.60</th>
      <th>...</th>
      <th>130</th>
      <th>mpfi</th>
      <th>3.47</th>
      <th>2.68</th>
      <th>9.00</th>
      <th>111</th>
      <th>5000</th>
      <th>21</th>
      <th>27</th>
      <th>13495</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3</td>
      <td>?</td>
      <td>alfa-romero</td>
      <td>gas</td>
      <td>std</td>
      <td>two</td>
      <td>convertible</td>
      <td>rwd</td>
      <td>front</td>
      <td>88.6</td>
      <td>...</td>
      <td>130</td>
      <td>mpfi</td>
      <td>3.47</td>
      <td>2.68</td>
      <td>9.0</td>
      <td>111</td>
      <td>5000</td>
      <td>21</td>
      <td>27</td>
      <td>16500</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>?</td>
      <td>alfa-romero</td>
      <td>gas</td>
      <td>std</td>
      <td>two</td>
      <td>hatchback</td>
      <td>rwd</td>
      <td>front</td>
      <td>94.5</td>
      <td>...</td>
      <td>152</td>
      <td>mpfi</td>
      <td>2.68</td>
      <td>3.47</td>
      <td>9.0</td>
      <td>154</td>
      <td>5000</td>
      <td>19</td>
      <td>26</td>
      <td>16500</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>164</td>
      <td>audi</td>
      <td>gas</td>
      <td>std</td>
      <td>four</td>
      <td>sedan</td>
      <td>fwd</td>
      <td>front</td>
      <td>99.8</td>
      <td>...</td>
      <td>109</td>
      <td>mpfi</td>
      <td>3.19</td>
      <td>3.40</td>
      <td>10.0</td>
      <td>102</td>
      <td>5500</td>
      <td>24</td>
      <td>30</td>
      <td>13950</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>164</td>
      <td>audi</td>
      <td>gas</td>
      <td>std</td>
      <td>four</td>
      <td>sedan</td>
      <td>4wd</td>
      <td>front</td>
      <td>99.4</td>
      <td>...</td>
      <td>136</td>
      <td>mpfi</td>
      <td>3.19</td>
      <td>3.40</td>
      <td>8.0</td>
      <td>115</td>
      <td>5500</td>
      <td>18</td>
      <td>22</td>
      <td>17450</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>?</td>
      <td>audi</td>
      <td>gas</td>
      <td>std</td>
      <td>two</td>
      <td>sedan</td>
      <td>fwd</td>
      <td>front</td>
      <td>99.8</td>
      <td>...</td>
      <td>136</td>
      <td>mpfi</td>
      <td>3.19</td>
      <td>3.40</td>
      <td>8.5</td>
      <td>110</td>
      <td>5500</td>
      <td>19</td>
      <td>25</td>
      <td>15250</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 26 columns</p>
</div>

由於原始資料的各欄位(Column)並無表頭，因此需先自訂表頭(Header)，並同時將問號('?')轉換成 `NaN`。

```python
headers = ['symboling', 'normalized_losses', 'make', 'fuel_type', 'aspiration',
           'num_doors', 'body_style', 'drive_wheels', 'engine_location',
           'wheel_base', 'length', 'width', 'height', 'curb_weight',
           'engine_type', 'num_cylinders', 'engine_size', 'fuel_system',
           'bore', 'stroke', 'compression_ratio', 'horsepower', 'peak_rpm',
           'city_mpg', 'highway_mpg', 'price']

df = pd.read_csv('http://mlr.cs.umass.edu/ml/machine-learning-databases/autos/imports-85.data',
                  header=None, names=headers, na_values='?')
df.head()
```

如此讀進來的 Dataframe 就會像一般常見的試算表，且問號的部分也轉換成 `NaN`(Not a Number)。

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>symboling</th>
      <th>normalized_losses</th>
      <th>make</th>
      <th>fuel_type</th>
      <th>aspiration</th>
      <th>num_doors</th>
      <th>body_style</th>
      <th>drive_wheels</th>
      <th>engine_location</th>
      <th>wheel_base</th>
      <th>...</th>
      <th>engine_size</th>
      <th>fuel_system</th>
      <th>bore</th>
      <th>stroke</th>
      <th>compression_ratio</th>
      <th>horsepower</th>
      <th>peak_rpm</th>
      <th>city_mpg</th>
      <th>highway_mpg</th>
      <th>price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3</td>
      <td>NaN</td>
      <td>alfa-romero</td>
      <td>gas</td>
      <td>std</td>
      <td>two</td>
      <td>convertible</td>
      <td>rwd</td>
      <td>front</td>
      <td>88.6</td>
      <td>...</td>
      <td>130</td>
      <td>mpfi</td>
      <td>3.47</td>
      <td>2.68</td>
      <td>9.0</td>
      <td>111.0</td>
      <td>5000.0</td>
      <td>21</td>
      <td>27</td>
      <td>13495.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>NaN</td>
      <td>alfa-romero</td>
      <td>gas</td>
      <td>std</td>
      <td>two</td>
      <td>convertible</td>
      <td>rwd</td>
      <td>front</td>
      <td>88.6</td>
      <td>...</td>
      <td>130</td>
      <td>mpfi</td>
      <td>3.47</td>
      <td>2.68</td>
      <td>9.0</td>
      <td>111.0</td>
      <td>5000.0</td>
      <td>21</td>
      <td>27</td>
      <td>16500.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>NaN</td>
      <td>alfa-romero</td>
      <td>gas</td>
      <td>std</td>
      <td>two</td>
      <td>hatchback</td>
      <td>rwd</td>
      <td>front</td>
      <td>94.5</td>
      <td>...</td>
      <td>152</td>
      <td>mpfi</td>
      <td>2.68</td>
      <td>3.47</td>
      <td>9.0</td>
      <td>154.0</td>
      <td>5000.0</td>
      <td>19</td>
      <td>26</td>
      <td>16500.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>164.0</td>
      <td>audi</td>
      <td>gas</td>
      <td>std</td>
      <td>four</td>
      <td>sedan</td>
      <td>fwd</td>
      <td>front</td>
      <td>99.8</td>
      <td>...</td>
      <td>109</td>
      <td>mpfi</td>
      <td>3.19</td>
      <td>3.40</td>
      <td>10.0</td>
      <td>102.0</td>
      <td>5500.0</td>
      <td>24</td>
      <td>30</td>
      <td>13950.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>164.0</td>
      <td>audi</td>
      <td>gas</td>
      <td>std</td>
      <td>four</td>
      <td>sedan</td>
      <td>4wd</td>
      <td>front</td>
      <td>99.4</td>
      <td>...</td>
      <td>136</td>
      <td>mpfi</td>
      <td>3.19</td>
      <td>3.40</td>
      <td>8.0</td>
      <td>115.0</td>
      <td>5500.0</td>
      <td>18</td>
      <td>22</td>
      <td>17450.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 26 columns</p>
</div>

由於本文只會練習 encoding，因此只取需要的 columns 即可。

```python
obj_df = df.select_dtypes(include=['object']).copy()
obj_df = obj_df[['num_doors', 'body_style', 'drive_wheels']]
obj_df.head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>num_doors</th>
      <th>body_style</th>
      <th>drive_wheels</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>two</td>
      <td>convertible</td>
      <td>rwd</td>
    </tr>
    <tr>
      <th>1</th>
      <td>two</td>
      <td>convertible</td>
      <td>rwd</td>
    </tr>
    <tr>
      <th>2</th>
      <td>two</td>
      <td>hatchback</td>
      <td>rwd</td>
    </tr>
    <tr>
      <th>3</th>
      <td>four</td>
      <td>sedan</td>
      <td>fwd</td>
    </tr>
    <tr>
      <th>4</th>
      <td>four</td>
      <td>sedan</td>
      <td>4wd</td>
    </tr>
  </tbody>
</table>
</div>

觀察一下各個 column 的資料型態，可以發現原始資料都是 `object`。

```python
obj_df.dtypes
```
```
num_doors       object
body_style      object
drive_wheels    object
dtype: object
```

接著檢查原始資料中是否有 `NaN`；
這裡可以利用 `DataFrame.isnull()` 檢查 Dataframe 中是否有欄位的值為 `NaN`；
>`.any()`：只要一個欄位為 `True` 即為 `True` <br>
>`.all()`：必須全部欄位為 `True` 才為 `True` <br>
>`.any(axis=1)`：依照 row direction 的方向 <br>
>`.any(axis=0)`：依照 column direction 的方向 <br>

e.g. `DataFrame.isnull().any(axis=1)` 依照 row driection 去檢查是否有 `True` 值，只要該 row 有一個欄位為 `True`，將判定整個 row 的為 `True`，最後將有 `NaN` 的 row 取出，便是以下結果。

```python
obj_df[obj_df.isnull().any(axis=1)]
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>num_doors</th>
      <th>body_style</th>
      <th>drive_wheels</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>27</th>
      <td>NaN</td>
      <td>sedan</td>
      <td>fwd</td>
    </tr>
    <tr>
      <th>63</th>
      <td>NaN</td>
      <td>sedan</td>
      <td>fwd</td>
    </tr>
  </tbody>
</table>
</div>

利用 `Series.value_counts()` 顯示各種值的總數
>Series.size: 該 Series 的大小，包含 `NaN` <br>
>Series.count(): 該 Series 中所有值的欄位總數，不包含 `NaN` <br>
>Series.value_counts(): 該 Series 中，計算各種值的總數，不包含 `NaN` <br>
```python
obj_df['num_doors'].value_counts()
```
```
four    114
two      89
Name: num_doors, dtype: int64
```

再利用 `DataFrame.fillna()` 將資料結構中的 `NaN` 替換成自己想要的值；這裡將 Dataframe 中的 `num_doors` 欄中的 `NaN` 替換成 `four`。
```python
obj_df = obj_df.fillna(value={'num_doors': 'four'})
obj_df['num_doors'].value_counts()
```
```
four    116
two      89
Name: num_doors, dtype: int64
```
再次檢查後發現，`four` 這個值的數量從 `114` 變成 `116`，表示原本的兩個 `NaN` 已經被我成功替換成我想要的值。

> 前面玩了這麼久也只搞定了各欄位的表頭和遺失值而已...

## Methods
經過前面的數據前處理後，我們終於有個可以乾淨的資料結構可以練習了～:metal:
下面將介紹三種方式來處理這個資料：

<div style="text-indent: 2em;">
    1. Find and Replace
</div>
<div style="text-indent: 2em;">
    2. Label Encoding
</div>
<div style="text-indent: 2em;">
    3. One Hot Encoding
</div>

### 1. Find & Replace
不要懷疑，你沒看錯，就是文書處理軟體最常見的方法。
**最適合的方法就是最好的方法！**
>子曰：「割雞焉用牛刀」

`num_doors` 的值其實就是數值，只是機器看不懂，因此這裡的動作就是將 `String` 轉換成相對應的 `Integer`，如：
>`one` -> `1` <br>
>`貳 ` -> `2` <br>
>`三 ` -> `3` <br>

使用 `DataFrame.replace()` 並傳入一個對照表 `to_replace`，即可將文字轉換成數字。
> to_replace: 可傳入 str、regex、list、dict、Series、numeric，這裡用 dict 建立對照表。 <br>
> inplace: 表示直接在原始的資料結構中將值更換掉，這樣就不用再 assign 一個物件，但是要注意的是原始資料將會消失。 <br>

```python
to_replace = {'num_doors': {'four': 4, 'two': 2}}
obj_df.replace(to_replace=to_replace, inplace=True)
obj_df.head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>num_doors</th>
      <th>body_style</th>
      <th>drive_wheels</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>convertible</td>
      <td>rwd</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>convertible</td>
      <td>rwd</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>hatchback</td>
      <td>rwd</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>sedan</td>
      <td>fwd</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>sedan</td>
      <td>4wd</td>
    </tr>
  </tbody>
</table>
</div>

```python
obj_df.dtypes
```
```
num_doors        int64
body_style      object
drive_wheels    object
dtype: object
```

再次檢查資料，可以發現文字都成功轉換成數字，而且 `num_doors` 的資料型態也從 `object` 變成 `int64`。

### 2. Label Encoding
另一個轉換類別型變數(Categorical variables)的方法稱之為 **Label Encoding**，Label Encoding 可以很輕鬆地把文字轉換成數字，如：

- Ordinal variables
	- 小 --> 0
	- 中 --> 1
	- 大 --> 2

- Nominal variables
	- 台北市 --> 0
	- 新北市 --> 1
	- 桃園市 --> 2
	- 台中市 --> 3
	- 台南市 --> 4
	- 高雄市 --> 5

從 Ordinal 來看：
- `大 > 中 > 小` --> `2 > 1 > 0`
- 例如三種尺寸的蘋果各買一顆，平均起來每顆蘋果差不多大；$\frac{0 + 1 + 2}{3} = 1$；

以上看起來都蠻合理。

從 Nominal 來看：
- `台北市 > 新北市 > 桃園市 > 台中市 > 台南市 > 高雄市` --> `5 > 4 > 3 > 2 > 1 > 0`
- 北部優於南部:unamused:？ 六都的平均值 = 2.5，嗯...這是新竹嗎:expressionless:？

以上看起來就有點怪怪的了呦(台中南波萬:thumbsup: ~~我沒有要戰南北~~)，所以 Nominal variable 就比較適合 `One Hot Encoding` 的方法。

> 以下保留，需再確認！ <br>
> 某些文件表示，若是使用 tree-based algorithm 時，這些數字只是類別代號，並無大小順序之別，因此可以用 Label encoding。[ref1](https://vinta.ws/code/feature-engineering.html) [ref2](https://hk.saowen.com/a/1b61dc9734ec055fd42ce2160acc79171cc5ff121248c3ceb44d65c4235feb57)

如此轉換後這些資料就可以丟到演算法裡面產生模型，下面介紹兩個好用的技巧。

#### 2-1. pandas
`pandas` 這一個萬用的~~瑞士刀~~ `package` 中有一個 `Categorical` 的 `class` 。
`Categorical` 物件可以直接建構，也可以從既有的 `Series` 中轉換資料型態。

```python
a1 = obj_df['body_style'] # a1: pandas.Series
print('ori data type(a1):', a1.dtypes)
a2 = obj_df['body_style'].astype('category') # a2: pandas.Series
print('new data type(a2):', a2.dtypes)
```
```
ori data type(a1): object
new data type(a2): category
```

當 `Series` 的 `dtype` 轉換成 `category` 時，才可以使用 `Series.cat.codes` 將原本的文字轉換成數字。
```python
# a1 為 object; a1.cat.codes 不可行
# a2 為 category; 
pd_cat = a2.cat.codes # convert to int from string
print('pd_cat:', pd_cat.tolist()[:5]) # transform the to list from Seires and shows the first 5 values
```
```
pd_cat: [0, 0, 2, 3, 3]
```

若想知道原本的文字被轉換成哪個數字的話，可用 `Series.cat.categories` 查詢。
```python
# pd_cat 所相對應的類別
print(a2.cat.categories)
```
```
Index(['convertible', 'hardtop', 'hatchback', 'sedan', 'wagon'], dtype='object')
```

為了方便閱讀，可以將利用 [enumerate](https://docs.python.org/3/library/functions.html#enumerate) 將上面的 `Index` 丟到 `dictionary` 中。
```python
# 將 pd_cat 所相對應的類別轉換成 dictionary，方便閱讀
a2_classes = dict(enumerate(a2.cat.categories))
print(a2_classes)
```
```
{0: 'convertible', 1: 'hardtop', 2: 'hatchback', 3: 'sedan', 4: 'wagon'}
```

#### 2-2. scikit-learn
另一個方法是利用 `scikit-learn` 中的 `sklearn.preprocessing.LabelEncoder`；而且使用 `LabelEncoder` 時，可直接使用 `object` 的 `Series`，並不需要事先轉換成 `categorical`。

```python
from sklearn.preprocessing import LabelEncoder

le = LabelEncoder() # 建構物件
sk_cat = le.fit_transform(a1) # 直接將資料傳入
print('sk_cat:', sk_cat.tolist()[0:5])
```
```
sk_cat: [0, 0, 2, 3, 3]
```
    
比較一下兩個方法所轉換的結果，可以發現是一模模一樣樣的。
```python
print('pandas :', pd_cat.tolist()[0:10])
print('sklearn:', sk_cat.tolist()[0:10])
```
```
pandas : [0, 0, 2, 3, 3, 3, 3, 4, 3, 2]
sklearn: [0, 0, 2, 3, 3, 3, 3, 4, 3, 2]
```

### 3. One Hot Encoding
Label Encoding 可以很輕鬆且直覺地轉換類別變數，但是它卻有容易讓人誤解的缺點，如先前提到的六都：

- 台北市 --> 0
- 新北市 --> 1
- 桃園市 --> 2
- 台中市 --> 3
- 台南市 --> 4
- 高雄市 --> 5

高雄市比台北市大？台南市是桃園市的兩倍？
***I don't think so.***

因此還有另一種常見的編碼方法稱為 **One Hot Encoding**，這種方法會依照各個類別另外產生新的欄(column)，並且重新指派 1/0(True/False)。
例如，利用原本`居住城市`欄的值，另外新增五個欄`台北市`、`新北市`、`桃園市`、`台中市`、`台南市`、`高雄市`，並且依照原本的值重新指派為 1/0，如下表：

<table style='text-align: center;'>
	<tr>
        <td><b>同學</b></td>
        <td><b>居住城市</b></td>
        <td><b>台北市</b></td>
        <td><b>新北市</b></td>
        <td><b>桃園市</b></td>
        <td><b>台中市</b></td>
        <td><b>台南市</b></td>
        <td><b>高雄市</b></td>
	</tr>
	<tr>
        <td>1號同學</td>
        <td>台北市</td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
	<tr>
        <td>2號同學</td>
        <td>新北市</td>
        <td>0</td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
	<tr>
        <td>3號同學</td>
        <td>桃園市</td>
        <td>0</td>
        <td>0</td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
    </tr>
	<tr>
        <td>4號同學</td>
        <td>台中市</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>1</td>
        <td>0</td>
        <td>0</td>
    </tr>
	<tr>
        <td>5號同學</td>
        <td>台南市</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>1</td>
        <td>0</td>
    </tr>
	<tr>
        <td>6號同學</td>
        <td>高雄市</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>1</td>
    </tr>
</table>

如此一來就不會有前述的問題。但是使用 One Hot Encoding 所產生的另一項缺點則是特徵(Features)的數量就會變得很大，在這裡的居住城市只有 1 個特徵，但是使用 One Hot Encoding 之後的特徵數量就變成至少 6 個。

> 一般而言，當特徵數量變多，可使用 PCA 來降低維度，因此 PCA + One Hot Encoding 的搭配組合很常見。

#### 3-1. pandas
我們在這裡又看到了萬用的~~瑞士刀~~ `package`，`pandas` 很貼心的提供一個方法 `pandas.get_dummies`。

先將需要的欄取出
```python
b = obj_df[['body_style', 'drive_wheels']]
b.head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>body_style</th>
      <th>drive_wheels</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>convertible</td>
      <td>rwd</td>
    </tr>
    <tr>
      <th>1</th>
      <td>convertible</td>
      <td>rwd</td>
    </tr>
    <tr>
      <th>2</th>
      <td>hatchback</td>
      <td>rwd</td>
    </tr>
    <tr>
      <th>3</th>
      <td>sedan</td>
      <td>fwd</td>
    </tr>
    <tr>
      <th>4</th>
      <td>sedan</td>
      <td>4wd</td>
    </tr>
  </tbody>
</table>
</div>

接著就可以直接使用 `get_dummies()`，並且把欲轉換的資料傳入即可。
```python
pd.get_dummies(b, columns=['drive_wheels']).head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>body_style</th>
      <th>drive_wheels_4wd</th>
      <th>drive_wheels_fwd</th>
      <th>drive_wheels_rwd</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>convertible</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>convertible</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>hatchback</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>sedan</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>sedan</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>

轉換完後可以看到三個新增欄分別是
- drive_wheels_4wd
- drive_wheels_rwd
- drive_wheels_fwd

如果你覺得這個欄位名稱又臭又長，當然也可以隨客官喜好來設定。

```python
pd.get_dummies(c, columns=['body_style', 'drive_wheels'], prefix=['body', 'drive']).head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>body_convertible</th>
      <th>body_hardtop</th>
      <th>body_hatchback</th>
      <th>body_sedan</th>
      <th>body_wagon</th>
      <th>drive_4wd</th>
      <th>drive_fwd</th>
      <th>drive_rwd</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>

#### 3-2. scikit-learn
既然萬能的瑞士刀可以辦到，那麼另外一個超強 package `scikit-learn` 應該也有相關的方法囉。
下面介紹 `sklearn.preprocessing.LabelBinarizer`：

```python
from sklearn.preprocessing import LabelBinarizer

lb_style = LabelBinarizer() # 建構物件
lb_results = lb_style.fit_transform(b['body_style']) # 直接將資料傳入
lb_results
```
轉換後的結果會是一個陣列
```
array([[1, 0, 0, 0, 0],
	   [1, 0, 0, 0, 0],
	   [0, 0, 1, 0, 0],
	   ..., 
	   [0, 0, 0, 1, 0],
	   [0, 0, 0, 1, 0],
	   [0, 0, 0, 1, 0]])
```
使用先前所建構的物件的屬性，就可以看到各欄的表頭為何。
```python
lb_style.classes_
```
```
array(['convertible', 'hardtop', 'hatchback', 'sedan', 'wagon'],
          dtype='<U11')
```
由於這樣不太好懂，為了方便閱讀，我利用轉換後的資料建立一個 `DataFrame`。
```python
pd.DataFrame(lb_results, columns=lb_style.classes_).head()
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>convertible</th>
      <th>hardtop</th>
      <th>hatchback</th>
      <th>sedan</th>
      <th>wagon</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>

## Conclusion
Encoding 在資料處理的過程中是一個很重要的程序，由於有很多的方法可以達到目的，因此更應該瞭解不同方法的處理原理以及它如何影響後續的模型。由於 Python 的社群中有許多好用的方法可以處理 Encoding 的問題，至於要選擇哪個方法就端看各位客倌的選擇。

## Reference
- [pandas 0.22.0](https://pandas.pydata.org/pandas-docs/stable/api.html)
- [scikit-learn 0.19.1](http://scikit-learn.org/stable/modules/classes.html)
- [Guide to Encoding Categorical Values in Python](http://pbpython.com/categorical-encoding.html)
- [Statistical Language - What are Variables?](http://www.abs.gov.au/websitedbs/a3121120.nsf/home/statistical+language+-+what+are+variables)

## Lincense
[The MIT License](https://opensource.org/licenses/MIT)

### It's Me
[![GitHub](https://i.imgur.com/Z6a4rDG.png?1)](https://github.com/orcahmlee) [![LinkedIn](https://i.imgur.com/ajGoSNq.png?2)](https://www.linkedin.com/in/orcahmlee)
