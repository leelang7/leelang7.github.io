## ***\*로지스틱 회귀(Logistic Regression)\****

로지스틱 회귀(Logistic Regression)는
분류 문제를 위한 회귀 알고리즘으로,
***\*0에서 1사이의 값\****만 내보낼 수 있도록 출력값의 범위를 수정한 분류 알고리즘입니다.

![image](https://cdn-api.elice.io/api-attachment/attachment/9e8839a8c9464264b6f0ae6fc24ac5c0/image.png)

이번 시간에는 사이킷런 안에 구현되어 있는 로지스틱 회귀 호출을 통해 실제로 S자형 곡선 그래프가 출력되는지 시각화를 통해 확인해 보겠습니다.

------

***\*로지스틱 회귀를 위한 사이킷런 함수/라이브러리\****

- `from sklearn.linear_model import LogisticRegression` : 사이킷런 안에 구현되어 있는 로지스틱 회귀 기능을 불러옵니다.

- `model=LogisticRegression()` : 로지스틱 회귀 모델 `logistic_model`을 정의합니다.

- `model.fit(X, y)`: (X, y) 데이터셋에 대해서 `logistic_model`모델을 학습시킵니다.

- `model.predict(X)`: X 데이터에 대해 `logistic_model`이 예측한 값을 반환합니다.

  ## ***\*실습\****

  1. 로지스틱 회귀 모델을 구현하고, 학습 결과를 확인할 수 있는 `main()` 함수를 완성합니다.
  2. - 함수를 이용하여 데이터를 불러옵니다.
     - 로지스틱 회귀 모델을 정의합니다.
     - 학습용 데이터로 로지스틱 회귀 모델을 학습시킵니다.
     - 테스트용 데이터로 예측한 분류 결과를 확인합니다.
  3. 실행 버튼을 눌러 실제 학습된 로지스틱 회귀 모델이 S자 곡선을 그리는지 확인합니다.

  [실행 결과]

  ![image_output (1).png](https://cdn-api.elice.io/api-attachment/attachment/d3897f25ae0749efb9f428b66fcc017e/image_output%20%281%29.png)

  ------

  ## Tips!

  - 지시사항에 따라 None값을 채웁니다.
  - None값이 아닌 주어진 값을 변경하면 오류가 발생할 수 있습니다.