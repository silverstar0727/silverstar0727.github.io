---
layout: post
title: "Docker image(1)()"
date: 2021-02-11
excerpt: "도커 이미지를 생성하고 삭제, 추출하는 것을 간단하게 다룬다."
tags: [Docker]
category: Docker & Kubernetes
comments: False
use_math: true
---

## Docker search
도커는 기본적으로 도커 허브(Docker Hub)라는 중앙 이미지 저장소에서 이미지를 내려받는다. 따라서 각 도커 엔진은 앞에서 “docker pull”, “docker push” 등의 명령어를 통해 도커 허브와 상호작용한다.

docker search 명령어는 도커 허브에서 해당 이미지를 찾게하는 명령어이다. 아래 실습을 통해 ubuntu관련 이미지를 찾아보자.
~~~
sudo docker search ubuntu
~~~

## docker 이미지 생성
우선 생성에 앞서 컨테이너 하나를 생성하자. 이후 내부에 first라는 파일을 하나 만들어 ‘docker commit’ 명령어를 통해 이미지를 생성하자.

* ubuntu 컨테이너 생성

~~~
sudo docker run -i -t —name  commit_test ubuntu:14.04
~~~

* 파일생성

d 옵션을 사용하지 않았으므로 바로 컨테이너에 접속이 될텐데, 컨테이너에서 아래의 명령어를 통해 파일을 생성하면 된다.

~~~
echo test_first! >> first
~~~

* container to image

commit 명령어를 통해 컨테이너를 이미지로 변경해준다. 옵션을 조금 설명하자면, -a의 옵션은 author로 저자에 대한 내용을 담는다.
-m옵션의 경우에는 message로 커밋에 대한 내용을 적어준다. commit_test는 대상 컨테이너가 되고 commit_test:first는 이미지의 이름이 된다.

~~~
docker commit \
-a “alicek106” -m “my first commit” \
commit_test \
commit_test:first
~~~

이미지를 생성한 것을 아래의 명령어를 통해 확인할 수 있다.
~~~
sudo docker images
~~~

이렇게 생성된 이미지를 통해 다시 새로운 도커 컨테이너를 만들 수도 있다.
~~~
sudo docker run -i -t —name commit_test2 commit_test:first
~~~

## docker 이미지 삭제
이미지를 삭제하기 위해서는 해당 이미지를 이용하는 컨테이너를 삭제하고 rmi명령어를 활용하여 아래와 같이 삭제할 수 있다.

~~~
sudo docker kill commit_test2
sudo docker rm commit_test2
sudo docker rmi commit_test:fist
~~~

## 이미지 추출
이미지 추출이라고 하면 다소 생소할 수 있으나, 이미지를 단일 바이너리 파일로 만든다는 것을 의미한다. 종종 우리는 도커 이미지를 별도로 저장하거나 옮기는 등의 필요를 느끼기에 추출을 할 수 있도록 설계가 되어있다.

추출은 save 명령어를 통해 진행하며 아래와 같다. 여기서 사용되는 -o 옵션은 추출될 파일 명을 의미한다.
~~~
sudo docker save -o ubuntu_14.04.tar ubuntu:14.04
~~~

추출된 파일은 다시 load 명령어를 통해서 로드할 수 있다. 
~~~
sudo docker load -i ubuntu_14_04.tar
~~~

export나 import 명령어도 save , load와 동일한 역할을 하기는 하나 조금의 차이가 존재한다. 이와 관련해서는 도큐먼트를 찾아보길 바란다.
