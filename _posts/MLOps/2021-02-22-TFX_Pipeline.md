---
layout: post
title: "TFX pipeline에 대한 이해"
date: 2021-02-22
excerpt: "나만 보려고 쓰는 TFX 간단 요약"
tags: [MLOps]
comments: False
use_math: true
---


## TFX pipeline에 대한 이해

#### ML worflow
* data 준비, 분석 변환
* 모델 교육 & 평가
* 프로덕션에 배포
* Artifact 추적 & 종속성 파악

#### Artifact
* 파이프라인 단계의 출력 (가령, ExampleGen의 출력(아티팩트) -> StatisticsGen의 입력으로 사용됨)

#### Parameter
* 파라미터를 통해 파이프라인을 변경 가능
* 파이프라인의 코드 변경 없이 다른 hyperparameter set 실행 가능

#### Component
* 파이프라인을 구성하는 요소들(ExampleGen, StatisticsGen, SchemaGen, ExampleValidator, Transform, Trainer, Tuner, Evaluator, infraValidator, Pusher, BulInferrer)
* ExampleGen: 입력데이터 수집, 분할
* StatisticsGen: 데이터 세트 통계적 분석
* SchemaGen: 통계 검사 및 데이터 스키마 생성
* ExampleValidator: 이상치 및 결측치 탐색
* Transform: feature extraction
* Trainer: 모델 학습
* Tuner: HPO
* Evaluator: 학습 결과 분석 및 모델 검증
* InfraValidator: 모델이 인프라에 제공 가능한 상태인지 확인
* Pusher: deploy to infra
* BulkInferrer: 라벨이 지정되지 않은 추론 요청에 대한 일괄 처리


#### Pipeline
* Component를 비순환적 모형으로 구성

![image](https://user-images.githubusercontent.com/49096513/108686967-77e4c880-7539-11eb-977a-7dd4251cbec3.png)

## Reference
* https://www.tensorflow.org/tfx/guide/understanding_tfx_pipelines
