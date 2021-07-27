---
layout: post
title: "Chi-square_test Practice(2)"
tags: [Statistics, Python]
comments: true
---

### 1. 목적

​	지난 번에 사용했던 예제를 이번에는 R이 아닌 Python을 활용하여 풀어보고자 하였다. 애초에 주로 사용하던 언어가 Python이기도 하고, Python도 통계에서 자주 사용되는 만큼 익혀두면 좋을 것 같다. pandas, numpy, scipy, matplotlib와 같은 라이브러리들이 거의 필수적으로 사용되는데, numpy는 어느정도 써본 경험이 있지만 scipy, matplotlib는 연습이 필요해서 이번 기회를 통해 연습해보고자 하였다. Chi-square 검정과 본 글에서 사용된 예제에 대해서는 이전 글을 참고하길 바란다

<img src="https://github.com/ChanToRe/ChanToRe.github.io/blob/master/images/2020-10-20/chi-squared-test.png?raw=true" width="60%" height="60%"/>

<br>

### 2. 코드

```python
import math
import pandas as pd
import numpy as np
from numpy import linspace
import scipy.stats
import matplotlib.pyplot as plt

data = pd.read_csv("/Users/jch/Desktop/test.csv") #data load

#교차분할표
crosstab = pd.crosstab(data.Sex, data.RorL)
print(crosstab)
print("-------------------------------------------")

#카이제곱검정
##연속성 수정 O
chi2, pvalue, df, expected = scipy.stats.chi2_contingency(crosstab, correction=True)
print('chi^2 = ', chi2) #x2
print('p-value = ', pvalue) #p-value
print('자유도 = ', df) #자유도
print('e-expected = ', expected) #expected value
print("-------------------------------------------")
##연속성 수정 X
chi2_2, pvalue_2, df_2, expected_2 = scipy.stats.chi2_contingency(crosstab, correction=False)
print('chi^2 = ', chi2_2) #x2
print('p-value = ', pvalue_2) #p-value
print('자유도 = ', df_2) #자유도
print('e-expected = ', expected_2) #expected value
print("-------------------------------------------")

#카이제곱분포
chi2_graph = np.linspace(0.5, 50, 200)
y = (1 / (math.gamma(df / 2) * (1 / 2 ** (df / 2)))) * (chi2_graph ** ( df / 2 - 1)) * np.exp(- chi2_graph / 2)

#시각화
plt.figure(figsize=(6, 6))
plt.plot(chi2_graph, y, label=r'$\chi^2-distribution$')
plt.axvline(x=chi2, color='red', linestyle = ':')
plt.axvline(x=chi2_2, color='blue', linestyle = ':')
plt.text(chi2, .4, '  result1($\chi^2$)  \n=  ' + str(round(chi2, 4)) + '  ', horizontalalignment='right', color='red')
plt.text(chi2_2, .6, '  result2($\chi^2$)\n  =' + str(round(chi2_2, 4)), horizontalalignment='left', color='blue')
plt.xlabel(r'$\chi^2$')
plt.ylabel('y')
plt.grid()
plt.title('$\chi^2$ (df=1)')
plt.legend()
plt.show()
```

​	전체코드이며 Python으로 작성했다. 전체적인 순서는 먼저 데이터를 불러온 뒤 교차분할표를 작성한다. 이후 교차분할표를 바탕으로 카이제곱검정을 실시하고 카이제곱분포도를 작성한 뒤, 시각화 작업을 진행했다.

​	라이브러리는 math, pandas, numpy, scipy, matplotlib를 사용하였다.

<br>

```python
data = pd.read_csv("/Users/jch/Desktop/test.csv") #data load

#교차분할표
crosstab = pd.crosstab(data.Sex, data.RorL)
print(crosstab)
```

​	제일 먼저 데이터셋을 불러와야한다. 별도의 예제 데이터셋이 없어서 .csv 파일을 직접 만들어서 사용하였다. 저작권 문제로 데이터를 제공하지는 않을 것이다.

​	이전에 R을 사용할 때는 데이터의 자료형을 바꿔주고 리코딩한 후 교차분할표를 작성하는 과정을 거쳤다. 하지만 Python을 사용하면 그럴 필요가 없다. pandas의 crosstab 기능을 사용하면 간단하게 교차분할표를 작성할 수 있다.

