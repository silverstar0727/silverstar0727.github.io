---
layout: post
title: "[PR]Adaptively Sparse Transformer(2)"
date: 2020-11-10
excerpt: "Adaptively Sparse Transformer 논문 리뷰. (실험, 결과)"
category: Paper Review
tags: [PR]
comments: False
use_math: true
---

## 실험
#### 두 가지 변형 모델
* 1.5-entmax: 모든 헤드에 softmax대신 $\alpha = 1.5$인 entmax를 갖도록한다(transformer에선 최초로 적용).
* $\alpha-entmax$: 각각의 헤드에 $\alpha_{i,j}^{t}$인 adaptively sparse attention을 적용한다(헤드 당 추가 스칼라 파라미터가 생김).
#### 데이터 셋(BPE preprocessing)
* IWSLT2017(German to English): 200K sentence paris
* KFTT(Japanese to English): 300K sentence pairs
* WMT2016(Romanian to English): 600K sentence pairs
* WMT2014(English to German): 4.5M sentence pairs

## Result
* entmax로 softmax를 대체하는 것이 성능향상에 도움이 됨(BLEU)
* 1.5-entmax가 $\alpha-entmax$에 비해 좋은성능을 기록하기도 함
![image](https://user-images.githubusercontent.com/49096513/98567279-15055380-22f3-11eb-8f4a-3dca0a0ca8a7.png)

* layer마다, head마다 $\alpha$가 training step에 따라변하는 것을 확인할 수 있음
* 특정 head의 경우에는 초기에 감소하다 급격히 증가하여 2에 수렴하게 됨(그만큼 sparsity한 문맥적 특성을 지님)
![image](https://user-images.githubusercontent.com/49096513/98568100-108d6a80-22f4-11eb-9d70-8eeaa852cfa2.png)

* 아래 그림에서는 softmax나 1.5-entmax에 비해서 $\alpha-entmax(\alpha = 1.91)$일때 가장 높은 집중도를 나타낸 것을 볼 수 있다. 
![image](https://user-images.githubusercontent.com/49096513/98613040-d8f4e180-2338-11eb-9ec0-f17972e43395.png)

* BPE-merging head에서 $\alpha = 1.91$로 정할 시 하이픈으로 연결된 단어 간 집중도가 밀집된 것을 확인할 수 있다.
![image](https://user-images.githubusercontent.com/49096513/98613186-21ac9a80-2339-11eb-811e-569cfbe29103.png)

* 그러나 아래 그림과 같이 의문문의 경우에는 $\alpha-entmax$보다 softmax가 물음표에 더 많은 attention을 할당한 것을 확인할 수 있다.
![image](https://user-images.githubusercontent.com/49096513/98613293-57ea1a00-2339-11eb-94e8-9c3d081bf599.png)

* 한편 동일한 $\alpha$의 long sequence에서는 짧은 것 보다 더 높은 신뢰도로 sparsity하다는 것을 볼 수 있다.
![image](https://user-images.githubusercontent.com/49096513/98613627-ffffe300-2339-11eb-82a5-625a5efad501.png)

## REFERENCE
* [Adaptively Sparse Transformer](https://arxiv.org/abs/1909.00015)
* [official repository](https://github.com/deep-spin/entmax)
