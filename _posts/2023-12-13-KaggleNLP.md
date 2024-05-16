---
layout: single
title:  "12/13 Getting started with NLP for absolute beginners"
categories: [Programming, python, NLP, Kaggle]
tag: [Programming, python, NLP, Kaggle]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

NLP 관련 도서로 이론 공부를 하다가 프로젝트로 어떻게 넘어가야하는지 고민하는 중에 많이들 Kaggle 사이트로 감을 잡는게 좋다고 해서 사이트를 보는데 Learn 카테고리에서 Natural Language Processing Guide 코스가 있는걸 보고 하나씩 해보려고 한다. .

[NLP Guide](https://www.kaggle.com/learn-guide/natural-language-processing)

[Getting started with NLP for absolute beginners](https://www.kaggle.com/code/jhoward/getting-started-with-nlp-for-absolute-beginners)



# Introduction

지난 2년간 극적으로 발전한 딥러닝의 한 분야는 자연어처리(NLP)이다. 컴퓨터는 텍스트를 생성할 수 있고 한 언어를 다른 언어로 자동적으로 번역할 수 있고, 댓글들을 분석할 수 있고, 문장의 단어들을 라벨링 그리고 더 많은 일들을 할 수 있다.



아마 가장 광범위하고 실질적으로 유용한 NLP의 응용은 분류이다. 이는 문서를 자동으로 어떤 카테고리로 분류한다. 예를들어 다음과 같이 사용될 수 있다.

* 감정 분류(예를 들어 당신의 물건에 대해서 긍정 혹은 부정적인 말을 한다.)
* 저자 확인(어떤 저자가 특정 문서를 쓸것 같은지)
* 법적 증거자료(어떤 문서가 법정에서 적합한지)
* 주제에 따른 문서 조직화
* 이메일 분류

분류 모델은 처음에는 자명하지 않은 문제를 해결하는데 사용될 수 있다. 예를 들어, Kaggle U.S. Patent Phrase to Phrase Matching competition을 생각하라. 여기서 두개의 단어 또는 짧은 문장을 비교하고 그들이 유사한지 여부에 따라 점수를 메기고 어떤 patent class에 사용되었는지에 따라 점수를 메긴다. 점수 1은 두개의 입력이 동등한 의미를 가지는 것으로 간주되고 0은 완전히 다른 의미를 가진다고 간주된다. 예를 들어 **abatement**와 **eliminating process**는 점수 0.5를 가지고 이는 비슷하지만 같은 의미는 아니라는 뜻이다.



이는 분류 문제로 표현될 수 있음이 밝혀졌다. 어떻게? 다음과 같은 질문을 표현함으로써 :

> "TEXT1 : abatement; TEXT2 : eliminating process"
>
> 위의 텍스트를 의미 유사도에 따른 카테고리를 골라라 : "다름; 유사; 동일".

이 notebook에서 Patent Phrase Matching을 분류 작업으로 처리하고 위에 보여지는 것과 매우 유사한 방식으로 표현하여 문제를 해결하는 방법을 살펴보겠다.



## On Kaggle

Kaggle은 열망하는 데이터 사이언스나 머신러닝 스킬을 향상시키고 싶은 어느 누구에게나 경이로운 리소스이다.  당신의 기술을 향상시키는데 직접 실습하고 실시간 피드백을 받는것만큼 좋은 것이 없다. Kaggle은 다음을 제공한다.

1. 흥미로운 데이터셋
2. 당신이 하는 것에 대한 피드백
3. 무엇이 좋고 무엇이 가능하며 무엇이 최신의 기술인지를 알려주는 리더보드
4. 우승자의 노트북과 블로그가 유용한 팁과 기술들을 공유한다.

여기서 사용하는 데이터셋은 Kaggle에서만 이용가능하다. 따라서 사이트에 등록하고 competition 페이지로 가라.



***읽다보니 Kaggle에 대한 전반적인 설명들이 앞에 있어서 그냥 읽고 넘어가겠다.***





