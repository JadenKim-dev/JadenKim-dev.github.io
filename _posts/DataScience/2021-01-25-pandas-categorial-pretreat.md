---
title:  "DataFrame 숫자형/범주형 데이터 전처리하기"

categories: 
  - Data Science
tags: 
  - [Data Science, Python, Pandas]

toc: true
toc_sticky: true

date: 2021-01-25
last_modified_at: 2021-01-25
---

DataFrame에 담을 수 있는 정보에는 크게 숫자형 데이터와 범주형 데이터가 있다.   
이 데이터들을 분석에 본격적으로 사용하기 전에, 약간의 전처리 과정이 필요하다.

```python
In)
df = pd.read_csv("./grade_data.csv")
df

Out)
```

|   | StudentId |    Name |  Surname | Class | Gender |        City | Grade | Age | Passed |
|--:|----------:|--------:|---------:|------:|-------:|------------:|------:|----:|-------:|
| 0 |         1 |  Joanne |   Runyon |     1 | female |     Saginaw |   4.2 |  25 |      1 |
| 1 |         2 |  Marita |    Parks |     3 | female |       Chico |   NaN |  29 |      0 |
| 2 |         3 | Richard |   Whalen |     2 |   male |         NaN |   NaN |  24 |      1 |
| 3 |         4 | Barbara |    Primm |     3 | female |     Houston |   1.2 |  23 |      1 |
| 4 |         5 |   Hazel |    Young |     2 | female |     Sheldon |   2.5 |  22 |      1 |
| 5 |         6 |    Dong |    Ramos |     2 |   male |         NaN |   2.7 |  22 |      0 |
| 6 |         7 |    Kyle |    Geary |     1 |   male |         NaN |   3.5 |  23 |      0 |
| 7 |         8 | Jeffery | Caldwell |     1 |   male | Weatherford |   NaN |  24 |      1 |
| 8 |         9 |    Ivan |  Bennett |     3 |   male |     Phoenix |   1.5 |  28 |      1 |

### 1) astype()함수를 통해 데이터타입 바꾸기

Class column의 경우, 각각의 학생데이터가 어느 학급에 속하는지를 나타내는 데이터이다.   
따라서 이 데이터는 범주형 데이터로 다루야 하고, 문자열 데이터로 저장하는 것이 적절하다.

이와 같은 타입변경에는 astype함수를 사용할 수 있다.

```python
In)
df.info()

Out)
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 9 entries, 0 to 8
Data columns (total 9 columns):
 #   Column     Non-Null Count  Dtype  
---  ------     --------------  -----  
 0   StudentId  9 non-null      int64  
 1   Name       9 non-null      object 
 2   Surname    9 non-null      object 
 3   Class      9 non-null      int64  
...
 8   Passed     9 non-null      int64  
dtypes: float64(1), int64(4), object(4)
memory usage: 776.0+ bytes
```

```python
In)
df['Class'] = df['Class'].astype(str)  # Class의 데이터타입을 String으로 변경
df.info()
Out)
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 9 entries, 0 to 8
Data columns (total 9 columns):
 #   Column     Non-Null Count  Dtype  
---  ------     --------------  -----  
 0   StudentId  9 non-null      int64  
 1   Name       9 non-null      object 
 2   Surname    9 non-null      object 
 3   Class      9 non-null      object 
...
 8   Passed     9 non-null      int64  
dtypes: float64(1), int64(3), object(5)
memory usage: 776.0+ bytes
```

### 2) apply() 함수를 통해 column에 변환로직 적용

grade_data에는 'Age' 변수가 존재한다.   
나이에 따라 학생 데이터가 속한 그룹을 나누고자 한다면,   
그에 맞는 변환 로직을 작성하고 apply()함수를 통해 해당 column에 적용하면 된다.

```python
# apply함수 : 특정 컬럼에 변환 로직을 적용할 수 있음
In)
import math
def get_status(age):
    if math.isnan(age):
        return -1
    if(age < 24):
        return "junior"
    else:
        return "senior"
df.insert(4, "Status", df["Age"].apply(get_status))  
df        # apply함수를 통해 만든 "Status" column을, index:4 자리에 삽입

Out)
```

|   | StudentId |    Name |  Surname | Class | Status | Gender |        City | Grade | Age | Passed |
|--:|----------:|--------:|---------:|------:|-------:|-------:|------------:|------:|----:|-------:|
| 0 |         1 |  Joanne |   Runyon |     1 | senior | female |     Saginaw |   4.2 |  25 |      1 |
| 1 |         2 |  Marita |    Parks |     3 | senior | female |       Chico |   NaN |  29 |      0 |
| 2 |         3 | Richard |   Whalen |     2 | senior |   male |         NaN |   NaN |  24 |      1 |
| 3 |         4 | Barbara |    Primm |     3 | junior | female |     Houston |   1.2 |  23 |      1 |
| 4 |         5 |   Hazel |    Young |     2 | junior | female |     Sheldon |   2.5 |  22 |      1 |
| 5 |         6 |    Dong |    Ramos |     2 | junior |   male |         NaN |   2.7 |  22 |      0 |
| 6 |         7 |    Kyle |    Geary |     1 | junior |   male |         NaN |   3.5 |  23 |      0 |
| 7 |         8 | Jeffery | Caldwell |     1 | senior |   male | Weatherford |   NaN |  24 |      1 |
| 8 |         9 |    Ivan |  Bennett |     3 | senior |   male |     Phoenix |   1.5 |  28 |      1 |

### 3) get_dummies()함수를 활용한 One-hot encoding 적용

어떤 그룹에 속하는지를 나타내는 범주형 데이터의 경우에는, 해당 그룹에 속하는지의 유무를 0/1로 나타내는 것이 분석하는데 더 편리하다.   
이러한 형태로 데이터를 자동 생성해주는 함수가 get_dummies()함수이다.

```python
In)
pd.get_dummies(df, columns=["Status", "Class", "Gender"])
# 지정한 칼럼들의 각 범주가 column레벨로 올라옴 
# 0/1로 각 범주에 포함되는지를 나타낸다

Out)
```

|   | StudentId |    Name | ... | Status_junior | Status_senior | Class_1 | Class_2 | Class_3 | Gender_female | Gender_male |
|--:|----------:|--------:|----:|--------------:|--------------:|--------:|--------:|--------:|--------------:|-------------|
| 0 |         1 |  Joanne | ... |             0 |             1 |       1 |       0 |       0 |             1 |           0 |
| 1 |         2 |  Marita | ... |             0 |             1 |       0 |       0 |       1 |             1 |           0 |
| 2 |         3 | Richard | ... |             0 |             1 |       0 |       1 |       0 |             0 |           1 |
| 3 |         4 | Barbara | ... |             1 |             0 |       0 |       0 |       1 |             1 |           0 |
| 4 |         5 |   Hazel | ... |             1 |             0 |       0 |       1 |       0 |             1 |           0 |
| 5 |         6 |    Dong | ... |             1 |             0 |       0 |       1 |       0 |             0 |           1 |
| 6 |         7 |    Kyle | ... |             1 |             0 |       1 |       0 |       0 |             0 |           1 |
| 7 |         8 | Jeffery | ... |             0 |             1 |       1 |       0 |       0 |             0 |           1 |
| 8 |         9 |    Ivan | ... |             0 |             1 |       0 |       0 |       1 |             0 |           1 |