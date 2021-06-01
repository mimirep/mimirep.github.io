---
layout: post
title: "Numerical Indicator Practice(1)"
tags: [Statistics, Python]
comments: true
---

### 1. 목적

​	오늘은 숫자군의 수준(Levels), 중심(center)에 대해서 공부했다. 중심에 관한 수치지표를 활용하여 비교하는 것은 통계 분석에 있어서 필수적이다. 해당 목차에서는 Levels에 대한 지표로 평균(Mean), 중앙값(Median), 절사평균(Trim Mean)을 제시하였는데 모두 장단점들이 존재하는 지표이다. Mean의 경우 가장 잘 알려져있는 지표지만 이상점에 대한 저항이 없다는 큰 단점이 존재한다. 이에 반해 Median은 이상점에 대한 저항을 가지고 있다. Trim Mean은 이상점에 대한 저항이 없는 Mean을 보완한 것인데 가장 높은 숫자와 가장 낮은 숫자를 일정한 비율로 제거하여 이상점에 대해 대응하는 것이다. 이 때 제거할 일정한 비율을 절사율이라고 하며 통상적으로 5의 배수 단위로 많이 사용한다. 이러한 중심에 대한 수치지표는 자료별 특징에 따라 적절하게 사용하는 것이 필요하다.

​	해당 목차의 연습문제는 이전에 사용한 Pine ridge Cave와 Flat Willow에서 출토된 긁개일괄을 제시하였고, 필자는 이를 CSV형식으로 변환하여 이번 연습에 사용하였다.

<br>

### 2. 코드

```python
import pandas as pd
import numpy as np
from scipy import stats

data = pd.read_csv("/Users/jch/Documents/github/Study/Statistics_for_Archaeologists/Part1-1.csv")

is_data_pr = data['Site'] == 'PR'
data_PR = data[is_data_pr]

is_data_wf = data['Site'] == "WF"
data_WF = data[is_data_wf]

is_data_Chert = data['Stone type'] == "Chert"
data_Chert = data[is_data_Chert]

is_data_Flint = data['Stone type'] == 'Flint'
data_Flint = data[is_data_Flint]

#연습문제1
PR_mean = data_PR['Length(mm)'].mean()
PR_median = data_PR['Length(mm)'].median()
PR_trim_mean = stats.trim_mean(data_PR['Length(mm)'], 0.05)

WF_mean = data_WF['Length(mm)'].mean()
WF_median = data_WF['Length(mm)'].median()
WF_trim_mean = stats.trim_mean(data_WF['Length(mm)'], 0.05)

print("PR 평균 : %0.3f" % PR_mean)
print("PR 중앙값 : %0.3f" % PR_median)
print("PR 절사평균(0.05) : %0.3f" % PR_trim_mean)

print("WF 평균 : %0.3f" % WF_mean)
print("WF 중앙값 : %0.3f" % WF_median)
print("WF 절사평균 : %0.3f" % WF_trim_mean)

#연습문제2
Chert_mean = data_Chert['Length(mm)'].mean()
Chert_median = data_Chert['Length(mm)'].median()
Chert_trim_mean = stats.trim_mean(data_Chert['Length(mm)'], 0.05)

Flint_mean = data_Flint['Length(mm)'].mean()
Flint_median = data_Flint['Length(mm)'].median()
Flint_trim_mean = stats.trim_mean(data_Flint['Length(mm)'], 0.05)

print("Chert 평균 : %0.3f" % Chert_mean)
print("Chert 중앙값 : %0.3f" % Chert_median)
print("Chert 절사평균(0.05) : %0.3f" % Chert_trim_mean)

print("Flint 평균 : %0.3f" % Flint_mean)
print("Flint 중앙값 : %0.3f" % Flint_median)
print("Flint 절사평균 : %0.3f" % Flint_trim_mean)
```

​	 Jupyter Notebook 환경에서 연습을 진행했으며 Pandas, Numpy, Scipy 라이브러리를 사용하였다. 가장 먼저 사용할 라이브러리들을 import 해주었고 pd.read_csv를 사용하여 미리 만들어놓은 csv파일을 불러왔다. 이후 연습문제에서 요구한 것 처럼 출토지(Site), 석재의 종류(Stone Type)별로 구분하는 데이터 전처리 과정을 실행하였다. 

