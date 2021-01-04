---
layout: post
title: "Cross Entropy"
date: 2021-01-04
excerpt: "cost function으로 자주 쓰이는 cross entropy에 대하여"
tags: [ML Basic]
category: ML Basic
comments: False
use_math: true
---

Cross Entropy는 머신러닝에서 cost function으로 너무나도 자주 쓰이는 개념이다. 그러나 대다수는 이에 대한 정확한 이해없이 쓰고 있다고 생각을 한다.
약간의 통게적 지식만 있으면 쉽게 그 이론적 배경을 이해할 수 있기에 한 번 배울때 제대로 배우는 것이 중요하다고 생각한다.

Cross Entropy에서 Entropy는 열역학의 엔트로피 공식과 상당히 유사하여 붙은 이름이며, 엔트로피를 이해하는 것은 정보이론으로부터 출발한다. 
따라서 정보이론과 shannon Entropy, Kullback-Libller Divergence 그리고 Cross Entropy 순서로 알아보자.

## 정보이론
정보이론은 시그널(특정한 사건)에 존재하는 정보의 양을 측정하는 것으로, Cross Entropy를 이해하는 것에 기초적인 역할을 한다.
머신러닝에서는 해당 확률분포의 특성 또는, 확률분포간 유사성을 정량화 한 것이다.

이러한 정보이론의 핵심 아이디어는 다음과 같다.
* 잘 발생하지 않는 시그널일수록 많은 정보를 담고있다.
* 독립적인 사건은 추가 정보량을 가진다.

이를 바탕으로 정보이론(Information Theory)의 창시자 Shannon의 Shannon Entropy에 대해서 배워보자.

## Shannon Entropy
섀넌은 위 정보이론의 아이디어를 수식화하였는데, 확률변수 x의 확률값이 $P(x)$인 사건의 정보량을 $I(x) = -log{P(x)}$라고 정의하였다($P(x)$와 $I(x)$는 반비례).

가령 동전던지기의 경우 $I(x) = -log_{2}{0.5} = 1$이고, 주사위 굴리기의 경우 $I(x) = -log_{2}{1/6} = 2.5849$이다. 즉 확률이 낮은 주사위는 더 많은 정보를 준다.

> 로그의 밑이 2인 경우: shannon or 비트 라고 한다.

> 로그의 밑이 e인 경우: nat이라고 한다. 머신러닝의 경우 주로 nat을 사용한다.

Shannon Entropy는 모든 사건에 대한 정보량($I(x)$)의 기댓값이며, 이는 전체 사건 확률분포의 불확실성에 대한 양을 나타낸다.
따라서, 어떠한 확률분포 P에 대한 섀넌 엔트로피 $H(P)$는 다음과 같이 정의된다.

$$H(P) = H(x) = E_{x\simP}(I(x)) = E_{x\simP}(-log(P(x)))$$

> 위 식은 x가 분포 p를 따를 때 그 I값들의 평균을 나타냈다고 보면 된다.

가령 동전을 한개 던질 때는 식이 다음과 같이 전개된다.
