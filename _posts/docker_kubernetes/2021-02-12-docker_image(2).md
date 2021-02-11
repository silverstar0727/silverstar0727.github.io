---
layout: post
title: "Docker image(2)"
date: 2021-02-12
excerpt: "도커 이미지를 docker hub에 push하는 것을 배운다."
tags: [Docker]
category: Docker & Kubernetes
comments: False
use_math: true
---


## docker image deploy
앞선 포스트에서 바이너리 파일로 도커 이미지를 저장하는 것을 배웠으나, 이미지 구조의 레이어 형태를 이용하지 않으므로 비효율적이다.

따라서 Docker hub나 Docker private registry를 이용하는 것이 효과적이다. 

그러나 private registry는 유료이고 같이다루기엔 양이 방대하기에 public에 pull하는 것만을 본 포스트에서 다루도록 한다(다음에 추가적으로 다룰 예정..).

본 포스트의 실습을 따라하기 위해서는 선험적으로 docker hub에 가입되어 있어야 하며, 본인의 repo를 public하게 만들어주어야 한다.

## 이미지 생성
docker image(1)의 포스트에서 다룬대로 다음의 코드로 docker image를 생성하고 commit을 진행하도록 한다.
~~~
sudo docker run -i -t —name commit_container ubuntu:14.04
~~~
내부로 접속하여 아래 명령어를 통해 파일을 하나 만들어준다.

~~~
echo my first push >> test
~~~

그리고 다음엔 commit 명령어를 통해 이미지를 만들어 준다.
~~~
sudo docker commit commit_container my-image:0.0
~~~

## 이미지 이름 변경
위에서 이미지를 my-image:0.0으로 만들었으나, 이처럼 이름을 설정하면 docker hub에 올릴 수 없다. 따라서 tag명령어를 통해 이름을 {본인의 docker hub 이름}/{이미지 이름} 으로 변경해주도록 한다.

~~~
sudo docker tag my-image:0.0 silverstar456/my-image:0.0
~~~

## docker hub에 로그인
이제 docker hub에 로그인을 해보자. 아래의 코드를 통해 로그인을 할 수 있다. 차례로 아이디와 비밀번호를 입력하면 된다.
~~~
sudo docker login
~~~

## push the image
로그인이 성공적으로 완성되면 push 명령어를 통해 이미지를 docker hub에 푸시하도록 하자.
~~~
sudo docker push silverstar456/my-image:0.0
~~~

## pull the image
만약 푸쉬된 이미지를 내려받고 싶다면 아래의 명령어를 통해 pull을 하면 된다. 여기서는 별도의 로그인이 필요가 없다. 우리가 public에 올렸기 때문이다.
~~~
sudo docker pull silverstar456/my-image:0.0
~~~
