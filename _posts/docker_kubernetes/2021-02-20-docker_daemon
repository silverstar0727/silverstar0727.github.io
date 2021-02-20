docker daemon: 도커 자체를 다뤄보자

## 도커의 구조
클라이언트 기능을 하는 도커: usr/bin/docker
서버 기능을 하는 도커: usr/bin/dockerd

docker daemon: 도커 프로세스가 실행되어 서버 기능을 하는 도커가 입력을 받을 준비가 된 상태
docker client: /var/run/docker.sock에 위치한 유닉스 소켓을 통해 명령을 도커 데몬의 API를 호출하는 방식

## 실행 순서
개발자의 명령 (ex) docker version) -> docker client -> 유닉스 소켓을 사용해서 명령어를 전달(/var/run/docker.sock) -> docker daemon은 명령어를 파싱하고 작업을 수행 -> 수행 결과를 docker client에 반환하고 결과를 출력

##docker daemon 실행
~~~
service docker start
service docker stop

# 자동으로 실행
systemctl enable docker 

dockerd
# 종료하고 싶으면 ctrl + c
~~~

## docker daemon 제어(미완)

## docker daemon monitoring

* docker daemon debug mode: 모든 명령어를 로그로 출력
* events: 컨테이너와 이미지 관련 명령어를 실시간 스트림 로그로 보여줌
* stats: 자원 사용량을 실시간 스트림으로 출력
* system df: 도커에서 사용하고 있는 이미지, 컨테이너, 볼륨 개수 등의 스토리지 공간 출력
~~~
# debug mode
docker -D

# events
docker events

# stats
docker stats

# system df
docker system df
~~~

* CAdvisor
구글이 만든 컨테이너 모니터링 도구, 8080포트로 연결하여 사용가능

## Remote API를 사용한 도커 원격제어(미완)
~~~
# 설치
sudo apt-get install python-pip -y
pip install docker

import docker
client = docker.DockerClient(base_url = ‘unix://var/run/docker.sock')
client.info()
~~~
