소개
## components 종류
* 데이터 수집: ExampleGen
* 데이터 유효성 검사: StatisticsGen, SchemaGen, ExampleValidator -> TFDV
* 데이터 전처리: Transform -> TFT
* 모델 훈련: Trainer 
* 이전에 훈련된 모델 확인: ResolverNode 
* 모델 분석 및 검증: Evaluator -> TFMA
* 모델 배포: Pusher -> TFserving

## component 개괄
* 과정 = metadata store(artifact) -> driver -> executor -> publisher -> metadata store(artifact)
- driver: metadata store의 쿼리를 처리함
- executor: 요소의 작업을 수행
- publisher: 출력 및 metatdata store로의 저장
- MLMD: artifact를 SQLite, MySQL등의 백엔드에 저장

## 대화형 파이프라인
* 장점: 아티팩트를 즉시 검토 가능
~~~
import tensorflow as tf
from tfx.orchestration.experimental.interactive.interactive_context import InteractiveContext

context = InteractiveContext()

#======
# 사용법
# from tfx.components import StatisticsGen

# statistics_gen = StatisticsGen()
# context.run(statistics_gen)

# =====
# 시각화
# context.show(statistics_gen.show[‘statistics’])
# for artifacts in statistics.gen.outputs[‘statistics’].get():
# 	print(artifact.uri)
~~~

## Apache Beam
Batch Process, Streaming, Data Pipeline을 Apache Spark, Google Cloud Dataflow에서 동일하게 수행 가능하다는 장점

* 추상화: 컬렉
