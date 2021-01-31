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

아래의 명령어는 mysql 이미지로 데이터베이스 컨테이너를 만드는 것이다. d옵션은 백그라운드에서 실행하도록 한다.
~~~
sudo docker run -d \
--name wordpressdb \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
mysql:5.7
~~~

아래의 명령어는 wordpress 웹서버 컨테이너를 만드는 것이다.
~~~
sudo docker run -d \
-e WORDPRESS_DB_PASSWORD=password
--name wordpress
-p 80 \
wordpress
~~~

ps 명령어로 컨테이너의 포트와 생성 유무를 확인할 수 있다.



