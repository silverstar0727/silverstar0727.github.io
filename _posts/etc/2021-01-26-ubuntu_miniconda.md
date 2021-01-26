---
layout: post
title:  "ubuntu에서 miniconda로 pytorch와 tensorflow 설치하기"
date:   2021-01-26
excerpt: "ubuntu에서 miniconda의 사용"
category: OS
tag: ubuntu
comments: False
---

## Environments
GCP e2-medium instance(Ubuntu 18.04)

## 설치과정
우선 명령 터미널을 연 상태에서 miniconda를 설치해 준다.

* download miniconda

~~~
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
~~~

* install miniconda

~~~
bash Miniconda3-latest-Linux-x86_64.sh
~~~

여기서 글이 빼곡히 나오는데, 엔터를 계속 누르다가 yes -> enter -> yes 순으로 입력하라고 할때마다 해주면 된다.

Thank you for installing Miniconda3

해당 글이 나오면 성공!

* miniconda VM

~~~
source ~/.bashrc
~~~

* downgrade python version

쌩으로 miniconda를 설치하면 python 버전이 3.9로 설치되는데, 이러면 pytorch와 호환성이 안맞아서 오류가 나기 때문에 버전을 변경해야 한다. 
~~~
conda install python=3.7
~~~

* install VM

여기서 test는 가상환경의 이름을 가리키므로 본인이 원하는 것으로 설정하면 된다.
~~~
conda create -y -n test
~~~

* VM

~~~
conda activate test
~~~

* install pytorch, tensorflow

~~~
conda install pytorch
conda install tensorflow
~~~

## 실행
실행을 하고자 하는 파일이 있다면 vim, nano등의 우분투 파일편집기를 활용하여 python 파일을 생성하고 실행하면된다.

여기서는 dcgan을 pytorch로 구현한 파일을 생성하였다.

* 파일 생성

이름을 dcgan으로 했지만 원하는 이름으로 설정하면 된다.
~~~
vim dcgan.py
~~~

insert키를 눌러 아래 코드를 복사하여 붙여넣고 :wq를 입력하여 수정하고 종료한다.

~~~
""" configuration json을 읽어들이는 class """
class Config(dict): 
    __getattr__ = dict.__getitem__
    __setattr__ = dict.__setitem__

    @classmethod
    def load(cls, file):
        with open(file, 'r') as f:
            config = json.loads(f.read())
            return Config(config)
        
        
config = Config({
    'batch_size': 128,
    'learning_rate': 0.0002,
    'epochs': 500,
    'in_features': 256,
    'in_channels': 64
})


import torch
import torch.nn as nn
import torch.optim as optim
import torch.nn.init as init


class Generator(nn.Module):
  def __init__(self, config):
    super(Generator, self).__init__()
    self.config = config

    # def layers
    self.fc1 = nn.Linear(in_features=self.config.in_features, out_features = 7*7*128)
    self.bn1 = nn.BatchNorm2d(7*7*128)
    self.conv2tr1 = nn.ConvTranspose2d(in_channels=128, out_channels=64, kernel_size=5, stride=2)
    self.bn2 = nn.BatchNorm2d(64)
    self.conv2tr2 = nn.ConvTranspose2d(in_channels=64, out_channels=64, kernel_size=5, stride=2)

  # def logic
  def forward(self, x):
    x = self.fc1(x)
    x = self.bn1(x)
    x = nn.LeakyReLU(x)
    x = x.view(-1, 7, 7, 128)
    x = self.conv2tr1(x)
    x = self.bn2(x)
    x = nn.LeakyReLU(x)
    x = self.conv2tr2(x)
    x = nn.tanh(x)

    return x
  
  
class Discriminator(nn.Module):
  def __init__(self, config):
    super(Discriminator, self).__init__()
    self.config = config

    self.conv1 = nn.Conv2d(in_channels = self.config.in_channels ,out_channels=64, kernel_size=5, stride = 2)
    self.bn1 = nn.BatchNorm2d(64)
    self.conv2 = nn.Conv2d(in_channels = 64, out_channels = 128, kernel_size=5, stride = 2)
    self.bn2 = nn.BatchNorm2d(128)
    self.flatten = nn.Flatten()
    self.fc1 = nn.Linear(in_features=2048, out_features=1024)
    self.bn3 = nn.BatchNorm2d(1024)
    self.fc2 = nn.Linear(in_features=1024, out_features = 2)

  def forward(self, x):
    x = x.view(-1, 28, 28, 1) 
    x = self.conv1(x)
    x = self.bn1(x)
    x = nn.LeakyReLU(x)
    x = self.conv2(x)
    x = self.bn2(x)
    x = self.flatten(x)
    x = self.fc1(x)
    x = self.bn3(x)
    x = nn.LeakyReLU(x)
    x = self.fc2(x)

    return x
  
  
generator = Generator(config)
discriminator = Discriminator(config)

print(generator)
print(discriminator)
~~~

* 실행

python3 명령을 통해 본인이 생성한 파일을 실행시키면 된다.
~~~
python3 dcgan.py
~~~

최종적으로 모델이 출력되면 성공!!
