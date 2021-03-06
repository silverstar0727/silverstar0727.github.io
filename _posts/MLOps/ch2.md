# 소개
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

## Apache Beam(미완)
Batch Process, Streaming, Data Pipeline을 Apache Spark, Google Cloud Dataflow에서 동일하게 수행 가능하다는 장점

* 추상화: 컬렉


# 데이터 수집
* 목적: 데이터 세트를 하위 집합으로의 분류, 하나의 포괄적인 데이터로 결합, 수집하는 전략
* 개괄: 데이터 파일, bigquery등 외부에서 읽기 -> tf.Example의 데이터 구조를 가진 TFRecord 파일로 데이터를 변환(장점: 시스템 독립적, 빠른 속도, TFX에서 광범위하게 사용)
* component: ExampleGen

## 로컬 데이터 수집
~~~
from tfx.utils.dsl_utils import external_input

examples = external_input(<data_path>)

# =====
# image, corpus etc.
from tfx.components import ImportExampleGen

example_gen = ImportExampleGen(input = example)

# ===== 
# 쉼표로 구분된 데이터(CSV) - S3, GCS도 가능, 인증 필요
# from tfx.components import CsvExampleGen

# example_gen = CsvExampleGen(input = examples)
~~~

## 외부 데이터 수집
~~~
# BigQuery
from tfx.components import BigQueryExampleGen

query = “””
	SELECT * FROM ‘<project_id>.<database>.<table_name>’
“””
example_gen = BigQueryExampleGen(query = query)
~~~

## 데이터 준비
~~~
from tfx.components import CsvExampleGen
from tfx.proto import example_gen_pb2
from tfx.utils.dsl_utils import external_input

# =====
# 데이터 세트 분할(6:2:2 = train: eval: test)
output = example_genpb2.Ouput(
	split_config = example_gen_pb2.SplitConfig(splits = [
		example_gen_pb2.SplitConfig.Split(name = ‘train’, hash_bucket = 6),
		example_gen_pb2.SplitConfig.Split(name = ‘eval’, hash_bucket = 2),
		example_gen_pb2.SplitConfig.Split(name = ‘test’, hash_bucket = 2)
]))

examples = external_input(<data_path>)
example_gen = CsvExampleGen(input = examples, output_config = output)


# =====
# 새로운 데이터를 기존 데이터에 추가하기(span)
# input = example_gen_pb2.Input(splits = [example_gen_pb2.Input.Split(pattern = ‘export-{SPAN}/*’)])

# examples = external_input(<data_path>)
# example_gen = CsvExampleGen(input = examples, input_config = input)
~~~

#### 참고
* DVC(Data Version Control) 사용
* vision에는 tf.Example을 사용, 자연어에는 TFRecord 또는, Apache Parquet 사용



# 데이터 검증
## TFDV

## GCP로 대규모 전처리

## TFDV 통합
~~~
from tfx.components import StatisticsGen
from tfx.components import SchemaGen
from tfx.components import ExampleValidator

statistics_gen = StatisticsGen(examples = example_gen.outputs[‘examples])
schema_gen = SchemaGen(statistics_gen.outputs[‘statistics’], infer_feature_shape = True)
example_validator = ExampleValidator(statistics = statistics_gen.outputs[‘statistics’], schema = schema_gen.outputs[‘schema’])
~~~
