---
layout: post
title: "딥러닝 옵티마이저"
---



딥러닝에 이용되는 Optimizer 는 대부분 **Adam** 을 쓰고 있다.

**딥러닝** 은 입력층과 출력층 사이에 여러 개의 은닉층으로 이루어진 신경망이다.

층이 깊어질수록 모듈과 함수에 따른 하이퍼파라미터(hyper-parameter) 도 비례하여 많아지기에 이 하이퍼파라미터를 결정하여 모델이 정확하게 결과를 뱉어낼 수 있도록 하는 것이 학습의 핵심이다.
그러기 위해 Loss Function을 정의하여야하고, 그리고 **Loss Function**을 **최적화**해야한다.



### Optimization

딥러닝 학습시 최대한 틀리지 않는 방향으로 학습해야 한다,

얼마나 **틀리는지(loss)**를 알게 하는 함수가 **loss function(손실함수)**이다.
loss function 의 최솟값을 찾는 것을 학습 목표로 한다.

최소값을 찾아가는 것 **최적화 = Optimization**

이를 수행하는 알고리즘이 **최적화 알고리즘 = Optimizer** 이다.



학습속도를 빠르고 안정적이게하는 것을 목표로 한다.

아래 이미지는 여러 옵티마이저들이 어떻게 오차의 최저점을 찾아가는지 그래프로 형상화한 것이다.

![img](https://velog.velcdn.com/images/freesky/post/762c7c9b-6276-4343-aa9d-9fda9684cb3f/image.gif)

![img](https://velog.velcdn.com/images/freesky/post/607dea70-976d-4dfb-b875-cd75ea348aa8/image.gif)

### Optimizer 종류

![img](https://velog.velcdn.com/images/freesky/post/57e14895-6eb0-4c86-a9d1-0acdb0398406/image.png)

#### Adam (Adaptive Moment Esimation)

Momentum 와 RMSProp 두가지를 섞어 쓴 알고리즘이다.

즉, 진행하던 속도에 **관성**을 주고, 최근 경로의 곡면의 변화량에 따른 **적응적 학습률**을 갖은 알고리즘이다.

매우 넓은 범위의 아키덱처를 가진 서로 다른 신경망에서 잘 작동한다는 것이 증명되어,
일반적 알고리즘에 현재 가장 많이 사용되고 있다.

아담의 강점은 bounded step size 이다.

Momentum 방식과 유사하게 지금까지 계산해온 기울기의 지수 평균을 저장하며,

RMSProp과 유사하게 기울기의 제곱값에 지수평균을 저장한다.

![img](https://velog.velcdn.com/images/freesky/post/e03cedc8-da99-4dbf-a683-02b5b6693c5b/image.png)

Adam 에서는 기울기 값과 기울기의 제곱값의 지수이동편균을 활용하여 step 변화량을 조절한다.

또한, 초기 몇번의 update 에서 0으로 편향되어 출발 지점에서 멀리 떨어진 곳으로 이동하는, 초기 경로의 편향 문제가 있는 RMSProp의 단점을 보정하는 매커니즘이 반영되어있다.
( m 과 v가 처음에 0으로 초기화되어있기 떄문에, 학습의 초반부에서는 mt, vt, mtxVt 가 0에 가깝게 bias 되있을 것이라고 판단하여, 이를 unbiased 하게 만들어주는 작업을 거친다.)

보정을 통해 unbiased 된 expectation을 얻을 수 있다.
![img](https://velog.velcdn.com/images/freesky/post/d08d7d82-4f1f-4f18-9099-fa0e91ce03f6/image.png)

![img](https://velog.velcdn.com/images/freesky/post/1eb8e252-4cbb-488a-9cf3-3f5d0ae243bd/image.png)

hyper-parameter는 각각 아래 값들이 추천된다.
![img](https://velog.velcdn.com/images/freesky/post/c63fdc9c-857e-49a1-844a-8e86300267a3/image.png)

보통 β1, β2, ϵ 값은 고정 시켜두고, 에타를 여러 값으로 시도해가면서 가장 잘 작동되는 최적의 값을 찾는다.



source : https://velog.io/@freesky/Optimizer