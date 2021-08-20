---
layout: post
title: "Oxcal Practice"
tags: [Radiocarbondating, python, Statistics]
comments: true
use_math: true
---

<a href="https://c14.arch.ox.ac.uk/oxcal.html" style="Font-size:20pt">-Oxcal 바로가기-</a>

### 1. Oxcal이란?

​	Oxcal은 방사성탄소연대측정치를 분석하고 조정하는 프로그램이다. 주로 방사성탄소연대측정법을 활용해 도출한 방사성탄소연대를 수륜연대를 활용하여 보정할 때 사용된다. 영국의 옥스포드 대학교에서 제작 및 관리를 하고 있으며, 그들이 주기적으로 수륜의 방사성탄소연대를 측정하고 연결하여 보정곡선을 제작한다. 현재 가장 최신의 것은 2020년에 발표된 IntCal 20 이다. 발굴조사보고서에 기재되어 있는 탄소연대측정결과지를 보면 Oxcal을 사용해 연대를 보정하고 도표를 그린 것을 쉽게 확인할 수 있다.

​	서울대학교 고고미술사학과에서 교환학생으로 공부하면서 방사성탄소연대측정법과 Oxcal에 대해 배울 수 있었다. 방사성탄소연대측정법에 관해서는 쉽게 관련된 자료를 찾을 수 있었지만, Oxcal을 사용하는 방법에 대해서는 관련된 자료를 찾기 힘들었기에 본 글을 작성하게 되었다.

<br>

### 2. Oxcal을 사용해야하는 이유

​	전통적인 방사성탄소연대측정법은 기본적으로 우주선과 탄소의 비율이 항상 일정하다는 것을 전제로 한다. 하지만 산업혁명기 이후 오존층이 파괴됨에 따라 우주선의 유입량이 달라졌고, 이로인해 C^14의 양이 다르다는 것이 밝혀졌다. 이로인해 방사성탄소연대의 불확실성은 가중되었는데, 이를 해결하기 위해 다양한 방법이 시도되었다. 그중 현재 가장 보편적으로 사용되고, 거의 반드시 사용되는 보정방법이 바로 수륜연대보정이다. 

​	수륜연대보정을 하기 위해서는 수륜의 방사성탄소연대를 측정하여 보정곡선으로 만든 일종의 데이터베이스가 필요한다. 수륜은 매년 자라는데, 하나의 수륜은 전년도의 호흡량을 의미한다. 때문에 나이테별로 다른 방사성탄소연대를 가지게 되는데, 이를 이어붙여 만든 것이 수륜연대보정곡선이다. 수륜연대보정곡선을 제작하고 활용할 수 있도록 서비스하는 프로그램이 바로 Oxcal이다. 

<br>

### 3. Data 수집 및 전처리

​	Oxcal을 사용하기 위해서는 가장 먼저 방사성탄소연대측정치가 필요하다. 본 글에서는 <a href="http://rcdb.kr">RCDB</a>를 사용하여 경기도 연천군 남계리 유적의 방사성탄소연대측정치를 추출했다. 남계리 유적의 3호 주거지에서는 여러 탄화목이 출토되었는데, 3개의 범주로 구분된다(연천1, 연천2, 연천3). 본 글에서는 이 중 연천1에 해당하는 자료들을 사용할 것이다. 

```python
import pandas as pd
import matplotlib.pyplot as plt

#Data load
data = pd.read_csv('./rcdb.csv')

#Boxplot
plt.boxplot(data['Age_BP'])
plt.title('Namgyeri AMS')
plt.xticks([1], ['yeoncheon1'])
plt.ylabel('Age(BP)')

#save
plt.savefig('./YC-NG-boxplot.png')
```

​	가장 먼저 이상치를 파악하고 제거 여부를 결정하기 위해 Boxplot을 그려주었다. 데이터를 가져오기 위해 pandas를 사용했고, Boxplot을 그려주기 위해 matplotlib를 사용했다. 데이터를 불러온 뒤, Boxplot을 그려주었다. 이후 타이틀과 박스의 명칭을 붙여준 뒤, y축이 Age(BP)를 나타낸다는 것을 명시하기 위해 label을 붙였다.

