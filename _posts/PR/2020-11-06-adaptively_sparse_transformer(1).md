---
layout: post
title: "[PR]Adaptively Sparse Transformer(1)"
date: 2020-11-06
excerpt: "Adaptively Sparse Transformer 논문 리뷰. (필요성, 제안, alpha 정하기)"
category: Paper Review
tags: [PR]
comments: False
use_math: true
---

## Sparse Attention의 필요성
최근 NLP에서 주로 transformer를 활용한 모델이 SOTA를 기록하는데, 여기서 Multi Head Attention의 Scaled Dot Product는 $softmax({QK^{T} \over \sqrt{d}})V$를 사용하고 있다.
그러나 softmax function은 exponential fucntion에 대입한 값들의 결과물이기 때문에 0이 나올 수 없다. 즉, 확률이 적은 단어에 확실한 0의 attention score가 주어지지 못한다는 것이다.

GPT-2의 사례를 보면 Top-k 방식을 통한 autoregressive inference를 진행하는데, 낮은 확률을 가진 단어들이 우연에 의해서라도 선택된다면 long sequence에서는 상당한 영향을 미칠 수 있다. 
또한, 낮은 확률값들을 0으로 만들어 더이상 계산의 필요성을 줄인다면, 크기가 증가하는 최신 모델들에서 보다 적은 계산량을 가질 수 있기에 Sparse attention이 제시되었다([Generating Long Sequences with Sparse Transformers](https://arxiv.org/abs/1904.10509v1), Rewon Child et al.).

## Adaptively Sparse Attention의 제시
Rewon Child가 제시한 Sparse Attention은 softmax를 개선했음은 분명하다. 하지만, 아직도 적은 확률들에게 0을 부여하지 못하며, 특정 상황에 따라서는 적은 확률들이 많이 등장할 필요가 있기 때문에 Heads가 보다 유연하고 상황에 맞도록 달라지는 문맥에 의존적인 적응형(Adaptively) sparse attention을 제시한다. 

<그림 1>에서 sparse attention은 모든 heads에서 'The'와 'quick'에 0의 확률을 할당한 것을 불 수 있다. adative span transformer는 이를 어느정도 개선했지만, 본 논문에서 제시하는 adatively sparse attention이 헤드 별로 유연하게 적용한 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/49096513/98428781-77d5cf80-20e6-11eb-85b9-267388961845.png)
<center><그림 1></center>
    
    
즉, attention score가 미미한 단어에게 확실하게 zero-weight를 받을 수 있도록 하는 activation function을 도입하는 것이다. 
하지만, 필요에 따라 spread-out이 요구될 때에는 softmax가 선택될 수 있도록 $\alpha$-entmax를 채택하여 $\alpha$값은 자동적으로 선택이 가능하도록 하였다. 

<그림 2>에서는 $\alpha$ 가 1~4일 때의 $\alpha$-entmax를 그래프로 확인할 수 있다. $\alpha$의 값이 커질수록 보다 sparsity해지는 경향이 있으며, 경사도가 급해진다는 것을 알 수 있다. 이러한 $\alpha$-entmax이 heads에 붙는데 기존의 $Att(Q, K, V) = \pi({QK^{T} \over \sqrt{d}})V$에서 $\pi$를 $\alpha$-entmax$(z_{i})_ j$로 치환한다.

![image](https://user-images.githubusercontent.com/49096513/98296693-9e1c4200-1ff6-11eb-9dd1-c86c1e096744.png)
<center><그림 2></center>

## $\alpha$ 정하기
LSTM기반의 seq2seq모델에서는 grid search로 간단히 $\alpha$를 조정할 수 있는 반면, 여러 레이어에 걸쳐있는 transformer의 경우에는 gradient method를 이용해야 한다. 따라서 entmax의 output인 $w,r,t,\alpha$에 대한 Jacobian을 구해야 하며, 이는 최적화 문제의 argmin differentiation에 해당한다. 본 논문에서는 다음과 같은 형태로 지속적으로 형태가 변할 수 있는 신경망을 최초로 제시하였다.

#### Proposition
$\alpha$-entmax의 식 $argmax_{p \in \triangle^{d}} (p^{T}z + H _ {\alpha}^{T} (p))$이고, 다음 세가지를 정하자.
* $\hat{p} = \alpha$-entmax$(z)$
* $\tilde{p_i} = {\hat{p_i} \over \Sigma_j (\hat{p_i})^{2-\alpha}}$
* $h_i = -\hat{p_i} \log{\hat{p_i}}$

i번째 Jacobian은 $g = {\partial \alpha -entmax(z) \over {\partial \alpha}}$는 아래와같다.

![image](https://user-images.githubusercontent.com/49096513/98512409-9cc67000-22a9-11eb-9e7d-6b3480713d59.png)

> p의 위첨자에 star를 넣는 것이 LaTeX문법에 존재하지 않아 마지막 수식을 제외하고 $\hat{p}$로 대체하였다. 위와 같은 방식으로 Jacocbian을 계산하여 $\alpha$를 자동적으로 정하는 방식을 사용하였다

## REFERENCE
[Adaptively Sparse Transformer](https://arxiv.org/abs/1909.00015)
