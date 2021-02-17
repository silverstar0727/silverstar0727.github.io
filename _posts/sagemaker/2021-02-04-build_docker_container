---
layout: post
title: "Build Docker Container"
date: 2021-02-04
excerpt: "SageMaker에서 custom model의 docker container를 빌드하자!"
tags: [SageMaker]
comments: False
category: SageMaker
use_math: true
---

1. AWS SageMaker에서 notebook 생성
2. Jupyter Lab에서 Dockerfile과 train.py 생성
3. build docker container
4. Amazon ECR에 푸시 
5. 푸시한 container 실행

#### AWS SageMaker에서 notebook 생성
우선 AWS SageMaker에 접속하여 노트북 인스턴스를 생성한다. 이때 주의해야할 점은 생성한 IAM role에서 AmazonEC2ContainerRegistryFullAccess 해당 정책을 추가해주어야 한다. 추가는 다음의 [링크](https://console.aws.amazon.com/iam/home)에서 하면 된다.

#### Jupyter Lab에서 Dockerfile과 train.py 생성
Jupyter Lab을 열고 docker_file_test폴더를 만든 뒤, 여기에 해당 [링크](https://github.com/silverstar0727/SageMaker-build-dockercontainer)의 파일들을 넣어주자.

#### build docker container
다시 Jupyter Lab에서 나와서 ipynb파일을 하나 만든 뒤 아래의 명령어로 디렉토리의 위치를 옮겨준다.

~~~
cd docker_test_test
~~~

제대로 이동했는지 확인하자.
~~~
!pwd
~~~

아래의 코드로 테스트를 진행하자. 이때, role arugument는 본인의 iam role로 설정하면 된다.
~~~
from sagemaker.estimator import Estimator

estimator = Estimator(image_uri='tf-custom-container-test',
                      role='arn:aws:iam::111122223333:role/role-name',
                      instance_count=1,
                      instance_type='local')

estimator.fit()
~~~

#### Amazon ECR에 푸시
아래의 쉘스크립트 코드를 이용하여 빌드한 컨테이너를 Amazon ECR에 푸시해주자.
~~~
%%sh

# Specify an algorithm name
algorithm_name=tf-custom-container-test

account=$(aws sts get-caller-identity --query Account --output text)

# Get the region defined in the current configuration (default to us-west-2 if none defined)
region=$(aws configure get region)
region=${region:-us-west-2}

fullname="${account}.dkr.ecr.${region}.amazonaws.com/${algorithm_name}:latest"

# If the repository doesn't exist in ECR, create it.

aws ecr describe-repositories --repository-names "${algorithm_name}" > /dev/null 2>&1
if [ $? -ne 0 ]
then
aws ecr create-repository --repository-name "${algorithm_name}" > /dev/null
fi

# Get the login command from ECR and execute it directly

$(aws ecr get-login --region ${region} --no-include-email)

# Build the docker image locally with the image name and then push it to ECR
# with the full name.

docker build -t ${algorithm_name} .
docker tag ${algorithm_name} ${fullname}

docker push ${fullname}

~~~

#### 푸시한 container 실행
아래 명령어를 통해 푸시한 이미지를 가져오자.
~~~
import boto3

account_id = boto3.client('sts').get_caller_identity().get('Account')
ecr_repository = 'sagemaker-byoc-test'
tag = ':latest'

region = boto3.session.Session().region_name

uri_suffix = 'amazonaws.com'
if region in ['cn-north-1', 'cn-northwest-1']:
    uri_suffix = 'amazonaws.com.cn'

byoc_image_uri = '{}.dkr.ecr.{}.{}/{}'.format(account_id, region, uri_suffix, ecr_repository + tag)

byoc_image_uri
# This should return something like
# 111122223333.dkr.ecr.us-east-2.amazonaws.com/sagemaker-byoc-test:latest
~~~

이미지를 다시 python SDK v2를 이용하여 테스트를 진행한다.
~~~
import sagemaker
from sagemaker import get_execution_role
from sagemaker.estimator import Estimator

estimator = Estimator(image_uri=byoc_image_uri,
                      role=get_execution_role(),
                      base_job_name='tf-custom-container-test-job',
                      instance_count=1,
                      instance_type='ml.m5.xlarge')

# start training
estimator.fit()

# deploy the trained model
predictor = estimator.deploy(1, instance_type)
~~~

## Reference
* [aws document](https://docs.aws.amazon.com/ko_kr/sagemaker/latest/dg/adapt-training-container.html)
