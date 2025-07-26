---
layout: single
title:  "[LLM] Accelerating Large Language Model Decoding with Speculative Sampling"
categories: [Programming, LLM, RAG, Project]
tag: [Programming, LLM, RAG, Project]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

# 개요

- 최근 수십억~수천억개의 파라미터를 가진 LLM들이 등장함에 따라 모델의 텍스트 생성 속도는 중요한 성능 지표
- 대형 언어 모델의 디코딩 속도를 향상시키기 위한 알고리즘 Speculative Sampling(SPS)
- 대형 트랜스포머 모델은 일반적으로 Auto Regressive 방식으로 한 번에 하나의 토큰을 생성
- 병목 요소
  - 메모리 대역폭 제한 : 모든 파라미터가 각 토큰 생성 시마다 GPU를 통해 전달 → 병목
  - 모델 병렬화에 따른 오버헤드 : 대형 모델은 여러 GPU에 분산 → 장치 간 통신 오버헤드
  - 순차적 토큰 생성 : 각 토큰은 이전 토큰에 의존 → 병렬 처리가 어렵고 전체 디코딩 속도 느려짐

# Speculative Sampling

두개의 모델을 사용하여 디코딩 속도 향상

- Draft 모델 : 작고 빠른 모델로 K개의 초안을 생성
- Target 모델 : 정확하지만 느린 모델로 Draft 모델이 생성한 토큰들을 종합 평가

## Step

Step 1 : 초안 생성

- Draft 모델이 현재 입력에 대해 K개의 토큰을 예측

Step 2 : 확률 평가 

- Target 모델이 초안에 대한 점수 평가

Step 3 : 수용/거절 판정

- 평가가 좋은 토큰은 사용, 그렇지 않은 토큰은 다시 Target 모델로 생성

# 결과

- 딥 마인드의 ChinChilla(7B) 모델로 실험 수행
- 2~2.5배 빠른 디코딩
- 생성된 문장의 내용, 일관성, 문법적 완성도 등 품질 면에서 기존 방식과 동등한 수준
- 모델 구조, 파라미터를 변경할 필요 없이 적용 가능 → 실용성 높다