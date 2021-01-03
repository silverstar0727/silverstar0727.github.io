---
layout: post
title: "[PR]Bert is not all you need for commonsense inference"
date: 2020-11-11
excerpt: "Bert is not all you need for commonsense inference 논문리뷰."
category: Paper Review
tags: [PR]
comments: False
use_math: true
---

## 요약
BERT의 unimodal이 야기하는 단점을 Knowledge Graph와의 합성 방식을 두 가지(joint, coordinated)로 제시하여 outperform하는 것을 보여준다.

## 배경지식 & BERT의 불완전성
본 논문에서 추론(inference)은 크게 자연어 추론(NLI, Natural Language Inference)과 인과추론(CI, Casual Inference)으로 나뉜다. 추론의 목적은 전제(p)에 대해 가설(h)가 참인지를 판단하는 것이며, h는 수반(entailment), 모순(contradiction) 그리고 중립(neutral)의 세 가지로 분류가 가능하다. 가령, 아래와 같이 가정하면,
* p: It was still night
* h: The sun is shining

상식(common knowledge)에 해당하는 p': the sun cannot shile in the night은 contradiction이 된다는 것이다.

본 논문은 이러한 잘못된 CI의 근거로 SOTA(state-of-the-art)로 소개되는 BERT가 unimodal(단일 데이터 종류로 표현되는 모델)이며, 선별되거나 선별되지 않은 자료 둘 중 하나로부터 p와 h를 표현한다고 한다. 즉, p' casual knowledge가 사전에 학습되지 않았음을 지적한다. 따라서 p'으로부터 KG(knowledge graph) triple인 (h,r,t) 중 h, t사이의 관계 r에 대해 파악하는 것을 목적으로 한다.

한편 KG를 embedding하는 방법으로 SE(structure embedding)과 DE(description embedding)이 존재 한다. BERT는 이완 대조적으로 CE(Contextual embedding)인데, 각각의 특성은 아래와 같다.
* SE: (h,t,r)의 관계가 유용할 경우 높은 점수를 갖도록 인코딩하는 것을 목적으로 함
* DE: 유사한 h,t에 유사한 description이 존재한다고 가정하고 인코딩하는 것을 목적으로 함
* CE: 문제의 맥락으로부터 downstream task에 대한 성능을 높이도록 인코딩하는 것을 목적으로 함


## 가설
논문은 아래의 두 가지를 가정한다.
* BERT의 지식이 누락된 정보를 가지고 있으며, 긍정적 공동 발생으로 치우져 있음.
    * 예를 들어 해와 밤의 부정적 연관성 대신 해와 밤 사이의 긍정적 연관성에 집중하고 있다는 것임.
    * 이러한 문제점을 KG와 BERT의 보완적 성격을 통해 선별된 자료와 선별되지 않은 자료 모두를 이용하여 NLI, CI 모두에서 높은 performance를 보이는 것을 통해 증명한다.
* KG와 BERT의 합성이 multimodality fusion problem으로 아래 두 가지가 존재하며 이것을 통해 commonsense knowledge에 대한 학습을 한다는 것이다.
    * 개별 단일 신호를 공유표현 공간에 결합하는 것(joint)
    * 개별 단일 신호를 개별적 처리하되, 쌍을 이루는(의미가 겹치는) 부분을 기준으로 서로 조정하는 것(coordinated)
    
![image](https://user-images.githubusercontent.com/49096513/98779324-0c1d9a80-2437-11eb-843a-0ccfabec2442.png)

가령, 강아지를 판단할 때 강아지의 사진과 '강아지' 글자 자체 두 가지를 보고 판단하는 것을 두고 multimodality라고 한다.


## 제안
본 논문에서 제안되는 모델은 두 가지가 존재한다. 앞서 두번째 가설의 multimodality fusion에 해당하는 것으로 join(OWE + BERT)와 coordinated(OWE ~ BERT)가 그것이다. 여기서 OWE는 SE와 DE의 공간 $\Psi^{map}$에 대해 학습하는 것으로, KG 임베딩에서 압도적인 성능을 보이고 있어 채택한 KG embedding 방식이다.
* Joint(OWE + BERT): joint의 경우에는 간단히 $v_{OWE}, v_{BERT}$ 두 가지를 합치는 것으로 $f(v_{OWE}, v_{BERT})$로 표현할 수 있다.
* Coordinated(OWE ~ BERT): 이 방법은 cross-modal 유사성을 통해 학습하는데, 이를 위해서는 KG triple과 대응되는 동일한 문맥의 표현이 필요하다. 그러한 문장은 RnnOIE model을 통해서 생성이 가능하다.
    * 예를 들어 "albert Enstein, a German theoretical physicist, published..."의 경우 RnnOIE를 이용해서 KG triple로 다음과 같이 대응된다. (Albert Einstein, publish, relatively theory) 
    * 이는 다시 $f(v_{OWE}, v_{BERT})$ 두 개의 벡터로 표현이 가능하다. 
    * 아래 세 가지는 해당 모델의 loss이다.
1. $L_{CM}$: $v_{OWE}, v_{BERT}$는 cross-modality loss($L_{CM}$)으로 조정된다($L_{CM} = MSE(v_{OWE}, v_{BERT})$).
2. $L_{M}$: masking에 의한 loss
3. $L_{AS}$: Attention supervistion에 의한 loss로 아래 수식을 참고

![image](https://user-images.githubusercontent.com/49096513/98781941-80a60880-243a-11eb-9f35-37cbea13e4dd.png)

## 실험
huggingface의 BERT, bert-based-uncased pretrained-weight 그리고 RnnOIE를 사용
#### 데이터 셋
* MNLI
    * 문장 + entailment, contradiction, neutral로 구분되는 데이터 쌍을 사용
* Stress test
    * 의도한 사양이 충족되는지 확인하는 것과 약점을 식별하는 것이 목적
    * 정상 작동용량 이상으로 실험을 진행
    * 자연어추론에서 현상별로 시스템을 평가하여 교란요인을 제거
* COPA
    * 100개의 Q&A 데이터로 절반은 development, 절반은 test로 쓰임
    * 각 질문은 전제 + 대안으로 구성
    * 전제보다 그럴듯한 대안을 선택하는 것이 목표

## 결과
* multimodal에서 BERT대비 outperformance를 모든 데이터 셋에 대해 보여줌
* joint방법은 p,h가 유사한 데이터에, 나머지는 coordinated의 방법이 우월하다는 것을 보여줌
* 세가지 손실함수를 모두 사용하는 것 대비 $L_{CM}$이 없을 때 성능이 현저히 떨어진다는 것을 알게 됨.
