---
layout: post
title: "SageMaker 외부에서 엔드포인트에 접근"
date: 2021-02-03
excerpt: "AWS의 SageMaker에서 배포한 엔드포인트를 AWS Lambda를 이용해 서버리스로 외부에서 접근하는 방법을 배운다."
tags: [SageMaker]
comments: False
category: SageMaker
use_math: true
---

AWS의 SageMaker에서 만든 모델을 엔드포인트로 배포하고 나면, 여기에 데이터를 넣어 모델의 아웃풋을 확인할 수 있다.

그러나, 이는 SageMaker내부에서만 가능한 작업으로 외부에서 데이터가 들어오고 그것을 추론하기 위해서는 접근 권한이 필요하다.

이때, 별도의 서버를 개설하지 않고도 요청이 오면 그때 즉각적으로 엔드포인트에 데이터를 전송하고 다시 결과를 받아 올 수 있는 서버리스 형태의 AWS 서비스인 Lambda가 존재한다.

따라서, 본 포스트에서는 AWS Lambda를 이용하여 외부에서의 엔드포인트 접근을 배우게 된다.

## Build Model & Deploy Endpoint
우선 모델을 만들고 엔드포인트를 배포해야 하니 다음의 절차를 따라준다.
1. AWS S3에 데이터를 넣기
2. AWS SageMaker 노트북에서 모델 학습 및 엔드포인트 배포

#### AWS S3에 데이터를 넣기
데이터는 아래의 링크를 접속하면 바로 받을 수 있다.

https://s3.amazonaws.com/mlforbusiness/ch04/inbound.csv

링크에서 데이터셋을 받았으면, [S3](https://s3.console.aws.amazon.com/s3/home?region=us-east-2)에 새로운 버킷을 만들고 데이터를 넣어준다.

#### AWS SageMaker 노트북에서 모델 학습 및 엔드포인트 배포
아래의 ipynb파일을 SageMaker 노트북에서 그대로 실행시켜 준다.

https://github.com/silverstar0727/ml4biz/blob/main/ch08/customer_support_slugify.ipynb

## 
