# 시험
# 논문 정리
- 4차 산엽혁명의 정의 : ICT와 모든산업의 융합 혁명 <br/>특징 : 4차 산엽혁명의 가장 큰 특징은 '기기의 지능화'라고 할 수 있다. 이제 기기들은 스스로 판단하고 결정할 수 있게 된다. <br/>기술: ICBM

|구분|1차 산업형명|2차 상업혁명|3차 산업혁명|4차 산업혁명|
|---|---|---|---|---|
|시기|18세기 후반|19 ~ 20세기 초|20세기 후반| 2016년~ |
|핵심 기술|열 에너지를 이용한 증기 기관|전기 에너지 컨베이어 벨트|인터넷 기반의 지식정보|사물인터넷,클라우드,빅데이터,모바일 기반의 자동화|
|<span style = "color:#ff8088">핵심 가치</span>|생산의 자동화|대량 생산|정보 혁명|복지 향상|
|주요 특징|인력의 기계 대체화|대량 생산 체계 구축|합리적 의사 결정과 과학 기술의 급발전|기기 자동화로 인류 편의성과 효율성 향상|
|핵심 전략|-|과학적 관리론|마케팅, 기술경영|오픈형 플랫폼,I서비스형 모델|

__- 중요(그림 그리기)__
<img src = "http://www.waterjournal.co.kr/news/photo/201802/40024_23142_232.jpg" height="500px"/>
## <span style = "color:#ff8088">ICBM</span>
1. I(사물 인터넷)
   - 사물 인터넷은 '센서'기술로 정의할 수 있다. 사물인터넷은 네트워크 센서를통해 사물의 현황 정보를 수집 한 뒤 이를'중앙센터'에 전송한다.
2. C(클라우드)
   - 클라우드는 중앙에 수집된 정보를 분석하며, 이를 기반으로 사용자들에게 원격 서비스를 제공하는 기술로 정의할 수 있다. 예를 들어 스마트홈 에너지를 보면 각 가정의 에너지 사용 정보가 클라우드의 데이터베이스(정보를 저장하는장치)에 모이고 이에대한 빅데이터 분석이 이뤄지면 클라우드는 각 가정의 스마트홈 에너지 서비스에 유의미한 각종 정보를 전송한다.
3. B(빅데이터)
   - 빅데이터는 정보를 분석하는 기술의 일종이다. 기존 분석 기술과는 '속도''다양성''규모'3가지 측면에서 차별화 된다. 빅데이터는 분석 속도가 매우 빠르게 떄문에 대량의 데이터들을 실시간으로 분석해서 정보를 제공할 수 있으며 정형화된 데이터들뿐만 아니라 비정형화된 데이터도 분석해서 의미있는 정보를 찾아낸다. 스마트홈 에너지 서비스의 경우 가정에서 최소 5분단위로 저장된 데이터들이 '중앙센터'로 전송, 매일 수백 기가바이트 규모의 데이터가 축적된다. 빅데이터는 이러한 대규모 데이터를 바르게 분석하고 의미있는 정보를 추출해낸다.
4. M(모바일)
   - 모바일은 최종 사용자에게 서비스를 전달하는 수단이다. 클라우드가 제공하는 의미있는 정보들이 모바일을 통해 전달된다. 스마트폰,드론,자율주행자동차 등 사용자에게 서비스를 최종적으로 제공하는 기기 모두를 모방일로 정의할 수 있다.
- 정리하면 사물 인터넷에서 정보를 수집해 이를 클라우드 데이터베이스로 전송한다. 이렇게 클라우드에 쌓인 정보들은 빅데이터에 의해서 분석돼 모바일로 전송된다. 모바일에서는 사용자 인터페이스를 통해 사용자에게 정보들을 제공한다

