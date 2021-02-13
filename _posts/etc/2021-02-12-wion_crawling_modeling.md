---
layout: post
title: "재무제표 크롤링, 모델링 자동화"
date: 2021-02-12
excerpt: "재무제표를 크롤링하고 가치평가를 하기 위한 모델링을 자동화 하는 파이썬 코드."
tags: [etc]
category: etc
comments: False
use_math: true
---


# WION-Crawling-Modeling
연세대학교 미래캠퍼스의 투자동아리 WION에서 사용하고자, 재무제표를 크롤링하고 이를 가치평가 하기 위해 모델링 하는 작업의 자동화를 파이썬으로 코딩하였다.

구글의 colab에서 코드작성 및 실행을 진행하였으며, 아래의 Usage 항목에 따라 그대로 실행이 가능하다.

# Usage
1. [해당 링크의 repo](https://github.com/silverstar0727/WION-Crawling-Modeling)에 .ipynb 파일을 다운받아 구글 코랩에서 실행시킨다.
2. colab과 구글 드라이브를 마운트 시켜준다.
3. auto_crawling과 auto_modeling에서 본인의 구글 드라이브 위치를 적절히 넣어준다.
4. auto_crawling을 실행시켜 IS, BS, CF파일이 성공적으로 만들어진 것을 확인한 뒤, auto_modeling파일을 실행시켜 준다.

# Result
![image](https://user-images.githubusercontent.com/49096513/107860680-f5e30880-6e83-11eb-8358-984032fd4eb8.png)


위 그림과 같이 modling의 csv파일이 생성된 것을 확인할 수 있다.
