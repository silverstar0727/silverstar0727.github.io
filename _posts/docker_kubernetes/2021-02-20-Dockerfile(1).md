---
layout: post
title: "Dockerfile(1)"
date: 2021-02-12
excerpt: "Dockerfile을 작성하는 것을 배운다."
tags: [Docker]
category: Docker & Kubernetes
comments: False
use_math: true
---

## write Dockerfile

아래 코드를 통해 dockerfile이라는 디렉토리를 만들고 해당 디렉토리에 접속하여 test.html 파일 하나를 생성하자.
~~~
mkdir dockerfile
cd dockerfile
echo test >> test.html
~~~

다시 아래의 코드를 이용해서 이번에  Dockerfile을 만드는데, vi 파일 편집기를 사용하자.
~~~
vi Dockerfile
~~~

~~~
FROM ubuntu:14.04
LABEL maintainer “silverstar456 <silverstar456@yonsei.ac.kr>”
LABEL “purpose”=“practice”
RUN apt-get update
RUN apt-get install apache2 -y
ADD test.html /var/www/html
WORKDIR /var/www/html
RUN [“/bin/bash”, “-c”, “echo hello >> test2.html”]
EXPOSE 80
CMD apachectl -DFOREGROUND
~~~

위 Dockerfile을 한 줄씩 간단히 설명하자면 다음과 같다.

* FROM ubuntu:14.04
	* 이미지의 베이스가 될 이미지를 뜻한다.
* LABEL maintainer “silverstar456 <silverstar456@yonsei.ac.kr>”
	* 이미지를 생성한 저자를 나타낸다. (LABEL은 메타데이터를 키: 값 형태로 저장한다.)
* RUN
	* 이미지를 만들기 위한 컨테이너 내부 명령어이고, y와 같은 중간 설치 단계를 미리 설정해주어야 한다.
* ADD test.html /var/www/html
	* test.html 의 로컬 파일을 컨테이너 내부 /var/www/html 디렉토리로 가져온 것이다.
* WORKDIR
	* cd와 같은 역할을 한다.
* EXPOSE 80
	* 이미지에서 노출할 포트를 지정한다.
* CMD
	* 시작될 때의 커맨드를 설정한다.

## build Dockerfile
Dockerfile을 빌드하기 위해서는 아래의 명령어를 통해 빌드하면 된다. 여기서 t옵션은 생성될 이미지의 이름이고, 마지막은 Dockerfile이 존재하는 디렉토리를 입력해야한다.

~~~
sudo docker build -t mybuild:0.0 ./
~~~

## 생성된 이미지 확인
아래의 코드를  통해 생성된 이미지로 컨테이너를 만들 수 있다. d옵션으로 백그라운드에서 실행하고 P옵션을 통해 기존에 노출했던 80번 포트를 사용하여 myserver라는 컨테이너를 mybuild:0.0 이미지를 활용하여 만든다.
~~~
sudo docker run -d -P —name myserver mybuild:0.0
~~~