<center><img src="https://github.com/ChanToRe/ChanToRe.github.io/blob/master/images/2021-08-13/YC-NG-boxplot.png?raw=true"></center>

​	결과는 위의 표와 같다. 표를 보면 이상치가 3개 검출된 것을 알 수 있는데, 남계리 유적을 발굴조사한 서울대학교 박물관에서는 이중 SNU14-R016, Beta-373370 자료의 측정치만을 이상치로 분류하고 이후 진행하는 결합에서 제거하였다. 아마도 Beta-373371의 측정치는 상대적으로 이탈된 수준이 낮기 때문에 이상치로 보지 않은 것 같다.

​	이렇게 이상치를 제거하고 선정한 측정치들은  방사성탄소연대측정치를 수륜연대보정곡선으로 보정하는 기능인 R-Date를 이용하여 보정한 뒤 사용할 것이다.

<br>

### 4. R-Combine

​	R-Combine은 Oxcal에서 가장 많이 사용되는 기능이다. 방사성탄소연대측정법은 샘플의 오염, 측정오류, 고목효과 등으로 인해 불확실성이 존재한다. 이러한 방사성탄소연대측정법의 불확실성을 제거하기 위해 여러 방법이 사용되는데, 가장 확실하고 보편적으로 사용되는 방법이 바로 복수측정이다. R-Combine은 2개 이상의 탄소연대측정치를 결합하여 연대와 표준편차의 평균을 산출하는 방법이다.

​	남계리 유적에서 출토된 탄화목들은 모두 서울대학교 기초과학공동기기원, Beta Analytic, The Prairie Research Institute at the University of Illinois, PaleoLabo, 14Chrono Centre at Queen's University Belfast에서 복수측정되었다. 이 측정치들을 가지고 Oxcal을 사용하여 R-Combine을 시행하고자 한다. 발굴조사를 시행한 서울대학교 박물관측의 결과자료가 있기에 해당 결과를 표적으로 시행할 것이다.

​	Oxcal은 GUI를 사용하는 방법과 Coding을 하는 방법이 있는데, 두 방법 모두 활용하여 R-Combine을 사용해보도록 하겠다.

#### 1) GUI

```
Oxcal Oline -> File -> New -> Tools -> Import -> 기능(R-combine) -> 데이터 입력(R-Date) -> Run
```

​	Oxcal은 다양한 분야의 연구자들이 활용하기 때문에 이들이 공통적으로 사용할 수 있도록 GUI 환경이 갖추어져 있다. 간단한 영어로 이루어져 있기 때문에 기능의 이름과 같은 고유명사만 알고 있다면, 영어를 못하는 사람도 쉽게 사용할 수 있다.

​	간단한 사용법은 코드펜스 내의 내용과 같다. node.js로 프로그래밍된 Oxcal을 다운받아 오프라인 환경에서 사용할 수도 있고, Oxcal Oline에 접속하여 인터넷상에서 사용할 수도 있다. 본 글에서는 후자의 방법을 사용했다. 먼저 접속하면 좌측에는 메뉴, 우측에는 샌드박스가 존재한다. 우측 File 탭에서 New를 선택하여 새로운 프로젝트를 생성해준다. 이후 Tools로 이동하여 Import 탭에서 원하는 기능을 선택해준다. 이후 양식에 따라서 데이터를 입력하고 실행하면 된다. 본 글에서는 앞서 설명한 R-Combine을 시행했다.

​	R-Combine을 먼저 Import한 뒤 내부에 합칠 데이터들을 삽입해주었다. 데이터들은 R-Date를 사용해서 보정한 뒤 사용했다.

#### 2) Code

```
Plot(){
	R_Combine("도당유적"){
		R_Date("도당유적 1-1호 주거지", 2420, 50);
		R_Date("도당유적 3-7호 주거지", 2620, 60);
		R_Date("도당유적 3-5호 주거지", 2440, 50);
		R_Date("도당유적 3-8호 주거지", 2480, 50);
		R_Date("도당유적 3-11호 주거지", 2460, 50);
		R_Date("도당유적 2-10호 주거지", 2440, 40);
	};
};

```

