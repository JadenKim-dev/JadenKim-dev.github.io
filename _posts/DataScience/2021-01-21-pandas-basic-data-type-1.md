---
title:  "Pandas 기본 데이터 타입(1) - Series와 DataFrame"

categories: 
  - Data Science
tags: 
  - [Data Science, Python, Pandas]

toc: true
toc_sticky: true

date: 2021-01-21
last_modified_at: 2021-01-25
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
In)
df = pd.read_csv('./grade_data.csv')
df

Out)
```

|   | StudentId |    Name |  Surname | Gender |        City | Grade | Age | Passed |
|--:|----------:|--------:|---------:|-------:|------------:|------:|----:|-------:|
| 0 |         1 |  Joanne |   Runyon | female |     Saginaw |   4.2 |  25 |      1 |
| 1 |         2 |  Marita |    Parks | female |       Chico |   3.9 |  29 |      0 |
| 2 |         3 | Richard |   Whalen |   male |         NaN |   NaN |  24 |      1 |
| 3 |         4 | Barbara |    Primm | female |     Houston |   1.2 |  23 |      1 |
| 4 |         5 |   Hazel |    Young | female |     Sheldon |   2.5 |  22 |      1 |
| 5 |         6 |    Dong |    Ramos |   male |         NaN |   2.7 |  22 |      0 |
| 6 |         7 |    Kyle |    Geary |   male |         NaN |   3.5 |  23 |      0 |
| 7 |         8 | Jeffery | Caldwell |   male | Weatherford |   NaN |  24 |      1 |
| 8 |         9 |    Ivan |  Bennett |   male |     Phoenix |   1.5 |  28 |      1 |

```python
In)
# header=None 지정 → 전체 데이터를 다 값으로 인식
df = pd.read_csv('./grade_data.csv', header=None) 
df

Out)
```

|   |         0 |       1 |        2 |      3 |           4 |     5 |   6 |      7 |
|--:|----------:|--------:|---------:|-------:|------------:|------:|----:|-------:|
| 0 | StudentId |    Name |  Surname | Gender |        City | Grade | Age | Passed |
| 1 |         1 |  Joanne |   Runyon | female |     Saginaw |   4.2 |  25 |      1 |
| 2 |         2 |  Marita |    Parks | female |       Chico |   3.9 |  29 |      0 |
| 3 |         3 | Richard |   Whalen |   male |         NaN |   NaN |  24 |      1 |
| 4 |         4 | Barbara |    Primm | female |     Houston |   1.2 |  23 |      1 |
| 5 |         5 |   Hazel |    Young | female |     Sheldon |   2.5 |  22 |      1 |
| 6 |         6 |    Dong |    Ramos |   male |         NaN |   2.7 |  22 |      0 |
| 7 |         7 |    Kyle |    Geary |   male |         NaN |   3.5 |  23 |      0 |
| 8 |         8 | Jeffery | Caldwell |   male | Weatherford |   NaN |  24 |      1 |
| 9 |         9 |    Ivan |  Bennett |   male |     Phoenix |   1.5 |  28 |      1 |

```python
# 읽어올 Column들을 직접 지정
In)
# 읽어올 Column들을 직접 지정
df = pd.read_csv("./grade_data.csv", index_col="StudentId",
                     usecols=["StudentId", "Name", "Surname", "Grade", "Passed"])
df

Out)
```

|           |    Name |  Surname | Grade | Passed |
|----------:|--------:|---------:|------:|-------:|
| StudentId |         |          |       |        |
|         1 |  Joanne |   Runyon |   4.2 |      1 |
|         2 |  Marita |    Parks |   3.9 |      0 |
|         3 | Richard |   Whalen |   NaN |      1 |
|         4 | Barbara |    Primm |   1.2 |      1 |
|         5 |   Hazel |    Young |   2.5 |      1 |
|         6 |    Dong |    Ramos |   2.7 |      0 |
|         7 |    Kyle |    Geary |   3.5 |      0 |
|         8 | Jeffery | Caldwell |   NaN |      1 |
|         9 |    Ivan |  Bennett |   1.5 |      1 |

```python
In)
df = pd.read_csv("./grade_data.csv")
df.describe()  # 숫자형 데이터에 대해서 기본적인 통계값를 보여줌

Out)
```

|       | StudentId |    Grade |       Age |   Passed |
|------:|----------:|---------:|----------:|---------:|
| count |  9.000000 | 7.000000 |  9.000000 | 9.000000 |
|  mean |  5.000000 | 2.785714 | 24.444444 | 0.666667 |
|   std |  2.738613 | 1.155319 |  2.505549 | 0.500000 |
|   min |  1.000000 | 1.200000 | 22.000000 | 0.000000 |
|   25% |  3.000000 | 2.000000 | 23.000000 | 0.000000 |
|   50% |  5.000000 | 2.700000 | 24.000000 | 1.000000 |
|   75% |  7.000000 | 3.700000 | 25.000000 | 1.000000 |
|   max |  9.000000 | 4.200000 | 29.000000 | 1.000000 |


```python
In)
df.info()  # 각 속성의 데이터 타입과, 데이터 수를 보여줌 

Out)
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 9 entries, 0 to 8
Data columns (total 8 columns):
 #   Column     Non-Null Count  Dtype  
---  ------     --------------  -----  
 0   StudentId  9 non-null      int64  
 1   Name       9 non-null      object 
 2   Surname    9 non-null      object 
 3   Gender     9 non-null      object 
 4   City       6 non-null      object 
 5   Grade      7 non-null      float64
 6   Age        9 non-null      int64  
 7   Passed     9 non-null      int64  
dtypes: float64(1), int64(3), object(4)
memory usage: 704.0+ bytes
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