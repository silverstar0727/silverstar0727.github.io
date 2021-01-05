---
layout: post
title: "Activation Function(미완)"
date: 2021-01-06
excerpt: "ANN에서 제시되는 다양한 Activation Function의 장점과 단점"
tags: [ML Basic]
category: ML Basic
comments: False
use_math: true
---

## 역할
Artificial Neural Network는 각 노드들의 무수히 많은 연결로 이루어져 있으며, 노드의 output을 다음 layer의 노드에 전달할 때, 얼마만큼의 신호로 전달할 것인가를 결정하는 방식이다.
사실 이러한 설명은 너무 뜬구름 잡는 얘기처럼 들릴 수 있으니, 가장 기초로 쓰이는 activation function인 sigmoid function을 예로 들어보자.

우리는 앞서 로지스틱 회귀를 배웠는데, 여기서 쓰인 로지스틱함수는 시그모이드 함수라고도 불리며 다음의 식을 갖는다고 배웠다. $\phi(z) = {1 \over 1 + e^{-(z)}}$ 
그리고 이러한 시그모이드 함수는 여러개의 input을 받아 한개의 0~1사이의 값을 output으로 반환하는 함수라는 것도 배웠다.

즉, 시그모이드 함수를 activation function으로 사용한다는 것은 각 노드들의 input으로는 수많은 값들이 들어오지만, output으로는 0~1 사이의 값만을 반환한다는 것이다.

그렇다면 이러한 반환값은 무엇을 의미하냐면, 해당 노드의 input의 신호를 수치적으로 정량화 하여 다음 노드로 전달했다는 것을 의미한다.
다시 설명하면, activation function를 통과한 노드의 output이 커질수록 input의 영향이 유의미하다는 것을 의미하며, 작을수록 영향이 없어진다는 것을 뜻한다.

정말 마지막으로 설명하자면(솔직히 직관적으로 이해하는게 편하다) activation function은 말 그대로 현재 노드의 활성(영향력)의 정도를 수치화하는 함수이다.

## Sigmoid