<br>

```python
#카이제곱검정
##연속성 수정 O
chi2, pvalue, df, expected = scipy.stats.chi2_contingency(crosstab, correction=True)
print('chi^2 = ', chi2) #x2
print('p-value = ', pvalue) #p-value
print('자유도 = ', df) #자유도
print('e-expected = ', expected) #expected value
print("-------------------------------------------")
##연속성 수정 X
chi2_2, pvalue_2, df_2, expected_2 = scipy.stats.chi2_contingency(crosstab, correction=False)
print('chi^2 = ', chi2_2) #x2
print('p-value = ', pvalue_2) #p-value
print('자유도 = ', df_2) #자유도
print('e-expected = ', expected_2) #expected value
print("-------------------------------------------")
```

​	작성한 교차분할표를 활용하여 카이제곱검정을 실시했다. scipy의 chi2_contingency를 활용하면 작성해놓은 교차분할표를 활용하여 간단하게 카이제곱검정을 실시할 수 있다. 해당 검정을 실시하면 chi^2, p(p-value), df(dof), e(e-expected value)를 산출할 수 있다.

​	사실 처음에 14.XX라는 값을 얻어서 교수님께 정답이 맞는지 질문을 드렸었다. 교수님께서 "해당 값은 연속성이 수정된 값이지만 큰 차이는 없다. 라이브러리를 찾아보면 연속성 수정 여부를 선택할 수 있을거다"라고 답변해주셔서 scipy의 기능들을 살펴보았고 'correction='을 통해 연속성 수정 여부를 선택할 수 있다는 것을 알게 되었다. correction=True의 경우 연속성 수정이 이루어진 값, 14.109이며 correction=False의 경우 연속성 수정이 이루어지지 않은 값, 15.7714이다. 

<br>

```python
#카이제곱분포
chi2_graph = np.linspace(0.5, 50, 200)
y = (1 / (math.gamma(df / 2) * (1 / 2 ** (df / 2)))) * (chi2_graph ** ( df / 2 - 1)) * np.exp(- chi2_graph / 2)
```

​	앞선 작업으로 사실상 카이제곱검정은 모두 이루어진 것이다. 하지만 시각적으로 보기 좋게 그래프로 그리고자 한다. 먼저 카이제곱분포도를 작성한 뒤 도출된 값을 분포도 위에 표시하고자 하였다. 위 코드에 있는 수식을 활용하면 카이제곱분포도를 그릴 수 있다. 

<br>

```python
#시각화
plt.figure(figsize=(6, 6))
plt.plot(chi2_graph, y, label=r'$\chi^2-distribution$')
plt.axvline(x=chi2, color='red', linestyle = ':')
plt.axvline(x=chi2_2, color='blue', linestyle = ':')
plt.text(chi2, .4, '  result1($\chi^2$)  \n=  ' + str(round(chi2, 4)) + '  ', horizontalalignment='right', color='red')
plt.text(chi2_2, .6, '  result2($\chi^2$)\n  =' + str(round(chi2_2, 4)), horizontalalignment='left', color='blue')
plt.xlabel(r'$\chi^2$')
plt.ylabel('y')
plt.grid()
plt.title('$\chi^2$ (df=1)')
plt.legend()
plt.show()
```

​	도출해낸 카이제곱분포도를 matplotlib를 사용하여 그리고 그 위에 카이제곱검정 결과를 표시하고자 하였다. figure를 사용해 크기를 지정해주고 plot으로 분포도를 그려주었다. axvline을 사용해 카이제곱검정 결과 2개를 점선으로 표시하였고, text를 활용해 값을 출력했다. xlabel과 ylabel로 x축과 y축의 이름을 명시해주었고 마지막으로 title을 넣어주었다.

<br>

### 3. 향후 계획

​	기존에 사용하던 Python이라 그런지 R을 사용할 때보다 훨씬 간편하게 검정을 시행할 수 있었다. 이러한 통계학적 기법 뿐만 아니라 나의 최종적인 목표라고 할 수 있는 머신러닝, 딥러닝에 활용되는 많은 기법들을 사용하기 위해서는 matplotlib, pandas를 비롯한 여러 라이브러리들을 공부하고 연습해야할 것 같다.
