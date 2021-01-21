---
title:  "Pandas 기본 데이터 타입(1) - Series와 DataFrame"

categories: 
  - Data Science
tags: 
  - [Data Science, Python, Pandas]

toc: true
toc_sticky: true

date: 2021-01-21
last_modified_at: 2021-01-21
---
## 1. **Series**

Pandas에서 1차원 배열을 나타내는 데이터 타입이다.   numpy의 ndarray 기반으로 인덱싱 기능이 추가되어있다.

**Init signature:**   
pd.Series(data=None, index=None, dtype=None, name=None, copy=False, fastpath=False,)

### 1) Series 데이터 생성방법 : data, index 순으로 지정

```python
In) 
s = pd.Series([1, 2, 3])  # value를 지정 → index가 자동적으로 생성된다.
s

Out)
0    1
1    2
2    3
dtype: int64
```

```python
In)
s = pd.Series(['a', 'b', 'c'])  # 데이터를 명시하면 pandas가 자동으로 타입을 유추  
s                               #  → 그에 맞게 dtype이 정해짐

Out)
0    a
1    b
2    c
dtype: object
```

```python
In)
s = pd.Series([1, 2, 3], ['a', 'c', 'f'])  # data, index 함께 지정
s           #   data          index      순서 주의!!

Out)
a    1
c    2
f    3
dtype: int64
```

```python
In)
s = pd.Series(np.arange(5), np.arange(100, 105))
s

Out)
100    0
101    1
102    2
103    3
104    4
dtype: int32

In)
s6 [105] = 55  # 해당 인덱스가 없으면, 인덱스를 만들어서 값을 넣는다.
s6 [200] = 88
s6

Out)
100     0
… 
104     4
105    55
200    88
dtype: int64
```

### 2) Series 데이터의 연산 : 순서와 상관없이, 인덱스에 맞춰서!

```python
In)
s1 = pd.Series([1, 2, 3, 4], ['a', 'b', 'c', 'd'])
s2 = pd.Series([7, 5, 2, 1], ['d', 'c', 'b', 'a'])
print(s1)
print(s2)

Out) 
a    1
b    2
c    3
d    4
dtype: int64

d    7
c    5
b    2
a    1
dtype: int64

In)
s1 + s2     # 같은 인덱스끼리 연산이 이루어짐
Out)
a     2
b     4
c     8
d    11
dtype: int64
```

```python
# index pair가 맞지 않는 경우 - 해당 index에 대해선 NaN 값 생성
In)
s1['k'] = 7
s2['e'] = 9
print(s1)
print(s2)

Out)
a    1
b    2
c    3
d    4
k    7
dtype: int64

d    7
c    5
b    2
a    1
e    9
dtype: int64

In)
s1 + s2
Out)
a     2.0
b     4.0
c     8.0
d    11.0
e     NaN     #NaN 생성
k     NaN     #NaN 생성
dtype: float64
```

## 2. DataFrame

2차원 데이터를 나타내는 데이터타입

### 1) DataFrame 읽어오기 & 정보 확인

```python
# csv 데이터 불러오기
# https://www.kaggle.com/hesh97/titanicdataset-traincsv/data, 타이타닉 데이터 이용
In)
data = pd.read_csv('./train.csv')
data.head()
```

