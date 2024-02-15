---
layout: post
title: "점프 투 파이썬 4장 연습문제"
---



Q1. 주어진 자연수가 홀수인지 짝수인지 판별해 주는 함수(is_odd)를 작성해 보자.

```
def is_odd(number):
  if #your code here:
    return True
  else:
    return False
```

 

Q2. 입력으로 들어오는 모든 수의 평균 값을 계산해 주는 함수를 작성해보자. (단, 입력으로 들어오는 수의 개수는 정해져 있지 않다.)

```
def avg_numbers(#your code here):
  result = 0
  for i in args:
    result += i
  return #your code here

avg_numbers(1,2)
avg_numbers(1,2,3,4,5)
```



Q3. 다음은 두 개의 숫자를 입력받아 더하여 돌려주는 프로그램이다. 

```
input1 = input("첫 번째 숫자를 입력하세요:")
input2 = input("두 번째 숫자를 입력하세요:")

total = input1 + input2
print("두 수의 합은 %s입니다." % total)
```

위 식에 대한 출력값이다. 

> 첫 번째 숫자를 입력하세요: 3
> 두 번째 숫자를 입력하세요: 6
> 두 수의 합은 36입니다.

3과 6을 입력했을 때, 9가 아닌 36을 반환했다.

이 프로그램의 오류를 수정해보자.



Q4. 다음 중 출력 결과가 다른 것 한 개를 골라보자



① print("you" "need" "python")

② print("you"+"need"+"python")

③ print("you", "need", "python")

④ print("".join(["you", "need", "python"]))



Q5. 다음은 "text.txt"라는 파일에 "Life is too short" 문자열을 저장한 후 다시 그 파일을 읽어서 출력하는 프로그램이다.

```
f1 = open("test.txt", 'w')
f1.write("Life is too short")

f2 = open("test.txt", 'r')
print(f2.read())
```

이 프로그램은 우리가 예상한 "Life is too short"라는 문장을 출력하지 않는다.

우리가 예상한 값을 출력할 수 있도록 프로그램을 수정해보자.



Q6. 사용자의 입력을 파일(test.txt)에 저장하는 프로그램을 작성해보자.(단 프로그램을 다시 실행하더라도 기존에 작성한 내용을 유지하고 새로 입력한 내용을 추가해야한다.)

```
user_input = input("저장할 내용을 입력하세요 : ")
f = open('test.txt', #your code here)
f.write(user_input)
f.write(#your code here)
f.close()
```



Q7. 다음과 같은 내용을 지닌 파일 test.txt가 있다. 이 파일의 내용 중 'java'라는 문자열을 'python'으로 바꾸어서 저장해 보자.

Life is too short

you need java

```
f = open('test.txt', 'r')
body = #your code here
f.close()

body = #your code here

f = open('test.txt', #your code here)
f.write(body)
f.close()
```



source : 점프 투 파이썬