# 파이썬
- 빅데이터 분석과 딥러닝을 위하 프로그래밍 언어로 파이썬이 주목받고 있다.
- 파이썬은 십게 배울 수 있고 오픈소스로 제공되어 무료로 사용할 수 있따. 파이썬은 문법 자체가 간단하며 필요한 작업은 라이브러리(패키지)를 확장해서 할 수 있다.
1. 변수
   - 변수는 값을 저장하는 메모리 공간이다. 파이썬에서는 변수를 미리 선언하지 않으며 변수에 저장해서 사용하는 값의 자료형으로 변수의 자료형이 결정된다.
2. 객체
   - 객체는 변수 형태의 속성과 함수 형태의 메서드를 가진것으로 각 객체는 자기의 속성(내부데이터)과 메서드(내부연산)를 가진다.
3. 문자열 자료형
   - 문자열 데이터는 작은따옴표(`) 큰따옴표(") 작은따옴표3개(''') 큰 따옴표 3개(""")를 사용하여 나타낸다
   - |종류|파이썬 코드|
     |---|---|
     |문자열|>>> s1 = 'Hello Python'
     ||>>> s1
     ||'Hello Python'
     ||>>> s1 = "Hello Python"
     ||>>> s1
     ||'Hello Python'
     ||>>> s1 = '''Hello Python'''
     ||>>> s1
     ||'Hello Python'
     ||>>> s1 = """Hello Python"""
     ||>>> s1
     ||'Hello Python'
     |문자열 연결(+)|>>> head = "python"
     ||>>> tail = "is fun"
     ||>>> head + tail
     ||'python is fun'
     |대문자 변환(upper)|a.upper()
     ||'PYTHON'
     |소문자 변환(lower())|a.lower()
     ||'python'
     |문자열 바꾸기(replace())| a = "python is difficult"
     || a.replace("difficult","funny")
     || python is funny
     |집합|
     |교집합 연산(&,<span style = "color:#ff8088">intersection())| S2 & S3
     ||S2.<span style = "color:#ff8088">intersection()
     ||{4,5,6}
     |합집합 연산(\|,<span style = "color:#ff8088">union())|S2\|S3
     ||S2.<span style = "color:#ff8088">union(s3)
     ||{1,2,3,4,5,6,7,8,9}
     |차집합 연산(-,<span style = "color:#ff8088">difference())| S2-S3
     ||S2.<span style = "color:#ff8088">difference(S3)
     || {1,2,3}

4 그룹 자료형 특징(<span style = "color:#ff8088">차이점 구분!</span>)
   - 인덱스 사용|그룹자료형|원소값 변경
      ---|---|---
      가능|문자열|X
      ||리스트|O
      ||튜플|X
      불가능|딕셔너리|O
      ||집합|O

5. 모듈과 패키지
   - 모듈은 함수나 변수 또는 객체를 정의하는 클래스를 모아놓는 파일이고 패키지는 모듈을 모아놓는 디렉토리이다.
6. 파일 처리
   - 종류|형식|파이썬 코드
       ---|---|---
       파일쓰기|파일모드 'W' wite()| f = open("D:/새파일.txt",'w')<br/>for i in range(1,6):</br>data = "%d번째 줄입니다.\n"%i</br>f.write(data)</br>f.close()</br>입력 : "1번째줄입니다.</br>2번쨰 줄입니다. ........"
       |파일 읽기| 파일모드 Y </br> readline()|f = open("D:/새파일.txt",'r')<br/>line = f.readline()</br>while True:</br>line = f.readline()</br>if not line : break</br>"1번째줄입니다.</br>2번쨰 줄입니다. ........"</br>f.close()
7. numpy
   - numpy는 수치 데이터를 다루기 위한 라이브러리로 다차원 배열 자료구조인 ndarray를 지원하며 선형대수계산 등의 행렬 연산에 주로 사용된다.
8. pandas
   - pandas는 데이터 분석에서 자주 사용하는 테이블 형태를 다룰 수 있는 라이브러리다.
9. matplotlib
   - matplotlib은 라인플롯 차트, 바 차트, 파이 차트, 히스토그램, 산점도 등의 다양한 차트 그리기를 지원하는 라이브러리 이다.