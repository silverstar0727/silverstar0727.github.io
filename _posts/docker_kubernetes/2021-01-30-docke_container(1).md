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
sudo docker rename (이전이름) (바꿀이름)
~~~

위와 같은 형식으로 지정해 준다. 컨테이너 생성 시 name옵션으로 이름을 지정해주지 않으면 랜덤으로 이름이 설정되므로 이름을 변경할 필요가 있다.

## 컨테이너 삭제
컨테이너를 삭제할 때는 아래의 명령어를 통해 삭제가 가느하다.
~~~
sudo docker rm (컨테이너이름)
~~~

그런데, 실행중인 컨테이너는 삭제가 안되는데, 아래의 두 가지로 삭제할 수 있다.
~~~
sudo docker stop (컨테이너이름)
sudo docker rm (컨테이너이름)
~~~

또는,

~~~
sudo docker rm -f (컨테이너이름)
~~~

모든 컨테이너를 삭제할 때는 아래의 명령어를 이용한다.
~~~
sudo docker container prune
~~~

## 컨테이너 외부에 노출
컨테이너는 가상 ip주소를 할당 받는다. 따라서 컨테이너를 생성한 후 ifconfig를 통해 컨테이너의 네트워크 인터페이스를 확인한다.
~~~
# 컨테이너 생성 후 접속
sudo docker run -i -t --name network_test ubuntu:14.04

# 컨테이너 접속 후 아래 명령어를 실행
ifconfig
~~~

설정을 아무것도 하지 않은 경우 외부에서 접속이 불가능하다. 따라서, eth0와 IP포트를 호스트의 IP와 포트에 바인딩 해야한다.

종료 후 아래의 명령어를 통해 p옵션으로 호스트의 포트와 바인딩 할 수 있도록 해준다. 여러개를 쓰면 여러개의 포트를 개방할 수 있다
~~~
# 80포트를 호스트의 7777포트로 바인딩
sudo docker run -i -t -p 7777:80 ubuntu:14.04
# 추가로 192.168.0.100의 아이피에 3306포트를 3306포트에 바인딩
#sudo docker run -i -t -p 7777:80 -p 192.168.0.100:3306:3306 ubuntu:14.04
~~~

컨테이너 내부에서 아래의 명령어를 통해 아파치 웹 서버를 설치할 수 있다.
~~~
apt-get update
apt-get install apache2 -y
service apache2 start
~~~

## Referenece
* [시작하세요 도커 쿠버네티스](http://www.yes24.com/Product/Goods/93765519)