​	Oxcal은 앞서 살펴본 GUI외에도 간단하게 코드를 작성하여 사용할 수도 있다. 실제로 Oxcal에서 사용하는 문법은 굉장히 간단하기 때문에 필자는 Data가 많을 경우 GUI를 활용하고, 적을 경우는 코드를 작성한다. Oxcal을 수많은 기능이 있지만 대부분 방사성탄소연대측정치에 수륜연대보정을 시행하기 위해 사용한다. 때문에 대부분 도표를 그리기 위해 사용한다. 이 경우 굉장히 간단하게 사용할 수 있다.

```js
Plot(){
	
}
```

​	먼저 Plot()는 도표를 작성한다는 것이다. 새로운 Oxcal 창을 열었을 경우 자동적으로 Plot(){ }이 작성되어 있다. 이후의 코드는 Plot(){ }의 안에 작성하면된다.

```js
Plot(){
	R_Combine("Tittle"){
		
	}
}
```

​	그 다음에는 사용할 기능을 넣는다. 앞서 말했다시피, 본 글에서는 여러 연대측정치를 결합하는 R-Combine을 사용할 것이다. Tittle은 괄호 안에 R_Combine("Tittle") 형식으로 넣어준다. 이외에도 다른 기능을 사용하고 싶다면, R_Combine 자리에 해당 기능을 적어주면 된다.

```js
Plot(){
	R_Combine("Tittle"){
		R_Date("Name_1", BP, Error);
		R_Date("Name_2", BP, Error);
		...
		R_Date("Name_n", BP, Error);
	}
}
```

 	마지막으로 데이터를 넣어준다. 본 글에서는 R-Date를 사용했다. R-Date는 방사성탄소의 농도함수를 사용하여 보정연대에 분포할 확률을 계산하는 것이다. 데이터의 형식("샘플명", 연대측정치, 표준편차") 형식으로 넣어주면 된다.

 이후 마찬가지고 우측 상단의 화살표(Run) 버튼을 눌러주면 도표가 작성된다.

<br>

### 5. 결과 및 해석

<center><img src="https://github.com/ChanToRe/ChanToRe.github.io/blob/master/images/2021-08-13/Untitled.png?raw=true"></center>

​	앞선 방법을 통해 만들어진 것이 바로 위의 도표이다. 살펴보면, 측정치들을 R-Combine하여 결합한 결과가 1866(BP), 6(Error)이다.

​	파란색 그래프는 수륜연대보정곡선이며 하단의 회색이 해당 연대에 분포할 가능성을 나타낸다. 결합된 결과는 서울대학교 박물관에서 시행한 결과와 동일한 것을 알 수 있다. 이 경우 128-217calAD에 위치할 확률이 95.4%로 남계리 유적 3호 주거지가 대략 2세기 경의 것임을 알 수 있다. 본 글에서는 다루지 않았지만 연천2, 연천3 범주도 비슷한 결과값을 보여서 남계리 유적 3호 주거지의 연대가 약 2세기 경이라는 것에 신빙성을 더해준다.

<br>

### 6. 마무리

​	지금까지 Oxcal의 사용법을 아주 일부 살펴보았다. 본 글에서는 R-Combine과 R-Date를을 사용했지만, 이외에도 수많은 기능과 형식들이 존재한다. 여러 논문을 살펴보았을 때 가장 많이 사용된 기능은 R-Combine 이었다. 그 다음으로 많이 사용된 것은 여러 R-Date로 보정된 연대들을 Multi plot으로 표현한 것이었다. 본글에서는 다루지 않았지만, 연대의 단위도 바꿀 수 있으며, 세부적인 세팅을 사용자의 입맛대로 바꿀 수 있다. 더 관심이 있다면, <a href="https://c14.arch.ox.ac.uk/oxcalhelp/hlp_contents.html">Oxcal Manual</a>을 읽어볼 것을 추천한다.

​	Oxcal을 사용하는 것보다 더 중요한 것은 방사성탄소연대측정법과 수륜연대보정에 대한 이해이다. 도표만 그릴줄 알고 그려낸 도표를 해석할 수 있는 능력이 없다면, 이 좋은 프로그램을 사용하는 의미가 없을 것이다.