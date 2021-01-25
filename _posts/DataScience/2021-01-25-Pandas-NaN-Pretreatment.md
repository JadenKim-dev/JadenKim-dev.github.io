---
title:  "DataFrame NaN 데이터 전처리하기"

categories: 
  - Data Science
tags: 
  - [Data Science, Python, Pandas]

toc: true
toc_sticky: true

date: 2021-01-25
last_modified_at: 2021-01-25
---

데이터 누락, 소실 등 다양한 이유로 NaN데이터가 발생한다.   
NaN데이터를 포함한 상태로 분석을 진행할 경우, 특정 함수들의 경우 아예 에러를 발생시키기도 하고,   
그냥 방치할 경우 오히려 왜곡을 발생시킬 우려도 있기 때문에, 이에 대한 처리가 필요하다.

```python
# csv 데이터 불러오기
In)
df = pd.read_csv("./grade_data.csv")
df

Out)
```

|   | StudentId |    Name |  Surname | Gender |        City | Grade | Age | Passed |
|--:|----------:|--------:|---------:|-------:|------------:|------:|----:|-------:|
| 0 |         1 |  Joanne |   Runyon | female |     Saginaw |   4.2 |  25 |      1 |
| 1 |         2 |  Marita |    Parks | female |       Chico |   NaN |  29 |      0 |
| 2 |         3 | Richard |   Whalen |   male |         NaN |   NaN |  24 |      1 |
| 3 |         4 | Barbara |    Primm | female |     Houston |   1.2 |  23 |      1 |
| 4 |         5 |   Hazel |    Young | female |     Sheldon |   2.5 |  22 |      1 |
| 5 |         6 |    Dong |    Ramos |   male |         NaN |   2.7 |  22 |      0 |
| 6 |         7 |    Kyle |    Geary |   male |         NaN |   3.5 |  23 |      0 |
| 7 |         8 | Jeffery | Caldwell |   male | Weatherford |   NaN |  24 |      1 |
| 8 |         9 |    Ivan |  Bennett |   male |     Phoenix |   1.5 |  28 |      1 |

## 1) isna()를 통해 NaN데이터 확인하기

isna()는 NaN데이터에 대해서는 True를, 값이 있는 데이터에 대해서는 False를 반환한다.

```python
In)
df.isna()

Out)
```

|   | StudentId |  Name | Surname | Gender |  City | Grade |   Age | Passed |
|--:|----------:|------:|--------:|-------:|------:|------:|------:|-------:|
| 0 |     False | False |   False |  False | False | False | False |  False |
| 1 |     False | False |   False |  False | False | True | False |  False |
| 2 |     False | False |   False |  False |  True |  True | False |  False |
| 3 |     False | False |   False |  False | False | False | False |  False |
| 4 |     False | False |   False |  False | False | False | False |  False |
| 5 |     False | False |   False |  False |  True | False | False |  False |
| 6 |     False | False |   False |  False |  True | False | False |  False |
| 7 |     False | False |   False |  False | False |  True | False |  False |
| 8 |     False | False |   False |  False | False | False | False |  False |

```python
In)
df['Grade'].isna()

Out)
0    False
1     True
2     True
3    False
4    False
5    False
6    False
7     True
8    False
Name: Grade, dtype: bool
```

## 2) dropna()를 통해 NaN데이터 삭제하기

row기반으로 동작하여, column 중 nan인 값이 하나라도 있으면 해당 데이터(row)를 삭제한다.    column기반 삭제를 원할 시, axis=1로 지정 가능

```python
In)
df.dropna()   # NaN데이터가 포함된 row를 삭제

Out
```

|   | StudentId |    Name | Surname | Gender |    City | Grade | Age | Passed |
|--:|----------:|--------:|--------:|-------:|--------:|------:|----:|-------:|
| 0 |         1 |  Joanne |  Runyon | female | Saginaw |   4.2 |  25 |      1 |
| 3 |         4 | Barbara |   Primm | female | Houston |   1.2 |  23 |      1 |
| 4 |         5 |   Hazel |   Young | female | Sheldon |   2.5 |  22 |      1 |
| 8 |         9 |    Ivan | Bennett |   male | Phoenix |   1.5 |  28 |      1 |

```python
In)
df.dropna(subset=["Grade"])  # Grade에 NaN값이 있는 row에 대해서만 데이터 삭제

Out)
```