<br>

### 3. 연습문제 풀이

```python
#연습문제1
PR_mean = data_PR['Length(mm)'].mean()
PR_median = data_PR['Length(mm)'].median()
PR_trim_mean = stats.trim_mean(data_PR['Length(mm)'], 0.05)

WF_mean = data_WF['Length(mm)'].mean()
WF_median = data_WF['Length(mm)'].median()
WF_trim_mean = stats.trim_mean(data_WF['Length(mm)'], 0.05)

print("PR 평균 : %0.3f" % PR_mean)
print("PR 중앙값 : %0.3f" % PR_median)
print("PR 절사평균(0.05) : %0.3f" % PR_trim_mean)

print("WF 평균 : %0.3f" % WF_mean)
print("WF 중앙값 : %0.3f" % WF_median)
print("WF 절사평균 : %0.3f" % WF_trim_mean)
```

​	연습문제1은 출토지별로 구분하고 이후 평균, 중앙값, 절사평균을 산출한 뒤 가장 적절한 지표를 찾는 문제였다. 이를 위해 이전에 전처리 해놓은 데이터를 활용해 출토지별로 평균과 중앙값, 절사평균을 산출하여 비교해보았다. 산출된 값들은 모두 소수점 3번째 자리까지 출력되도록 하였다.

​	Pine ridge Cave에서 출토된 긁개들의 경우 2개 내지 3개의 이상점이 존재하는데 그로 인해 평균은 실효성이 없을 것으로 보이고 중앙값 혹은 5% 이상, 50% 이하 절사율의 절사평균이 유효할 것으로 보인다.

​	Flat Willow에서 출토된 긁개들의 경우 1개의 이상점이 보이는데 중앙값을 사용하기보다는 5%의 절사율의 절사평균을 사용하거나 아예 이상점을 지우고 분석하는 것이 유효해 보인다.

​	하단은 코드를 실행한 결과이다.

```
PR 평균 : 23.429
PR 중앙값 : 22.000
PR 절사평균(0.05) : 23.429
WF 평균 : 34.632
WF 중앙값 : 39.400
WF 절사평균 : 33.493
```

<br>

```python
#연습문제2
Chert_mean = data_Chert['Length(mm)'].mean()
Chert_median = data_Chert['Length(mm)'].median()
Chert_trim_mean = stats.trim_mean(data_Chert['Length(mm)'], 0.05)

Flint_mean = data_Flint['Length(mm)'].mean()
Flint_median = data_Flint['Length(mm)'].median()
Flint_trim_mean = stats.trim_mean(data_Flint['Length(mm)'], 0.05)

print("Chert 평균 : %0.3f" % Chert_mean)
print("Chert 중앙값 : %0.3f" % Chert_median)
print("Chert 절사평균(0.05) : %0.3f" % Chert_trim_mean)

print("Flint 평균 : %0.3f" % Flint_mean)
print("Flint 중앙값 : %0.3f" % Flint_median)
print("Flint 절사평균 : %0.3f" % Flint_trim_mean)
```

​	연습문제2는 석재의 종류별로 구분하고 이후 평균, 중앙값, 절사평균을 산출한 뒤 가장 적절한 지표를 찾는 문제였다. 앞선 문제와 같이 이를 위해 이전에 전처리 해놓은 데이터를 활용해 출토지별로 평균과 중앙값, 절사평균을 산출하여 비교해보았다. 산출된 값들은 모두 소수점 3번째 자리까지 출력되도록 하였다.

​	Chert로 제작된 긁개들의 경우 이상점이 없다고 볼 수 있으며 때문에 평균, 중앙값, 절사평균 모두 유효해보인다.

​	Flint로 제작된 긁개들의 경우 1개의 큰 이상점이 보이는데 이로인해 평균은 효과적이지 못하며 중앙값 혹은 절사평균이 유효하다고 생각된다. 절사평균의 경우 이상점의 수가 적기 때문에 5%의 절사율도 충분하다고 생각된다.

​	하단은 코드를 실행한 결과이다.

```
Chert 평균 : 18.387
Chert 중앙값 : 19.650
Chert 절사평균(0.05) : 18.595
Flint 평균 : 42.942
Flint 중앙값 : 42.000
Flint 절사평균 : 41.509
```

<br>

