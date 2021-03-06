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


# 데이터 전처리
## 장점
* 전체 데이터 세트에 대한 전처리: 정규화의 경우 전체 데이터에 대해서 수행해야 효과적임
* 단계확장: Apache Beam을 이용하여 GCP Dataflow등과 결합 가능
* Training Serving Skew 방지: Training과 Serving에서 동일한 전처리를 통해 편향을 제거

## TFT
* Process: Ingested raw data & Raw data schema -> preprocessing_fn() -> Transform graph, Transformed data

#### 다양한 기능
* 통합적 기능
- tft.scale_to_z_score(): 표준정규화
- tft.bucketize(): 데이터를 버킷으로 분할
- tft.pca(): 주성분 분석
- tft.compute_and_apply_vocabulary(): 특정 feature에 대한 unique값을 계산하고 가장 많은 값을 인덱스에 매핑 -> 이후 숫자로 변환

* 자연어에서의 기능
- tft.ngrams(): n-gram
- tft.bag_of_words(): bag of words
- tft.tfidf(): tf-idf

* 비전에서의 기능
~~~
def process_image(raw_image):
    raw_image = tf.reshape(raw_image, [-1])
    img_rgb = tf.io.decode_jpeg(raw_image, channels=3)
    img_gray = tf.image.rgb_to_grayscale(img_rgb) 
    img = tf.image.convert_image_dtype(img, tf.float32)
    resized_img = tf.image.resize_with_pad(
        img,
        target_height=300,
        target_width=300
    )
    img_grayscale = tf.image.rgb_to_grayscale(resized_img)
    return tf.reshape(img_grayscale, [-1, 300, 300, 1])
~~~

#### Usage
~~~
!pip install tensorflow-transform

def preprocessing_fn(inputs):
	x = inputs[‘x’]
	x_normalized = tft.scale_to_0_1(x)
	return {‘x_xf’: x_normalized}
~~~

#### 별도로 TFT만을 실행



## TFT on TFX
~~~
# 여기서 module.py는 preprocessing_fn()이 포함되어야 있어야 한다.
# https://github.com/Building-ML-Pipelines/building-machine-learning-pipelines/blob/master/components/module.py
transform = Transform(examples = example_gen.outputs[‘examples’],  schema = schema_gen.outputs[‘schema’], module_file = <module.py path>)

context.run(transform)
~~~

# 모델 훈련
## 별도의 훈련 파이썬 스크립트 ->  run_fn() 정의

1. TFT output
2. train, eval dataset에 Input 데이터를 넣기(이때, 함수를 이용하여 클린 코딩)
3. model 정의 (함수를 이용하여 컴파일까지 한 후 model을 반환)
4. log를 이용하여 Tensorboard 및 callback 정하기
5. model.fit
6. signatures 정의(8장 참조)
7. model.save()

## Usage
* module.py 내부에 run_fn()을 정의하기

~~~
from tfx.components import Trainer
from tfx.components.base import executor_spec
from tfx.components.trainer.executor import GenericExecutor
from tfx.proto import trainer_pb2

TRAINING_STEPS = 1000
EVALUATION_STEPS = 100

trainer = Trainer(
	module_file = <‘module.py’ path>, 
	custom_executor_spec = executor_spec.ExecutorClassSpec(GenericExecutor),
	transformed_examples = transform.outputs[‘transformed_examples’],
	transform_graph = transform.outputs[’transform_graph’],
	schema = schema_gen.outputs[‘schema’],
	train_args = trainer_pb2.TrainArgs(num_steps = TRAINING_STEPS),
	eval_args = trainer_pb2.EvalArgs(num_steps = EVALUATION_STEPS))
~~~

## Tensorboard on colab
~~~
import datetime

log_dir = ‘logs/my_board/‘ + datetime.datetime.now().strftime(‘%Y%m%d-%H%M%S’)

# tensorboard callback 설정 (log_dir 중요)
tensorboard_callback = tf.keras.callbacks.TensorBoard(log_dir = log_dir, histogram_freq = 1)

model.fit(
	<x_train>, 
	<y_train>, 
	validation_data = (<x_test>, <y_test>), 
	epochs = 100, 
	callbacks = [tensorboard_callback]) 
~~~

~~~
%load_ext tensorboard
%tensorboard —logdir {log_dir}
~~~

## Distribution training
* MirroredStrategy
* CentralStorage
* MultiWorkerMirrored
* TPU
* ParameterServerStrategy
* OneDeviceStrategy

~~~
# example
mirrored_strategy = tf.distribute.MirroredStrategy()
with mirrored_strategy.scope():
	model = get_model()
~~~
## TFX Tuner, Katib, kears tuner

# 모델 평가
## TFMA
#### 독립형 패키지로 단일 모델 분석
~~~
!pip install tensorflow-model-analysis
import tensorflow_model_analysis as tfma

# 저장된 모델과 평가 데이터 셋을 입력으로 사용
eval_shared_model = tfma.default_eval_shared_model(
	eval_saved_model_path = <model dir>,
	tags = [tf.saved_model.SERVING])

# metric 정하기
eval_config = tfma.EvalConfig(
	model_specs = [tfma.ModelSpec(label_key = <label_key>)],
	slicing_spec = [tfma.SlicingSpec()],
	metrics_specs = [
		tfma.MetricsSpec(metrics = [
			tfma.MetricConfig(class_name = ‘BinaryAccuracy’),
			tfma.MetricConfig(class_name = ‘ExampleCount’),
			tfma.MetricConfig(class_name = ‘FalsePositives’),
		])
	]
)

