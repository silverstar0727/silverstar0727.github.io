---
layout: post
title: "도커 컨테이너(실습)(2)"
date: 2021-01-30
excerpt: "도커 컨테이너를 다루는 것을 간단하게 배운다"
tags: [Docker]
category: Docker & Kubernetes
comments: False
use_math: true
---

이전 포스트와 이어지므로 [이전 포스트](https://silverstar0727.github.io/docker%20&%20kubernetes/2021/01/30/docker_container(1)/#)를 반드시 봐야한다.

## Environments
GCP e2-medium instance(Ubuntu 18.04)

## 컨테이너 애플리케이션 구축
웹 서비스를 만든다고 했을 때, 웹 서버와 데이터 베이스를 같은 컨테이너에서 사용하는 것은 편리하지 못하다.
따라서 각각의 컨테이너로 관리해야 한다.

아래의 명령어는 mysql 이미지로 데이터베이스 컨테이너를 만드는 것이다.
~~~
sudo docker run -d \
--name wordpressdb \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
mysql:5.7
~~~

> e 옵션은 MYSQL_ROOT_PASSWORD의 환경변수를 passwod로 설정한다는 것이다.
> d 옵션은 컨테이너를 백그라운드에서 실행하도록 한다.

아래의 명령어는 wordpress 웹서버 컨테이너를 만드는 것이다.
~~~
sudo docker run -d \
-e WORDPRESS_DB_PASSWORD=password
--name wordpress
-p 80 \
wordpress
~~~

ps 명령어로 컨테이너의 포트와 생성 유무를 확인할 수 있다.

## 도커 볼륨
컨테이너는 한 번 삭제되면 내부의 데이터가 모두 사라지기에 이를 다른 저장소에 보관해야 한다. 방법은 총 3가지로 호스트와의 공유, 컨테이너와의 공유, 도커 자체 볼륨과의 공유가 있다.
#### 호스트와의 볼륨 공유

아래 명령어를 통해 호스트 볼륨과 공유하는 mysql 데이터베이스 컨테이너와 웹 서버 컨테이너를 생성한다.

설정은 v옵션으로 [호스트 dir]:[컨테이너 dir] 이다.
~~~
sudo docker run -d \
--name wordpressdb_hostvolume \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
-v /home/wordpress_db:/var/lib/mysql \
mysql:5.7
~~~

~~~
sudo docker run -d \
-e WORDPRESS_DB_PASSWORD=password \
--name wordpress_hostvolume \
--link wordpressdb_hostvolume:mysql \
-p 80 \
wordpress
~~~

#### 컨테이너와의 볼륨 공유
