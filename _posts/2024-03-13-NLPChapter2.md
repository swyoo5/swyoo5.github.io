---
layout: single
title:  "3/13 HuggingFace-NLP 2. Using Transformers"
categories: [Programming, transformer, NLP]
tag: [Programming, transformer, NLP]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

# Pipeline 내부 실행과정

## 토큰화

다른 신경망과 마찬가지로 raw data를 그대로 사용할 수 없고 모델이 이해할 수 있는 숫자로 변환하는 과정을 거쳐야 한다(**Tokenizer 사용**).

* 입력 정보를 토큰으로 분할
* 토큰을 정수로 매핑
* 부가적인 입력 추가



## transformer의 차원

* 배치 크기 : 한번에 처리되는 시퀀스의 개수
* 시퀀스 길이 : 시퀀스 표현의 길이
* 은닉 크기 : 모델 입력의 벡터차원



## 모델 헤드

모델의 입력 데이터는 다음과 같은 과정을 거친다.

* 임베딩 : 모델이 이해할 수 있는 형태로 임베딩
* 트랜스포머 서브층 : 트랜스포머 내의 어텐션 레이어, add&norm, FFNN 레이어를 거친다
* 모델 헤드 : 출력된 은닉상태를 **다른 차원에 사영**시킨다

![image-20240313153758529](/images/2024-03-13-NLPChapter2/image-20240313153758529.png)