# 모델 분석 단계
eval_result = tfma.run_model_analysis(
	eval_shared_model = eval_shared_model,
	eval_config= = eval_config,
	data_location = <eval data file dir>,
	output_path = <output dir>,
	file_format = ‘tfrecords’	
)

# 시각화
tfma.view.render_slicing_metrics(eval_result)
~~~

#### 독립형 패키지로 여러 모델 분석
~~~
eval_shared_model_2 = tfma.default_eval_shared_model(
	eval_saved_model_path = <saved model dir>,
	tags = [tf.saved_model.SERVING]	
)

eval_result_2 = tfma.run_model_analysis(
	eval_shared_model = eval_shared_model_2,
	eval_config = eval_config,
	data_location = <eval data file dir>,
	output_path = <output_dir>,
	file_format = ‘tfrecords’
)

# 결과 로드
eval_results_from_disk = tfma.load_eval_results([
	<output dir1>, <output dir2>],
	tfma.constants.MODEL_CENTRIC_MODE
)

# 시각화
tfma.view.render_slicing_metrics(eval_result)
~~~

## WIT(What-If Tool)
1. eval data 준비
2. tf.saved_model.load로 모델 호출
3. predict_fn에 model.signatures를 불러오기
4. predict라는 함수를 설정
	4.1. predict_fn에 인자로 examples에 eval data를 넣어주기 -> 결과를 preds에 저장
	4.2. preds[‘outputs’].numpy()로 결과 반환
5. WIT 구성
6. 시각화

~~~
!pip install witwidget

eval_data = tf.data.TFRecordDataset(eval data file dir) # 
subset = eval_data.take(1000) # eval_data중 1000개를 샘플링
eval_examples = [tf.train.Example.FromString(d.numpy()) for d in subset] # 1000 개 예제를 만듦

model = tf.saved_model.load(export_dir = <model dir>)
predict_fn = model.signatures[‘serving_default’]

def predict(test_examples):
	test_examples = tf.constant([example.SerializeToString() for example in examples])
	preds = predict_fn(examples = test_examples)
	return preds[‘outputs’].numpy()

from witwidget.notebook.visualization import WitConfigBuilder

config_builder = WitConfigBuilder(eval_examples).set_custom_predict_fn(predict)
WitWidget(config_builder)
~~~

## Evaluator

1. ResolverNode 정하기
2. eval_config 정하기
3. Evaluator 실행
4. TFMA를 이용
~~~
from tfx.components import ResolverNode
from tfx.dsl.experimental import latest_blessed_model_resolver
from tfx.types import Channel
from tfx.types.standard_artifacts import Model
from tfx.types.standard_artifacts import ModelBlessing
import tensorflow_model_analysis as tfma
from tfx.components import Evaluator

model_resolver = ResolverNode(
	instance_name = ‘latest_blessed_model_resolver’,
	resolver_class = latest_blessed_model_resolver.LatestBlessedModelResolver,
	model = Channel(type = Model),
	model_blessing = Channel(type = ModelBlessing)
)

eval_config = tfma.EvalConfig(
	model_specs[tfma.ModelSpec(<label_key>)],
	slicing_specs = [tfma.SlicingSpec(), tfma.SlicingSpec(feature_keys = [‘product’])],
	metrics_specs = [tfma.MetricsSpec(metrics = [
			tfma.MetricConfig(class_name = ‘BinaryAccuracy’),
			tfma.MetricConfig(class_name = ‘ExampleCount’),
			tfma.MetricConfig(class_name = ‘AUC’),
		])
	]
)

evaluator = Evaluator(
	examples = example_gen.outputs[‘examples’],
	model = trainer.outputs[‘model’],
	baseline_model = model_resolver.output[‘model’],
	eval_config = eval_config
)


~~~

## TFX에서의 모델검사 자동화
~~~
eval_config = tfma.EvalConfig(
	model_specs[tfma.ModelSpec(<label_key>)],
	slicing_specs = [tfma.SlicingSpec(), tfma.SlicingSpec(feature_keys = [‘product’])],
	metrics_specs = [tfma.MetricsSpec(
		metrics = [
			tfma.MetricConfig(class_name = ‘BinaryAccuracy’),
			tfma.MetricConfig(class_name = ‘ExampleCount’),
			tfma.MetricConfig(class_name = ‘AUC’),
		],
		thresholds = {
			‘AUC’: tfma.config.MetricThreshold(
				value_threshold = tfma.GenericValueThreshold(lower_bound = {‘value’: 0.65}),
				change_threshold = tfma.GenericChangeThreshold(direction = tfma.MetricDirection.HIGHER_IS_BETTER, absolute = {‘value’: 0.01})
			)
		}
	)]
)
~~~

## Pusher
~~~
from tfx.components import Pusher
from tfx.proto import pusher_pb2

pusher = Pusher(
	model = trainer.outputs[‘model’],
	model_blessing = evaluator.outputs[‘blessing’],
	pusher_destination = pusher_pb2.PushDestination(filesystem = pusher_pb2.PusherDestination.Filesystem(base_directory = <serving model dir>))
)
~~~
