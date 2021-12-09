---
layout: post
title: "Chi-square_test Practice(1)"
tags: [Statistics, R]
categories : [Practice]
comments: true
---

### 1. 목적

​	Chi-squared test는 두 명목형 변수의 결합이 유의미한지에 대해 검정하는 방법으로 1953년 Spaulding에 의해 처음 고고학에 도입된 통계검정기법이다. 변수가 많아지면 컴퓨터의 도움을 받아야하지만 변수가 적을 경우 손으로 충분히 계산이 가능하다.

​	먼저 변수로 교차분할표를 만든다. 이후 관찰치를 토대로 기대치를 산출해야한다. 기대치는 (Marginal total * Marginal total)/table total 이다. 이를 통해 관찰치와 기대치 간의 차이를 검증하는 것이다. 여기서 Chi-square는 x^2 = sum(관찰치 - 기대치)^2/기대치 공식을 사용하여 산출할 수 있다.

​	Chi-square는 별도의 확률밀도함수를 갖는데 아래 첨부해놓았으니 참고하길 바란다. 이전의 T-test와 마찬가지로 귀무가설과 대립가설을 세운 뒤 특정 자유도 하에서 특정 유의수준에 따른 값보다 크다면 귀무가설을 기각할 수 있고 기각할 수 있다면 두 변수의 결합은 유의미하다는 결과가 된다.

​	Chi-square를 사용할 때에는 주의할 점이 따른다. 첫 번째, 관찰치의 값이 5 이하인 원소가 20% 이상일 경우 활용을 지양한다. 두 번째 관찰치에 0이 들어갈 경우 활용을 지양한다. 세 번째 Sample의 수가 늘어나면 귀무가설이 기각되기 쉬워지므로 이에 유의해야한다. 세 번째에 경우 통계학적으로는 유의미할지몰라도 실제로 의미를 갖는 것은 아니기 때문에 더욱더 유의해야한다.

<img src="https://github.com/ChanToRe/ChanToRe.github.io/blob/master/images/2020-10-20/Chi_square-test_table.jpeg?raw=true" width="100%" height="100%"/>

### 2. 코드

```R
test = read.csv("/Users/jch/Desktop/Chi_squared_Test.csv") #분석할 데이터를 불러옴

test$RorL = as.factor(test$RorL) #factor형으로 변경
test$Sex = as.factor(test$Sex) #factor형으로 변경

x = test$RorL #리코딩
y = test$Sex #리코딩

table(x, y) #교차분할표 생성

chisq.test(x, y)

#####

test = read.csv("/Users/jch/Desktop/Test.csv") #분석할 데이터를 불러옴

table(test) #교차분할표 생성

#교차분할표 정보
F_LHS = 33
F_RHS = 14
M_LHS = 11
M_RHS = 29

#Table total
T_T = F_LHS + F_RHS + M_LHS + M_RHS

#Marginal total
F_T = F_LHS + F_RHS
M_T = M_LHS + M_RHS
LHS_T = F_LHS + M_LHS
RHS_T = F_RHS + M_RHS

#Expected value
F_LHS_EV = (F_T * LHS_T)/T_T
F_RHS_EV = (F_T * RHS_T)/T_T
M_LHS_EV = (M_T * LHS_T)/T_T
M_RHS_EV = (M_T * RHS_T)/T_T

#chi_square
xx = ((F_LHS-F_LHS_EV)^2/F_LHS_EV)+((F_RHS-F_RHS_EV)^2/F_RHS_EV)+((M_LHS-M_LHS_EV)^2/M_LHS_EV)+((M_RHS-M_RHS_EV)^2/M_RHS_EV)
xx
```

