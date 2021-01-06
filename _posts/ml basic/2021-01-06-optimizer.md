---
layout: post
title: "Optimizer"
date: 2021-01-06
excerpt: "학습의 방법을 결정하는 '최적화'문제: optimizer"
tags: [ML Basic]
category: ML Basic
comments: False
use_math: true
---

## Optimization Theory & 공학에서의 다양한 수치해석
물리학을 전공하고 있기에 다양한 수학적 이론들을 접할 기회가 다른 학과보단 많은 편이라고 생각한다. 사실 물리학에서는 최적화, 수치해석적 이론에는 큰 관심이 없다.
자연현상의 자명해를 구하는 것을 목적으로 하며, 그렇기에 역학중에서도 유체역학의 나비에 스톡스 방정식과 같은 난제나 N-Body Problem과 같은 해가 없는 문제 혹은 Many Body Quantum Mechanics는 모두 공학 혹은 양자화학의 분야로 치부된다.

그러나 최근 컴퓨터 자원이 무어에 법칙에 따라 지속적으로 꾸준히 성장하면서, 보다 수치적 근사가 잘 이루어지고 있기도 하다.

대개는 time series에 대한 역학적 변화를 공학에서 초점을 맞추고 있다.(다체계 양자문제는 예외) 따라서, 공학에서는 다양한 수치해석적 방법을 배우는데 대표적으로는 룽게-쿠타 법이 존재한다.

보다 학문적으로 접근하고 싶다면, 수치해석학, 공업수학 등의 강의를 들으면 된다.

## AI에서의 optimizer 개요
한편 AI에서의 수치적 이론은 최적화에 포커싱을 하고 있다. 그 이유는 앞선 포스터에서 지속적으로 다룬 cost의 최적화가 그 목적이기 때문이다.

참고로 다시 언급하자면 cost는 관측값과 예측값의 차이를 정량화 한 것이다. 예로는 classification에서 cross entorpy가 있었으며, Regression에서는 MSE가 존재했다.

따라서 이러한 cost를 최소로 하는 것이 목적이라고 할 수 있다.

> 사실 최적화는 최대화와 최소화 모두를 포괄하지만, cost function을 정의할 때, 통일 되도록 일부러 최소화가 목적이 되도록 하므로 optimizer는 최소화 탐색 기법만을 다룬다.

그렇다면 AI에서 최적화 관심사에 대해서 조금 더 깊이 들어가보자. 아래 그래프를 보면, cost - weight 그래프에서 cost의 값이 다양하게 변하는 함수의 형태임을 알 수 있다. 이것은 차원을 극히 축소한 형태로 실제로는 다양한 weight의 조합에 따라 cost가 결정되는 형태를 갖는다. 

![image](https://user-images.githubusercontent.com/49096513/103747569-38742280-5046-11eb-8cad-8fddbc90baff.png)

근데 여기서 의문이 들 수 있다. 고등학교 때 주구장창 배웠던 이론에 따르면, 최대 최소 문제에서는 미분해서 0인 값을 찾으면 되는 것 아닌가? 

하지만, AI에서는 non-linear한 다중 결합의 형태를 취하는 경우가 대부분이고, 수많은 parameter에 대해서 미분하여 0을 찾는 것은 생각보다 단순한 작업이 아닐 것이다. 비록 자명해가 나올 수 있을지라도, 공학이나 실생활에서는 이렇게 오래걸리는 것은 사업적 가치가 없기 때문이다.

따라서, 수치적 방법을 이용한 다양한 최적화 기법이 등장하게 되었다.

아래 그림에서는 이러한 발전 과정을 아주 단순하게 보여준다. 우리가 흔히 아는 Gradient Descent에서부터 시작하여 Adam에 이르기까지 몇 안되는 optimizer들이주로 사용되고 있으며, 이들 중 자주 쓰이는 몇 가지를 통해 이론적 배경을 알아보자.
> 사실 그냥 'cost를 최소화하는 일종의 방법론'으로 생각하면 편할 수 있으나, 그럼 재미가 없지 않는가... 따라서 본 포스터는 지적 유희를 추구하니 관심이 없는 분들은 개요만 읽고 넘어가면 된다.

![image](https://user-images.githubusercontent.com/49096513/103747657-52ae0080-5046-11eb-9118-b4bff860317d.png)

## SGD
SGD는 Stochastic Gradient Descent Optimizer로 미니배치로 나누어 epoch마다 학습을 진행하는 것을 말한다. 해당 방법의 장점은, weight 값을 여러차례 수정하면서 학습할 수 있다는 점과, 그래픽 카드의 메모리가 작아도 효과적으로 학습을 진행할 수 있다는 점이다. weight는 GD와 같은 $W_{t+1} = W_{t}+\alpha {\partial cost \over \partial W}$ 이다.

## Momemtum
Momemtum은 물리학의 관성과 같은 개념을 도입한 방법으로 직전 배치학습 결과를 모멘텀 상수($\beta$)를 곱한만큼 반영하게 된다. 따라서 식은 다음과 같다. $W_{t + 1} = W_{t} \beta - \alpha {\partial cost \over \partial W}$ 

## RMSProp
기울기를 누적시키는 AdaGrad에서 최신 기울기일수록 더욱 많이 반영하는 방식을 차용하였다. hyperparameter p를 통해서 작을 수록 더 크게 반영하는 방식으로 적용하였다. 이러한 방식은 local minimum과 최적해 근처에서 느린 학습속도를 개선하였다. 

## Adam
가장 많이 사용하는 optimizer로 RMSProp과 Momentum를 혼합한 방식으로 가중치의 초기화 시 0으로 편향되는 문제를 해결하였다.

[blog](https://ganghee-lee.tistory.com/24)