|   | StudentId |    Name | Surname | Gender |    City | Grade | Age | Passed |
|--:|----------:|--------:|--------:|-------:|--------:|------:|----:|-------:|
| 0 |         1 |  Joanne |  Runyon | female | Saginaw |   4.2 |  25 |      1 |
| 3 |         4 | Barbara |   Primm | female | Houston |   1.2 |  23 |      1 |
| 4 |         5 |   Hazel |   Young | female | Sheldon |   2.5 |  22 |      1 |
| 5 |         6 |    Dong |   Ramos |   male |     NaN |   2.7 |  22 |      0 |
| 6 |         7 |    Kyle |   Geary |   male |     NaN |   3.5 |  23 |      0 |
| 8 |         9 |    Ivan | Bennett |   male | Phoenix |   1.5 |  28 |      1 |

```python
In)
df.dropna(axis=1)  # column wise로 NaN데이터 삭제 --> 하나라도 NaN데이터가 있는 column는 삭제

Out)
```

|   | StudentId |    Name |  Surname | Gender | Age | Passed |
|--:|----------:|--------:|---------:|-------:|----:|-------:|
| 0 |         1 |  Joanne |   Runyon | female |  25 |      1 |
| 1 |         2 |  Marita |    Parks | female |  29 |      0 |
| 2 |         3 | Richard |   Whalen |   male |  24 |      1 |
| 3 |         4 | Barbara |    Primm | female |  23 |      1 |
| 4 |         5 |   Hazel |    Young | female |  22 |      1 |
| 5 |         6 |    Dong |    Ramos |   male |  22 |      0 |
| 6 |         7 |    Kyle |    Geary |   male |  23 |      0 |
| 7 |         8 | Jeffery | Caldwell |   male |  24 |      1 |
| 8 |         9 |    Ivan |  Bennett |   male |  28 |      1 |

## 3) fillna()를 통해 NaN 데이터 대체하기

NaN데이터를 특정 값으로 대체할 수 있다. 일반적으로 평균이나 중위값으로 대체한다.

```python
In)
gradeMean = df["Grade"].mean()
print(gradeMean)

Out)
2.7857142857142856

In)
df['Grade'].fillna(gradeMean)

Out)
0    4.200000
1    3.900000
2    2.785714
3    1.200000
4    2.500000
5    2.700000
6    3.500000
7    2.785714
8    1.500000
Name: Grade, dtype: float64
```

```python
In)
passedGradeMean = df.loc[df['Passed']==1, 'Grade'].mean()  # 합격자 성적 평균
failedGradeMean = df.loc[df['Passed']==0, 'Grade'].mean()  # 불합격자 성적 평균
print(passedGradeMean, failedGradeMean)

Out)
2.35 3.1

In)
# 합격자의 NaN 데이터를 합격자 성적 평균으로 대체
df.loc[df['Passed']==1, 'Grade'] = df.loc[df['Passed']==1, 'Grade'].fillna(passedGradeMean)
# 불합격자의 NaN 데이터를 불합격자 성적 평균으로 대체
df.loc[df['Passed']==0, 'Grade'] = df.loc[df['Passed']==0, 'Grade'].fillna(failedGradeMean)
df

Out)
```

|   | StudentId |    Name |  Surname | Gender |        City | Grade | Age | Passed |
|--:|----------:|--------:|---------:|-------:|------------:|------:|----:|-------:|
| 0 |         1 |  Joanne |   Runyon | female |     Saginaw |  4.20 |  25 |      1 |
| 1 |         2 |  Marita |    Parks | female |       Chico |  3.10 |  29 |      0 |
| 2 |         3 | Richard |   Whalen |   male |         NaN |  2.35 |  24 |      1 |
| 3 |         4 | Barbara |    Primm | female |     Houston |  1.20 |  23 |      1 |
| 4 |         5 |   Hazel |    Young | female |     Sheldon |  2.50 |  22 |      1 |
| 5 |         6 |    Dong |    Ramos |   male |         NaN |  2.70 |  22 |      0 |
| 6 |         7 |    Kyle |    Geary |   male |         NaN |  3.50 |  23 |      0 |
| 7 |         8 | Jeffery | Caldwell |   male | Weatherford |  2.35 |  24 |      1 |
| 8 |         9 |    Ivan |  Bennett |   male |     Phoenix |  1.50 |  28 |      1 |

```python
df[df['Grade'] == passedGradeMean]  # NaN에서 합격자 평균으로 대체된 경우를 필터링
```

|   | StudentId |    Name |  Surname | Gender |        City | Grade | Age | Passed |
|--:|----------:|--------:|---------:|-------:|------------:|------:|----:|-------:|
| 2 |         3 | Richard |   Whalen |   male |         NaN |  2.35 |  24 |      1 |
| 7 |         8 | Jeffery | Caldwell |   male | Weatherford |  2.35 |  24 |      1 |