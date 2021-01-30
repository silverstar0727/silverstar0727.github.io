---
layout: post
title: "도커 컨테이너(실습)(1)"
date: 2021-01-30
excerpt: "도커를 설치하고 도커 컨테이너를 다루는 것을 간단하게 배운다"
tags: [Docker]
category: Docker & Kubernetes
comments: False
use_math: true
---

## Environments
GCP e2-medium instance(Ubuntu 18.04)

## 도커 설치
~~~
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
~~~

도커에는 EE(Enterprise Edition)과 CE(Community Edition)이 존재하는데, 그 중 CE stable 버전을 설치하였다.
~~~
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce=5:18.09.9~3-0~ubuntu-bionic docker-ce-cli=5:18.09.9~3-0~ubuntu-bionic containerd.io vim
~~~

아래 코드가 정상적으로 실행되면 성공적인 설치이다.
~~~
sudo docker run hello-world
~~~

## 도커 컨테이너 생성
* version

설치된 도커 엔진의 버전을 확인한다
~~~
docker -v
~~~

* docker container 생성

ubuntu:14.04 이미지로부터 도커 컨테이너를 생성한다. 옵션은 차후에 다룬다.

아래 명령어를 실행하면 로컬 도커엔진에 이미지가 존재하지 않아서 도커 허브에서 자동으로 내려받는다.

설치 후에는 도커 컨테이너 실행과 동시에 내부로 들어가게 된다. (호스트 이름이 변경된 것을 확인)
~~~
sudo docker run -i -t ubuntu:14.04
~~~

아래의 create부문에서 run과 다른 명령어로 container를 생성하는 것을 함께 공부해야 한다.

* exit

컨테이너를 빠져나오기 위해서는 exit 명령어나 ctrl + D를 이용하면 종료 뒤 빠져나올 수 있다.

종료시키지 않고 빠져나오기 위해서는 ctrl + Q를 입력하라.

* pull

centos:7이라는 이미지를 docker 공식 이미지 저장소에서 가져온다. 
~~~
sudo docker pull centos:7
~~~

이미지 목록은 아래의 코드로 확인이 가능하다
~~~
sudo docker images
~~~

* create
create는 컨테이너를 생성한다는 점에서 run과 동일하지만, 실행 및 접속을 하지 않는다는 점에서 차이가 존재한다.

아래의 명령어로 centos:7을 custom name으로 container를 생성해보자
~~~
sudo docker create -i -t --name mycentos centos:7
~~~

그럼 이젠 접속을 해보자
~~~
# 컨테이너 실행
sudo docker start mycentos
# 컨테이너 접속
sudo docker attach mycentos
~~~

즉, run 명령어는 create + start + attach 명령어를 실행시킨 것과 동일하다.

## 컨테이너 목록 확인
지금까지 생성한 '실행중인' 컨테이너 목록을 아래의 명령어로 확인이 가능하다.
~~~
sudo docker ps
~~~

실행중이지 않은 컨테이너 목록은 옵션 a를 붙여 확인해준다.

* 이름 변경

~~~
sudo docker rename renamin
~~~