|   | PassengerId | Survived | Pclass |                                              Name |    Sex |  Age | SibSp | Parch |           Ticket |    Fare | Cabin | Embarked |
|--:|------------:|---------:|-------:|--------------------------------------------------:|-------:|-----:|------:|------:|-----------------:|--------:|------:|---------:|
| 0 |           1 |        0 |      3 |                           Braund, Mr. Owen Harris |   male | 22.0 |     1 |     0 |        A/5 21171 |  7.2500 |   NaN |        S |
| 1 |           2 |        1 |      1 | Cumings, Mrs. John Bradley (Florence Briggs Th... | female | 38.0 |     1 |     0 |         PC 17599 | 71.2833 |   C85 |        C |
| 2 |           3 |        1 |      3 |                            Heikkinen, Miss. Laina | female | 26.0 |     0 |     0 | STON/O2. 3101282 |  7.9250 |   NaN |        S |
| 3 |           4 |        1 |      1 |      Futrelle, Mrs. Jacques Heath (Lily May Peel) | female | 35.0 |     1 |     0 |           113803 | 53.1000 |  C123 |        S |
| 4 |           5 |        0 |      3 |                          Allen, Mr. William Henry |   male | 35.0 |     0 |     0 |           373450 |  8.0500 |   NaN |        S |

```python
# header=None 지정 → 전체 데이터를 다 값으로 인식
data = pd.read_csv('./train.csv', header=None) 
data.head()
```

|   |           0 |        1 |      2 |                                                 3 |      4 |   5 |     6 |     7 |                8 |       9 |    10 |       11 |
|--:|------------:|---------:|-------:|--------------------------------------------------:|-------:|----:|------:|------:|-----------------:|--------:|------:|---------:|
| 0 | PassengerId | Survived | Pclass |                                              Name |    Sex | Age | SibSp | Parch |           Ticket |    Fare | Cabin | Embarked |
| 1 |           1 |        0 |      3 |                           Braund, Mr. Owen Harris |   male |  22 |     1 |     0 |        A/5 21171 |    7.25 |   NaN |        S |
| 2 |           2 |        1 |      1 | Cumings, Mrs. John Bradley (Florence Briggs Th... | female |  38 |     1 |     0 |         PC 17599 | 71.2833 |   C85 |        C |
| 3 |           3 |        1 |      3 |                            Heikkinen, Miss. Laina | female |  26 |     0 |     0 | STON/O2. 3101282 |   7.925 |   NaN |        S |
| 4 |           4 |        1 |      1 |      Futrelle, Mrs. Jacques Heath (Lily May Peel) | female |  35 |     1 |     0 |           113803 |    53.1 |  C123 |        S |

```python
# 읽어올 Column들을 직접 지정
data = pd.read_csv('./train.csv', index_col='PassengerId', 
                    usecols=['PassengerId', 'Survived', 'Pclass', 'Name', 'Age'])  
data.head()
```

| PassengerId | Survived | Pclass |                                              Name |  Age |
|------------:|---------:|-------:|--------------------------------------------------:|-----:|
|           1 |        0 |      3 |                           Braund, Mr. Owen Harris | 22.0 |
|           2 |        1 |      1 | Cumings, Mrs. John Bradley (Florence Briggs Th... | 38.0 |
|           3 |        1 |      3 |                            Heikkinen, Miss. Laina | 26.0 |
|           4 |        1 |      1 |      Futrelle, Mrs. Jacques Heath (Lily May Peel) | 35.0 |
|           5 |        0 |      3 |                          Allen, Mr. William Henry | 35.0 |

```python
In)
data.describe()  # 숫자형 데이터에 대해서 기본적인 통계값를 보여줌
```

Out)

|    　 | PassengerId | Survived |   Pclass |       Age |    SibSp |    Parch |      Fare |
|------:|------------:|---------:|---------:|----------:|---------:|---------:|----------:|
| count |         891 |      891 |      891 |       714 |      891 |      891 |       891 |
|  mean |         446 | 0.383838 | 2.308642 | 29.699118 | 0.523008 | 0.381594 | 32.204208 |
|   std |  257.353842 | 0.486592 | 0.836071 | 14.526497 | 1.102743 | 0.806057 | 49.693429 |
|   min |           1 |        0 |        1 |      0.42 |        0 |        0 |         0 |
|   25% |       223.5 |        0 |        2 |    20.125 |        0 |        0 |    7.9104 |
|   50% |         446 |        0 |        3 |        28 |        0 |        0 |   14.4542 |
|   75% |       668.5 |        1 |        3 |        38 |        1 |        0 |        31 |
|   max |         891 |        1 |        3 |        80 |        8 |        6 |  512.3292 |

```python
In)
train_data.info()  # 각 속성의 데이터 타입과, 데이터 수를 보여줌 

Out)
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
 #   Column       Non-Null Count  Dtype  
---  ------       --------------  -----  
 0   PassengerId  891 non-null    int64  
 1   Survived     891 non-null    int64  
 2   Pclass       891 non-null    int64  
 3   Name         891 non-null    object 
 4   Sex          891 non-null    object 
 5   Age          714 non-null    float64
 6   SibSp        891 non-null    int64  
 7   Parch        891 non-null    int64  
 8   Ticket       891 non-null    object 
 9   Fare         891 non-null    float64
 10  Cabin        204 non-null    object 
 11  Embarked     889 non-null    object 
dtypes: float64(2), int64(5), object(5)
memory usage: 83.7+ KB
```

### 2) DataFrame 데이터 생성

- dictionary로부터 생성하기   dict의 key → column,  value → data로 인식됨

```python
# value 부분에 상수 입력 → 해당 열의 모든 데이터에 같은 값이 들어감
In)
data = {'a': 10, 'b': 20, 'c': 30}   # 'a', 'b', 'c'를 변수의 이름으로 지정
pd.DataFrame(data, index={'x', 'y', 'z'})   # 각 데이터의 이름을 'x', 'y', 'z'로 지정

Out)
```

| a |  b |  c |
|--:|---:|---:|
| z | 10 | 20 |
| x | 10 | 20 |
| y | 10 | 20 |

```python
# value 부분에 리스트 입력 → 데이터로 각 리스트의 원소가 저장됨
In)
data = {'a' : [1, 2, 3], 'b': [4, 5, 6], 'c': [10, 11, 12]}  # 리스트로 값 지정
pd.DataFrame(data)  # 인덱스를 지정하지 않는 것도 가능

Out)
```

|   | a | b |  c |
|--:|--:|--:|---:|
| 0 | 1 | 4 | 10 |
| 1 | 2 | 5 | 11 |
| 2 | 3 | 6 | 12 |

- Series로부터 생성하기

```python
In)
a = pd.Series([10, 20, 30], ['a', 'b', 'c'])
b = pd.Series([11, 21, 31], ['a', 'b', 'd'])  
c = pd.Series([100, 200, 300], ['a', 'b', 'c'])
pd.DataFrame([a, b, c], ['ap', 'ba', 'ki'])

Out)
```

|    |     a |     b |     c |    d |
|---:|------:|------:|------:|-----:|
| ap |  10.0 |  20.0 |  30.0 |  NaN |
| ba |  11.0 |  21.0 |   NaN | 31.0 |
| ki | 100.0 | 200.0 | 300.0 |  NaN |