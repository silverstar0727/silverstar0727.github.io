---
layout: post
title: "Dockerfile(2)"
date: 2021-02-20
excerpt: "Dockerfile을 다루는 것을 배운다."
tags: [Docker]
category: Docker & Kubernetes
comments: False
use_math: true
---

## Dockerfile 실행 
이미지 빌드 시작 -> 빌드 컨텍스트 (이미지를 생성하는 데 필요한 각종 파일, 코드, 메타데이터를 담은 디렉터리 = Dockerfile위치)를 읽음 -> Dockerfile에 기록된 대로 step을 이어나가고, 각 스텝마다 컨테이너를 생성하고 이미지로 커밋함
이미지 빌드가 끝나고 다시 실행하면 이전에 저장한 캐시를 활용하여 빌드함


## 빌드컨텍스트에서 주의해야 할 점 
디렉터리에 다른 것들이 존재하면 모두 읽게 됨. —> .dockerignore파일을 작성하여 해당파일에 명시된 파일을 무시

.dockerignore 파일은 Dockerfile과 함께 있어야 함

.dockerignore example

~~~
vi .dockerignore
test2.html
*.html
*/*.html
test.htm?       # html htma 등의 파일 제외
~~~

## 새로운 컨테이너 생성 및 커밋
Dockerfile 내부에 아래와 같은 코드를 실행한다고 가정
~~~
FROM ubuntu:14.04
RUN apt-get update
~~~

FROM ubuntu:14.04를 실행 -> 새로운 이미지 레이어 생성(1) -> 새 이미지 생성(1)
-> RUN apt-get update를 실행 -> 이미지(1) 위에 이미지 레이어 (2) 생성 -> 이미지(1)과 이미지레이어(2)를 묶은 이미지(2) 생성


## 캐시 활용하지 않는 경우
깃헙에서 내용이 바뀌었지만, RUN git clone~~ 에서 캐시를 이용하여 반영하지 않는 경우 아래의 코드를 활용하여 빌드
~~~
docker build —no-cache -t mybuild:0.0
~~~

또는 직접 지정도 가능 (—cache-from)

## 멀티스테이지 빌드 (생략)

## 기타 Dockerfile 명령어
* ENV: 환경변수 설정

test라는 환경변수에 /home을 설정하고 싶으면 아래코드 처럼 지정하고 사용
~~~
ENV test /home

$test
# or ${test}
~~~

* VOLUME: 호스트와 공유할 내부 디렉토리

~~~
VOLUME [“home/dir1”, “home/dir2”]
~~~

* USER: 루트권한이 아니라 유저권한으로 실행

~~~
RUN groupadd -r author && useradd -r -g author alicek106
USER alicek106
~~~

* SHELL: 사용하려는 셀을 별도 지정

기존: /bin/sh -c 에서 RUN이 실행 됨
아래코드: SHELL의 json 배열 값에서 실행이 됨
~~~
FROM node
RUN echo hello, node!
SHELL [“/usr/local/bin/node”]
RUN -v
~~~

* ADD: COPY 상위 호환 여러가지 가능

~~~
#ADD https:~~~ /home
#ADD ./~~ /home
~~~

* ENTRYPOINT, CMD: 컨테이너가 시작될 때 수행할 명령을 지정

~~~
docker run ubuntu:14.04 /bin/bash
docker run —entrypoint=“echo” ubuntu:14.04 /bin/bash
~~~

엔트리 포인트를 사용할 경우 시작 후 echo /bin/bash 명령어가 실행 됨.
따라서 이미지가 작동하기 위에서는 아래 단계를 맞춰야 함

1. 설정사항 -> 스크립트로 정리
2. ADD로 이미지 복사
3. ENTRYPOINT로 스크립트 지정
4. 이미지를 빌드해서 사용
5. 스크립트에서 필요한 인자는 docker run 명령어에서 cmd로 entrypoint 스크립트에 전달

example
~~~
vi Dockerfile
FROM ubuntu:14.04
RUN apt-get update
RUN apt-get install apache2 -y
ADD entrypoin.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT [“/bin/bash”, “/entrypoint.sh”]
~~~

~~~
vi entrypoint.sh
echo $1 $2
apachectl -DFOREGROUND
~~~

~~~
docker build -t entrypoint_image:0.0 ./
~~~



## Reference
* [Docker, Docker-compose 설치하기](https://velog.io/@dudu/Docker-Docker-compose-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0-Ubuntu-18.04)