​	전체코드이며 R로 작성했다. 주석(#####)을 기준으로 상단은 R을 사용하여 쉽게 Chi-square test를 사용한 것이고 아래는 그러한 과정을 전체적으로 직접 수행한 것이다. 실제로 사용할 때는 위의 코드를 활용하면 간편할 것이다.

<br>

```R
test = read.csv("/Users/jch/Desktop/Chi_squared_Test.csv") #분석할 데이터를 불러옴

test$RorL = as.factor(test$RorL) #factor형으로 변경
test$Sex = as.factor(test$Sex) #factor형으로 변경

x = test$RorL #리코딩
y = test$Sex #리코딩

table(x, y) #교차분할표 생성

chisq.test(x, y)
```

​	제일 먼저 데이터셋을 불러와야한다. 별도의 데이터 셋이 제공되지 않아서 성별에 따른 청동기 부장 방향에 대한 예제 데이터셋을 직접 만들어서 사용했다. 

​	먼저 불러온 데이터는 M, R, LHS, RHS와 같은 chr형이기 때문에 이를 factor로 바꾸어주었다. 이후 x, y로 리코딩해주었다. 눈으로 보기 편하도록 교차분할표를 생성해준 뒤, chisq. 함수를 이용해 chi-squre test를 사용했다. 그 결과 14.109라는 x^2 값이 산출되었다. 이를 앞선 Chi-square 확률밀도함수에 대입해보면 대략 P-value = 0.0001725 가 나오며 유의수준을 어지간히 낮게 해주지않으면 귀무가설은 거의 기각되므로 두 변수의 결합은 통계학적으로 유의미하다고 할 수 있다. 

<br>

```R
test = read.csv("/Users/jch/Desktop/Test.csv") #분석할 데이터를 불러옴

table(test) #교차분할표 생성

#교차분할표 정보
F_LHS = 33
F_RHS = 14
M_LHS = 11
M_RHS = 29

#Table total
T_T = F_LHS + F_RHS + M_LHS + M_RHS

#Marginal total
F_T = F_LHS + F_RHS
M_T = M_LHS + M_RHS
LHS_T = F_LHS + M_LHS
RHS_T = F_RHS + M_RHS

#Expected value
F_LHS_EV = (F_T * LHS_T)/T_T
F_RHS_EV = (F_T * RHS_T)/T_T
M_LHS_EV = (M_T * LHS_T)/T_T
M_RHS_EV = (M_T * RHS_T)/T_T

#chi_square
xx = ((F_LHS-F_LHS_EV)^2/F_LHS_EV)+((F_RHS-F_RHS_EV)^2/F_RHS_EV)+((M_LHS-M_LHS_EV)^2/M_LHS_EV)+((M_RHS-M_RHS_EV)^2/M_RHS_EV)
xx
```

​	이어서 Chi-square 검정을 수기로 계산하는 과정으로 살펴보겠다.

<br>

```R
test = read.csv("/Users/jch/Desktop/Test.csv") #분석할 데이터를 불러옴

table(test) #교차분할표 생성

#교차분할표 정보
F_LHS = 33
F_RHS = 14
M_LHS = 11
M_RHS = 29
```

​	먼저 데이터셋을 불러오고 시각적으로 보기 편하게 교차분할표를 작성했다. 교차분할표에 따라 F_LHS, F_RHS, M_LHS, M_RHS 네 개의 값을 얻었다.

<br>

```R
#Table total
T_T = F_LHS + F_RHS + M_LHS + M_RHS

#Marginal total
F_T = F_LHS + F_RHS
M_T = M_LHS + M_RHS
LHS_T = F_LHS + M_LHS
RHS_T = F_RHS + M_RHS
```

​	Chi-square 검정을 시행하기 전에 기대치(Expected value)를 산출해야하기 때문에 이에 필요한 Table total과 Marginal total을 산출했다.

<br>

```R
#Expected value
F_LHS_EV = (F_T * LHS_T)/T_T
F_RHS_EV = (F_T * RHS_T)/T_T
M_LHS_EV = (M_T * LHS_T)/T_T
M_RHS_EV = (M_T * RHS_T)/T_T
```

​	앞서 구해놓은 두 값과 관찰치를 활용하여 기대치를 산출했다.

<br>

```R
#chi_square
xx = ((F_LHS-F_LHS_EV)^2/F_LHS_EV)+((F_RHS-F_RHS_EV)^2/F_RHS_EV)+((M_LHS-M_LHS_EV)^2/M_LHS_EV)+((M_RHS-M_RHS_EV)^2/M_RHS_EV)
xx
```

​	앞선 과정들을 통해 구해놓은 관찰치, 기대치를 활용하여 Chi-square 검정을 진행했다. 결과는 앞서 살펴본 방법과 동일하다.

<br>

### 3. 향후 계획

​	R공부를 많이 하지 않고 통계학적 방법들을 구현하려니 제한이 많았다. 이어서 계속 혼자 공부해보고자 하는 마음이 들어서 로버트 드레넌의 고고학을 위한 통계학을 구매했다. R도 수학도 통계학적 기법도 고고학도 더 열심히 해야할 것 같다.