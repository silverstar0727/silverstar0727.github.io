---
layout: post
title: "koreALBERT"
date: 2021-01-31
excerpt: "삼성 SDS의 ICLR 2020에 억셉된 koreALBERT의 논문 리뷰"
category: Paper Review
tags: [PR]
comments: False
---

본 논문은 A Lite BERT(ALBERT)의 기초하에 작성되었다. 
이에 대한 논문은 기존에 리뷰를 한 적이 있으니, 다음의 [링크](https://silverstar0727.github.io/paper%20review/2020/12/14/ALBERT/#)에서 보고 해당 포스트를 보는 것이 이해가 빠를 것이라고 생각된다.

특히 해당 논문에서 사용된 KoreALBERT는 KorQuAD1.0에서 1위를 차지하기도 하는 등 우수한 성과를 거두고 있기에 더욱 흥미가 생겼다. 

## Challenge
ALBERT는 BERT모델의 사이즈와 특정 task에 대해서 개선을 하였다. 
사이즈 즉, parameter의 개수를 줄이기 위해서 Cross-layer Parameter Sharing의 방식을 사용하였고, NSP를 SOP로 전환을 하였다.

여기서 NSP는 BERT의 Next Sentence Prediction으로 다음 문장이 알맞게 오는 것인지를 확인하는 태스크이다.
한편, SOP는 Sentence order prediction으로 문장의 순서가 맞는지를 기준으로 학습을 진행하는 태스크이다.

그러나 이러한 개선에도 불구하고 ALBERT를 한국어에 적용하기에는 알맞지 않은 점이 존재했다. 

한국어는 접사(affixes)가 존재하는 방식의 언어이기에 접사의 종류에 따라서 문맥의 의미가 크게 달라지기 때문이다.
그렇기에 형태소간의 관계를 더욱 잘 표현할 수 있는 임베딩의 기법이 요구되었다.

## Contibution
KoreALBERT에서는 위에서 언급한 ALBERT의 한국어 적용 부적합을 새롭게 제시한 WOP(Word Order Predction)으로 해결하였다.

WOP는 형태소 단위로 tokenizing하여 그들의 앞뒤를 바꾸거나 바꾸지 않고, 바뀐 token에 대한 예측을 하는 것이 핵심 task이다.
따라서, 먼저 언급한 ALBERT의 MLM과 SOP를 고스란히 적용하고 여기세 WOP를 추가로 적용하여 총 3 가지의 task를 이용한 모델을 완성하였다.

전체 모델의 구조를 보면 아래와 같다는 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/49096513/106372675-9f6ec800-63b5-11eb-97e0-7abeb7af3383.png)

추가로 WOP는 uniformly하게 30~50% 정도를 섞을 때 가장 효과가 좋다고 밝혀졌다.
또한, MASK토큰의 MLM과 겹치지 않게 해야하고, dropout을 빼는 것과 더해서 하는 것을 모두 해보았지만, 더해서 하는 것이 효과가 좋았다고 한다.

> dropout을 빼는 것이 더 효과가 좋다는 논문이 있다고 언급하기도 하였는데, 그런게 있다니... 신기하다.

## Experiments
실험은 WOP를 성능을 알아보기 위해 위에서 언급한 WOP를 포함한 3가지 task를 2개씩 번갈아 가면서 진행하기도 하였다.

#### optimization
* MLM과 SOP의 최대 suffle rate를 15%로 설정하였다.
* LAMB optimizer를 사용하였다. (lr = 1.25 x e-3)
* 128의 sequence length를 사용하였다.
* 2048의 batch size를 사용하였다.
* dropout rate를 0.1로 하였다.
* GELU activation function을 사용하였다.
* SentencePiece tokenizer를 사용하였다.

#### dataset
* 한국어 뉴스 데이터를 정치, 사회, 경제, 문화, IT 등을 2007년부터 2019년까지 수집하였다.
* 한국어 위키피디아를 490,220개의 document를 크롤링하였다.
* 나무위키의 740,094개의 document를 크롤링 하였다.

## Result
결과는 대부분의 task에서 괄목할만 했다. NLI에서 SOP를 제거하는 것이 더 효과적이라는 사실을 놀랍다...

![image](https://user-images.githubusercontent.com/49096513/106372859-5c155900-63b7-11eb-97b6-53b7b523adc6.png)


## Reference
* [KoreALBERT](https://arxiv.org/abs/2101.11363)
