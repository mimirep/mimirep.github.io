---
layout: post
title: "Median Polish Practice"
tags: [Statistics, R]
comments: true
use_math: true
---

### 1. 목적

​	이번에는 Median Polish를 연습해보고자 한다. Median Polish는 이전에 연습했던 통계기법들과는 다른 **탐색적 데이터 분석(EDA, Exploratory Data Analysis)**의 하나이다.

​	지금까지 살펴보았던 확증적 데이터 분석(CDA, Confirmatory Data Analysis)의 목적이 가설을 검증하는 것이라면, 탐색적 데이터 분석의 목적은 **유연하게 데이터를 탐색**하고 특징과 정보를 추출하여 패턴을 살펴보는 것이다. 탐색적 데이터 분석은 **John Tukey**가 제안한 분석방식으로 기존 통계학적 분석 방법으로는 많은 데이터의 핵심적인 의미를 파악하기 힘들기 때문에 이를 보완하고자 제시되었다.

​	**Median Polish**는 역시 **John Tukey**가 개발한 방법으로 교차분할표(CrossTab)의 값들을 중앙값(Median)으로 빼는 행위를 반복하여 자료의 패턴을 찾는 방법이다. 단순히 변수 사이의 관계여부만을 파악하는 것이 아니라, **강도를 파악**할 수 있기에, 명목형 변수 사이의 관계여부만 파악할 수 있는 카이제곱검정의 훌륭한 대안이 될 수 있다.

​	이번 연습예제는 '피장자 연령에 따른 묘제의 종류'이다. 피장자의 연령에 따라 묘제의 종류가 다른지 살펴보는 예제로 카이제곱검정을 사용해서 살펴볼 경우 두 변수 사이에 관계가 없는 것으로 결과가 도출된다. 하지만 고고학적 맥락을 통해 Raw Data를 보면 단순히 관계가 없다고 보기는 힘들다. 때문에 Median Polish를 통해 패턴을 찾아보고자 한다. 예제를 그대로 사용하면 저작권에 위반될 수 있기에 수치를 조정하여 사용했다.

<br>

### 2. 코드

```R
require(graphics)

df <-
    rbind(c(25, 21, 13),
          c(14, 19, 15),
          c(12, 18, 17))
rownames(df) = c("simple pit", "wooden chamber", "stone chamber")
colnames(df) = c("<21", "21-40", ">40")

med.d <- medpolish(df)

print(df)
print(med.d)

png("/Users/jch/Desktop/plot.png", width=500, height=500, res=100)
plot(med.d)

dev.off()
```

​	전체 코드는 R을 사용했다. Python의 Scipy, Pandas 등에서는 Median Polish를 찾을 수 없었다. 구글링해본 결과 개인이 제작한 라이브러리들이 있긴 했지만 가시성이 좋지않아서 Median Polish를 정식적으로 지원하는 R을 사용했다.

<br>

```R
require(graphics)

df <-
    rbind(c(25, 21, 13),
          c(14, 19, 15),
          c(12, 18, 17))
rownames(df) = c("simple pit", "wooden chamber", "stone chamber")
colnames(df) = c("<21", "21-40", ">40")
```

​	라이브러리는 graphics를 사용했다. 데이터 프레임(df)은 row를 묘제, col을 연령으로 구성하였다. 교차분할표만 봐서는 구체적인 양상을 파악하기 힘들지만, 대체로 낮은 연령대에 Simple pit(이하 토광묘)가 분포하고 높은 연령대에 Wooden chamber(이하 목관묘), Stone chamber(이하 석관묘)가 분포하는 것으로 보인다.

<br>

```R
med.d <- medpolish(df)
```

​	데이터 프레임을 구성한 뒤, Median Polish를 시행해주었고 결과를 med.d에 저장해주었다.

<br>

```R
print(df)
print(med.d)
```

​	데이터 프레임과 시행한 Median Polish결과를 출력해주었다. 

```
               <21 21-40 >40
simple pit      25    21  13
wooden chamber  14    19  15
stone chamber   12    18  17
```

​	데이터 프레임(df) 출력 결과다.

```
Median Polish Results (Dataset: "df")

Overall: 17

Row Effects:
    simple pit wooden chamber  stone chamber 
             3             -2              0 

Column Effects:
  <21 21-40   >40 
   -1     1     0 

Residuals:
               <21 21-40 >40
simple pit       6     0  -7
wooden chamber   0     3   0
stone chamber   -4     0   0
```

​	Median Polish 결과를 출력해주었다. Overall은 중앙값을 이용해 교차분할표의 요소들을 뺀 횟수이다.

​	중요한 것은 Residuals로 결과를 보면, 토광묘는 <21세에서 가장 많이 나타나고, >40에서는 적게 나타나는 것으로 보인다. 목관묘는 21-40에서 가장 많이 나타나고, 다른 연령에서는 비슷하게 나타난다. 석관묘는 <21에서 적게 나타나고 다른 연령에서는 비슷하게 확인된다. 결론적으로 비율을 살펴보면 <21은 토광묘와 연결되고, 목관묘는 21-40과 대응된다고 볼 수 있다.

<br>

```R
png("/Users/jch/Desktop/plot.png", width=500, height=500, res=100)
plot(med.d)

dev.off()
```

​	앞서 살펴본 Median Polish 결과를 표로 저장해주었다. '/Users/jch/Desktop/plot.png' 부분이 저장되는 주소다. 마지막으로 dev.off()를 통해 분석을 종료해주었다.

<img src="https://github.com/ChanToRe/ChanToRe.github.io/blob/master/images/2021-06-24/plot.png?raw=true" width="60%" height="60%"/>

​	표는 Residuals가 축약되어있다. 살펴보면 (0, 0)에 보이는 점들은 Residuals에서 0의 값을 가진 것들이고 다른 값들은 각각 도표 위에 정상적으로 배치되어있다.

<br>

### 3. 고찰

​	Median Polish를 비롯한 탐색적 데이터 분석은 정확한 수치화를 하기 어렵다는 단점이 있지만, 고고학적 맥락을 기반으로 자료의 양상을 파악해야하는 고고학의 특성상 정확한 수치화를 요구하는 작업은 그렇게 많지 않다. 때문에 탐색적 데이터 분석은 고고학에서 활용하기 굉장히 용이할 것이다.

​	탐색적 데이터 분석이 고고학에서 아무리 용이해도 기법들의 원리와 장단점을 알지 못한다면 사용할 수 없거나 필연적으로 잘못된 결과를 도출할 수밖에 없다. 때문에 기법들에 대한 정확한 이해를 위해 계속 탐구하는 자세가 갖추도록 노력해야할 것이다.

<br>

