---
layout : post
title: "Azure Designer 기초"
date: 2021-03-11
excerpt: "Azure에서 codeless AI 솔루션인 Designer를 사용해봅시다."
tags: [MLOps]
comments: False
use_math: true
---

이번 포스트에서는 Azure에서 디자이너를 이용하여 codeless AI를 적용하는 방법을 공유해 드리고자 합니다.

## ML Studio에서 디자이너 시작하기
우선 Azure의 ML studio에 아래와 같이 접속하도록 합니다.

![image](/assets/post/AzureDesigner-jeongmin/fig1.png)
<center>Fig.1</center>

그리고 나서는 아래 그림에서의 왼쪽에 적힌 디자이너를 클릭하여 디자이너를 시작하도록 해줍니다.

![image](/assets/post/AzureDesigner-jeongmin/fig2.png)
<center>Fig.2</center>

위 두 과정을 거치고 나면, 새 파이프 라인을 만들기 위해 아래와 같은 버튼을 클릭하여 새로운 파이프라인을 만들어 줍시다.

![image](/assets/post/AzureDesigner-jeongmin/fig3.png){: width="100" height="100"}
<center>Fig.3</center>

그리고 나서는, 아래 그림에서 1이라고 표시된 기본 컴퓨팅 대상을 선택한 뒤, 2라고 표시된 곳에서 앞으로 작업을 하게될 예정입니다.

![image](/assets/post/AzureDesigner-jeongmin/fig4.png)
<center>Fig.4</center>

컴퓨팅 대상을 선택하면 새로운 컴퓨팅을 만들어야 하는데, 저는 여기서 기본적으로 구성되어 있는 2개의 cpu와 7GB의 RAM을 사용하도록 컴퓨팅을 선택하였습니다.

![image](/assets/post/AzureDesigner-jeongmin/fig5.png)
<center>Fig.5</center>

컴퓨팅을 생성하기 위해서는 상태가 creating에서 성공적으로 완료될 때까지 잠시 기다려 주어야 합니다. 저도 여기서 아무 생각 없이 읽지도 않고 진행했다가 왜 안되지 하면서 난감했던 기억이….

![image](/assets/post/AzureDesigner-jeongmin/fig6.png)
<center>Fig.6</center>

위 과정까지 마치면, 디자이너를 실행하기 위한 기본과정을 마친 것입니다. 따라서 다음으로는, 파이프라인을 어떻게 codeless하게 빌드하고 실행을 하면 되는 지 알아봅시다.

## Building Pipeline
우선  파이프라인을 빌드하는 순서를 개괄적으로 설명하자면 아래의 과정을 거치게 됩니다.
1. 데이터 선택
2. 데이터 preprocessing module 선택
3. ML Algorithm 선택
4. Model Validation

앞의 Fig.4에서 봤던 좌측에 해당하는 2에서 앞으로의 프로세스를 계속 진행하게 됩니다. Azure에 pre-built된 많은 module들이 존재하는 것을 확인할 수 있고, 주어진 태스크를 수행하기 위해서는 이러한 모듈들을 효율적으로 배치하여 실행하면 됩니다.

#### 데이터 선택
처음에는 데이터 선택의 과정이 있습니다. 데이터 역시 custom 데이터를 사용할 수 있고, 당연하게도 이것을 지원해야 하겠지만, 튜토리얼 과정에서의 번거로움을 덜어주기 위해 이번 포스트에서는 Azure에서 제공하는 데이터를 사용하였습니다. 

모듈 이름은 “Automobile price data (Raw)”이고, 이것을 드래그 하여 중앙 캔버스에 드랍하면 됩니다. 해당 데이터 셋의 기본 통계치가 궁금하다면, 그 모듈을 눌러 시각화된 데이터셋을 확인할 수 있습니다.

![image](/assets/post/AzureDesigner-jeongmin/fig7.png){: width="200" height="600"}
<center>Fig.7</center>

#### 데이터 preprocessing module 선택
이후로는 Select Columns in Dataset이라는 모듈을 통해 데이터 셋에서 특정 컬럼을 선택하게 됩니다.

해당 모듈을 캔버스에 끌어다 놓으면 아래 그림과 같이 경고 문구가 뜨는 것을 볼 수 있는데, 이는 어떠한 columns을 선택할지 정하지 않았기 때문에 발생하는 오류로 우측에서 열편집에 들어가서 다음 작업을 수행해야 합니다.

![image](/assets/post/AzureDesigner-jeongmin/fig8.png)
<center>Fig.8</center>

우선 첫번째 규칙에서  포함에 모든 열을 선택해줍니다. 그리고 규칙을 추가하여 제외의 열 이름에 normalized-losses를 선택하고 저장을 하면 됩니다. 아래의 그림과 같습니다.

![image](/assets/post/AzureDesigner-jeongmin/fig9.png)
<center>Fig.9</center>

위와 같은 방식으로 Clean Missing Data Module을 사용하고, Split Data를 통해 전처리를 마무리 해주도록 합니다.

#### ML Algorithm 선택, Train
이젠 어떤 알고리즘을 선택할지 정하고 훈련을 진행하면 됩니다. 

이번 포스트에서 알고리즘은 단순히 테스트하기 위한 목적으로 진행되었기에 Linear Regression의 pre-built된 모델을 사용하였습니다.

#### Model Validation
Train 모듈까지 선택했으면 이제 완성된 모델을 평가하는 모듈을 놓아야 합니다. 이번 포스트에서는  “Score Model”과 “Evalutate Model”을 사용하였습니다. 

![image](/assets/post/AzureDesigner-jeongmin/fig10.png){: width="400" height="600"}
<center>Fig.10</center>

완성된 파이프라인은 아래 그림과 같고, 제출을 누르면 파이프라인이 실행됩니다.

이번 프로젝트의 파이프라인은 약 5분정도 소요되며, 모든 것이 완료되면 아래 그림과 같이 나오게 됩니다.

![image](/assets/post/AzureDesigner-jeongmin/fig11.png){: width="400" height="600"}
<center>Fig.11</center>


## Reference
* [Azure ML Docs](https://docs.microsoft.com/ko-kr/azure/machine-learning/tutorial-designer-automobile-price-train-score?view=azure-devops)
