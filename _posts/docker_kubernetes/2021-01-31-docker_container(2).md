---
layout: post
title: "도커 컨테이너(실습)(2)"
date: 2021-01-30
excerpt: "도커 컨테이너를 다루는 것을 간단하게 다룹니다"
tags: [Docker]
category: Docker & Kubernetes
comments: False
use_math: true
---

이전 포스트와 이어지므로 [이전 포스트](https://silverstar0727.github.io/docker%20&%20kubernetes/2021/01/30/docker_container(1)/#)를 반드시 봐야합니다.

## Environments
GCP e2-medium instance(Ubuntu 18.04)

## 컨테이너 애플리케이션 구축
웹 서비스를 만든다고 했을 때, 웹 서버와 데이터 베이스를 같은 컨테이너에서 사용하는 것은 편리하지 못합니다.
따라서 각각의 컨테이너로 관리해야 합니다.

아래의 명령어는 mysql 이미지로 데이터베이스 컨테이너를 만드는 것입니다.
~~~
sudo docker run -d \
--name wordpressdb \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
mysql:5.7
~~~

> e 옵션은 MYSQL_ROOT_PASSWORD의 환경변수를 passwod로 설정한다는 것입니다.
> d 옵션은 컨테이너를 백그라운드에서 실행하도록 합니다.

아래의 명령어는 wordpress 웹서버 컨테이너를 만드는 것입니다.
~~~
sudo docker run -d \
-e WORDPRESS_DB_PASSWORD=password
--name wordpress
-p 80 \
wordpress
~~~

ps 명령어로 컨테이너의 포트와 생성 유무를 확인할 수 있습니다.

## 도커 볼륨
컨테이너는 한 번 삭제되면 내부의 데이터가 모두 사라지기에 이를 다른 저장소에 보관해야 합니다. 방법은 총 3가지로 호스트와의 공유, 컨테이너와의 공유, 도커 자체 볼륨과의 공유가 있습니다.
#### 호스트와의 볼륨 공유

아래 명령어를 통해 호스트 볼륨과 공유하는 mysql 데이터베이스 컨테이너와 웹 서버 컨테이너를 생성합니다.

설정은 v옵션으로 [호스트 dir]:[컨테이너 dir] 입니다.
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
다른 컨테이너와 볼륨을 공유하기 위해서는 컨테이너 생성 시, --volumes-from 옵션을 설정하여 -v 옵션을 설정한 다른 컨테이너와 공유할 수 있습니다.

볼륨 구조를 host와 연결된 컨테이너(volume_overide), 그리고 이 컨테이너와 연결된 새로운 컨테이너(volumes_from_container)를 만들어 봅시다.

우선 아래 코드를 통해 alicek106/volume_test라는 미리 준비된 이미지로 컨테이너를 만들고 이것을 호스트의 /home/wordpress_db와 연결합니다.
~~~
# volume_overide
sudo docker run -i -t \
--name volume_overide \
-v /home/wordpress_db:/home/testdir_2 \
alicek106/volume_test
~~~

이후 위에서 생성한 컨테이너를 --volumes-from의 옵션을 통해 위의 container와 연결합니다.
~~~
# volumes_from_container
sudo docker run -i -t \
--name volumes_from_container \
--volumes-from volume_overide\
ubuntu:14.04
~~~

#### 도커 볼륨 활용
마지막은 도커에서 제공하는 docker volume 명령어를 사용하는 방법입니다. 

create 명령어를 통해 볼륨을 생성합니다.
~~~
sudo docker volume create --name myvolume
~~~

ls로 볼륨을 확인할 수 있습니다.
~~~
sudo docker volume ls
~~~

이제 컨테이너를 생성하고 여기에 연결하기 위해서는 다시 v옵션을 통해 연결하면 됩니다.
~~~
sudo docker run -i -t --name myvolume_1 \
-v myvolume:/root/ \
ubuntu:14.04
~~~

inspect 명령어는 myvolume의 볼륨이 실제로 어디에 저장되는 지 확인할 수 있습니다.
~~~
sudo docker inspect --type volume myvolume
~~~

~~~
sudo docker container inspect myvolume_1
~~~

삭제 시에는 prune 명령어를 사용합니다.
~~~
docker volume prune
~~~

## Referenece
* [시작하세요 도커 쿠버네티스](http://www.yes24.com/Product/Goods/93765519)
