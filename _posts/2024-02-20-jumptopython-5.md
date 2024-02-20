---
layout: post
title: "점프 투 파이썬 5장 연습문제"
---



> Q1. 다음은 Calculator 클래스이다.

 

```
class Calculator:
  def __init__(self):
    self.value = 0

  def add(self, val):
    self.value += val
```

 

위 클래스를 상속하는 UpgradeCalculator를 만들고 값을 뺄 수 있는 minus 메서드를 추가해보자.

즉 다음과 같이 동작하는 클래스를 만들어야 한다.

 

```
cal = UpgradeCalculator()
cal.add(10)
cal.munus(7)

print(cal.value) #10에서 7을 뺀  3을 출력
```



> Q2. 객체변수 value가 100이상의 값은 가질 수 없도록 제한하는 MaxLimitCalculator 클래스를 만들어보자 

 

```
cal = MaxLimitCalculator()
cal.add(50)
cal.add(60)

print(cal.value)
```

 

단, 반드시 다음과 같은 Calculator클래스를 상속해서 만들어야한다.

 

```
class Calculator:
  def __init__(self):
    self.value = 0

  def add(self,val):
    self.value += val
```



> Q3. 다음 결과를 예측해보자

 

 

```
#1번

all([1,2,abs(-3)-3])

#2번

chr(ord('a'))=='a'
```



Q4. filter와 lambda를 사용해서 리스트 [1,-2,3,-5,8,-3]에서 음수를 제거해보자.



Q5. 234라는 10진수의 16진수는 다음과 같이 구할 수 있다.
이번에는 반대로 16진수의 문자열 0xea를 10진수로 변경해보자.

\>>> hex(234) '0xea'



Q6. map과 lambda를 사용해서 [1,2,3,4] 리스트의 각 요솟값에 3이 곱해진 리스트 [3,6,9,12]를 만들어보자.



> Q7. 다음 리스트의 최댓값가 최솟값의 합을 구해보자.

 

> [-8, 2, 7, 5, -3, 5, 0, 1]



Q8. 17/3의 결과는 다음과 같다. 소숫점 4자리까지만 반올림해서 표시해보자.



> Q9. 다음과 같이 실행할 때 입력값을 모두 더해서 출력하는 스크립트(C:\doit\myargv.py)를 작성해보자.

 

```
>>> C:\cd doit
>>> C:\doit> python myargv.py 1 2 3 4 5 6 7 8 9 10
55
```



> Q10. os모듈을 사용해서 다음과 같이 동작하도록 코드를 작성해라.

 

> 1. C:\doit 디렉터리로 이동한다.
> 2. dir 명령을 실행하고 그 결과를 변수에 담는다.
> 3. dir 명령의 결과를 출력한다.



Q11. glob모듈을 사용해서 C:\doit 디렉터리의 파일 중 확장자가 .py인 파일만 출력하는 프로그램을 만들어보자.



Q12. time모듈을 사용해서 현재 날짜와 시간을 다음과 같은 형식으로 출력해보자.



Q13. random 모듈을 이용해서 로또번호(1~45사이의 숫자 6개)를 생성해보자. (중복 숫자 안 됨)



source : 점프 투 파이썬
