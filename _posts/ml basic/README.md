## 순서에 따라 정리

* [머신러닝 개요](https://silverstar0727.github.io/ml%20basic/2021/01/03/%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%EA%B0%9C%EC%9A%94/)
* [선형회귀 모델(1)](https://silverstar0727.github.io/ml%20basic/2021/01/03/%EC%84%A0%ED%98%95%ED%9A%8C%EA%B7%80%EB%AA%A8%EB%8D%B8(1)/)
* [선형회귀 모델(2)](https://silverstar0727.github.io/ml%20basic/2021/01/04/%EC%84%A0%ED%98%95%ED%9A%8C%EA%B7%80%EB%AA%A8%EB%8D%B8(2)/)
* [로지스틱 회귀](https://silverstar0727.github.io/ml%20basic/2021/01/05/%EB%A1%9C%EC%A7%80%EC%8A%A4%ED%8B%B1%ED%9A%8C%EA%B7%80/#)
  * [Cross Entorpy](https://silverstar0727.github.io/ml%20basic/2021/01/04/cross_entropy/)
* 인공신경망
  * Activation function
  * Optimizer
  * 모델 평가 metric

ML Basic post는 수식이 많아서 LaTex로 번역된 블로그에 가서 보아야 합니다. 

딥러닝으로 확장하기 위한 필수적인 개념만을 다루기 때문에 하나의 내용을 모른채로 건너뛸 경우 이후 이해에 어려움이 있습니다.

## 전반적인 정리
* 목표: 주어진 input data와 output data와의 관계를 설명하는 "함수(모델)"를 설정하고, 이 함수로 새로운 input에 대한 prediction을 수행하는 것
* 과정
  * 함수(모델)를 가정
  * input data를 함수(모델)에 넣고 나온 predict 값과 output data와의 차이를 cost function으로 
  * 해당 cost function을 최소화 하는 방향으로 학습을 진행(optimization)
* 모델 평가: 다양한 metic으로 모델평가를 진행

> 모델로는 linear regression, logistic regression, ANN 등 다양한 모델이 존재. 대부분의 논문들은 이러한 모델을 제시(too many..)

> cost function으로는 regression에서는 MSE, classification에서는 cross entorpy를 주로 사용하나 다양한 종류가 존재하니, 많이 찾아보고 사용하면 됨.

> metric으로는 MSE, f1-score, AUC, Accuracy등 다양한 metric이 존재하므로 데이터에 잘 맞는 평가지표를 사용하면 됨.
