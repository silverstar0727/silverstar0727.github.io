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

#### ML worflow에 아주너무 좋음
* data 준비, 분석 변환
* 모델 교육 & 평가
* 프로덕션에 배포
* Artifact 추적 & 종속성 파악

#### Artifact
* 파이프라인 단계의 출력 (가령, ExampleGen의 출력(아티팩트) -> StatisticsGen의 입력으로 사용됨)

#### Parameter(솔직히 뭔말인지 아직 잘 모르겠음)
* 파라미터를 통해 파이프라인을 변경 가능
* 파이프라인의 코드 변경 없이 다른 hyperparameter set 실행 가능

#### Component(블럭쌓기에 해당하는 블럭이라고 생각하면 편함)
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
* Component를 비순환적 모형(DAG)으로 아래처럼 구성

![image](https://user-images.githubusercontent.com/49096513/108688974-dca12280-753b-11eb-9399-039e755e406e.png)

## TFX pipeline 빌드

#### 개요
* 간단한 사용 코드
~~~
pipeline.Pipeline(
	pipeline_name = ~~~, # metadata의 쿼리시 사용할 이름
	pipline_root = ~~~, # 파이프라인 출력의 루트 경로 (GCS 같은거, 대신 오케스트레이터가 권한이 있어야..) 
	components = ~~~, # component 목록
	enable_cache = ~~~, # (선택, bool) 실행속도 높이기 위한 캐싱 사용여부 -> 이전 실행에서 동일한 입력세트는 건너뛸 수 있음
	meta_connection_config = ~~~, # (선택) metadata 연결 구성
	beam_pipeline_args = ~~~, # (선택) Beam을 사용하는 모든 구성요소에 대해서 apache beam에 전달
)
~~~

#### 템플릿을 사용한 파이프라인 빌드

아래 코드를 실행.

이때 사용된 템플릿은 데이터, 전처리 코드, keras를 이용한 모델, pipeline 구현 및 구성 스크립트를 가져와서 kubeflow 혹은 apache beam 파이프라인용 DAG 실행기 코드와 metadata 저장소에 복사
~~~
# 템플릿 보기(근데 지금은 taxi밖에 안됨)
tfx template list

# 템플릿 선택 후 복사
tfx template copy —model=taxi —pipline_name=test —destination_path={project_dir}
~~~

apache beam을 사용할 경우 아래 코드를 이용하여 파이프라인에 추가
~~~
python beam_dag_runner.py
~~~

> 추후에 kubeflow를 위한 pipeline 예제를 시도해 볼 예정

#### 사용자 정의 파이프라인 빌드(일단 skip)




## Reference
* [TFX-docs](https://www.tensorflow.org/tfx/guide/)
