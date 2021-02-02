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
아래의 ipynb파일을 SageMaker 노트북에서 그대로 실행시켜 준다(훈련부터 모델 배포까지 포함하고 있기 때문에 엔드포인트가 생성될 것이다.).

https://github.com/silverstar0727/ml4biz/blob/main/ch08/customer_support_slugify.ipynb

## 서버리스 API 엔드포인트 설정
다음의 순서를 따라서 실행을 하자

1. SageMaker 엔드포인트에 엑세스하기 위한 서버리스 API의 권한 설정
2. 로컬 컴퓨터에서 AWS 자격증명 설정
3. 자격증명 구성
4. Chalice를 이용한 프로젝트 생성
5. 세이지메이커 엔드포인트 추가
6. 권한 구성
7. 라이브러리 설정
8. chalice 배포

#### SageMaker 엔드포인트에 엑세스하기 위한 서버리스 API의 권한 설정
https://console.aws.amazon.com/iam/home?region=us-east-2#/security_credentials

위의 링크에서 엑세스 키 부분을 클릭하여 새로운 엑세스 키를 다운 받으면 csv파일로 다운로드가 된다.

이것을 잘 보관하자.

#### 로컬 컴퓨터에서 AWS 자격증명 설정
다른 개발환경을 로컬컴퓨터에서 사용해도 되나, 독립성을 위해 Goorm ide를 사용하여 개발을 하였다.

터미널에서 아래의 명령어를 입력하여 boto3로는 s3접근을 awscli로는 aws 커맨드라인을 활용하자
~~~
pip install boto3
pip install awscli
~~~

#### 자격증명 구성
아래의 명령어로 구성을 확인하자.
~~~
aws configure
~~~

그럼 아래와 같은 입력 요구사항이 뜨는데, 이 때 엑세스 키를 csv파일로 다운받을 것을 열어 ID와 Key를 입력하고 본인의 리전을 알맞게 입력한 뒤, output format은 엔터를 그냥 눌러 건너뛰면 된다.
~~~
# 이것은 예시이다
AWS Access Key ID: ABCDAFWEFDAG
AWS Secret Access Key: ASDFAFHBFNVFNB
Default region name: us-east-1
Default region name: 
~~~

#### Chalice를 이용한 AWS Lambda 생성 및 배포, API 게이트웨이 엔드포인트 구성
Chalice를 이용해 Lambda를 활용해보자.
~~~
# 설치
pip install chalice

# 새프로젝트 만들기
chalice new-project tweet_escalator
~~~

위 명령어를 터미널에서 실행하면 chalice가 설치되고 tweet_scalator라는 새로운 프로젝트가 만들어짐과 동시에 폴더와 내부 기본 파일이 만들어진다.

#### 세이지메이커 엔드포인트 추가
기본 프로젝트에 내장된 파일에서 눈여겨 볼 것은 다음의 세 가지이다.
* .chalice: 권한설정
* app.py: 애플리케이션 동작 코드
* requirements.txt: 필요한 라이브러리 리스트

우선 app.py 코드를 세이지메이커 엔드포인트를 제공하도록 모두 지우고 아래의 링크의 내용을 집어넣자.

https://github.com/silverstar0727/ml4biz/blob/main/ch08/app.py

#### 권한 구성
그러나 chalice는 여전히 Lambda API에 접근할 수 없을 것이다. 따라서 권한을 설정해야 한다.

이것은 위에서 말한 .chalice폴더 내부의 policy-dev.json이라는 파일에서 설정하며 이것 또한 모두 지우고 아래의 내용을 집어 넣자.

https://github.com/silverstar0727/ml4biz/blob/main/ch08/policy-dev.json


#### 라이브러리 설정
requrirements.txt 파일에서 slugify 라이브러리를 이용할 수 있도록 다음의 내용을 추가해주자
~~~
python-slugify
~~~

#### Chalice 배포
이제 모든 작업이 끝났다 터미널에 아래의 명령어를 입력하면 AWS SageMaker의 Endpoint는 AWS Lambda를 통해 웹서버와 소통이 가능하게 된다.

~~~
chalice deploy
~~~

이렇게 배포된 url의 뒷부분에 원하는 텍스트를 넣으면 그것에 대한 결과값을 출력하게 된다.

이때 url에 텍스트를 넣는 것을 변환하기 위해서 slugify를 사용하였고 그렇기에 라이브러리 설정에서 python-slugify 코드를 집어넣은 것이다.
