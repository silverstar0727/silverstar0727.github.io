---
layout: post
title: "도커 컨테이너(실습)"
date: 2021-01-30
excerpt: "도커를 설치하고 도커 컨테이너를 다루는 것을 간단하게 배운다"
tags: [Docker]
category: Docker & Kubernetes
comments: False
use_math: true
---

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

## 도커 컨테이너 다루기
~~~
docker
~~~
