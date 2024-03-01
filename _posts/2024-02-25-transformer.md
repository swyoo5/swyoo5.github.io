---
layout: single
title:  "2/25 Transformer"
categories: [Programming, transformer, nlp]
tag: [Programming, transformer, nlp]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

[블로그1](https://velog.io/@jhbale11/%EC%96%B4%ED%85%90%EC%85%98-%EB%A7%A4%EC%BB%A4%EB%8B%88%EC%A6%98Attention-Mechanism%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)

[딥러닝을 활용한 자연어처리 입문 위키독스](https://wikidocs.net/31379)

[Attention is all you need](https://proceedings.neurips.cc/paper/2017/file/3f5ee243547dee91fbd053c1c4a845aa-Paper.pdf)

[블로그2](https://www.blossominkyung.com/deeplearning/transformer-mha)

# Transformer

## 기존 seq2seq과 비교

트랜스포머는 기존의 seq2seq 모델과 같이 인코더-디코더의 형태를 유지하고 있다. 기존의 seq2seq 모델은 인코더와 디코더에서 총 t개의 recurrent network가 들어가 있었다. 그리고 시계열 데이터를 순차적으로 입력받다보니 **병렬화가 불가능**하여 데이터가 커지면 학습시간이 길어지며 **장기의존성 문제**로 **기울기 소실**이 발생해 멀리 떨어진 항목들간의 연관성은 학습을 하기가 힘들었다. 반면, 이러한 한계를 보완 트랜스포머는 인코더와 디코더가 N개의 레이어로 구성되어있는 구조이다. 트랜스포머를 제안한 논문 "Attention is all you need"에서는 레이어를 6개로 사용한다.



## Recurrent network vs Transformer

이전에 언어모델링, 기계번역과 같은 시퀀스 모델링, 변환 task에서의 SOTA는 RNN, LSTM, GRU였다. 최신의 연구에서 이들은 상당한 성능의 개선이 있었지만 시계열 데이터에서의 연산에서 근본적인 문제(Gradient Vanishing, Long Dependencies)는 여전히 남아있다. 어텐션에서는 시퀀스 모델링과 변환 모델에서 가장 중요한 부분이며 논문에서는 트랜스포머 모델을 제안하며 순환구조를 지양하고 대신 어텐션 메커니즘에 의존한다.



## Transformer의 전체적인 구조

![트랜스포머1](/images/2024-02-25-transformer/트랜스포머1.jpg)

트랜스포머의 전체적인 구조는 위의 그림과 같다.

* 데이터 임베딩
* 포지셔널 인코딩
* 인코더
  * Multi-head Selt-Attention
  * Position-wise FFNN
  * Add & Norm
* 디코더
  * Masked Multi-head Self-Attention
  * Multi-head Attention
  * Position-wise FFNN
  * Add & Norm

## Positional Encoding

Recurrent Neural Network같은 경우, 시계열 데이터를 순차적으로 입력받다보니 데이터에 대한 위치정보가 자연스럽게 제공되었다. 하지만 데이터를 병렬적으로 연산을 하는 트랜스포머의 경우, 모든 데이터가 동시에 입력되기 때문에 **위치정보를 갖지 못한다**. 문장으로 예를 들면, "I am a cat"이라는 문장과 "cat I a am"이라는 두 문장이 트랜스포머는 같은 문장으로 인식한다는 것이다. 이를 방지하기 위해 Positional Encoding이라는 방법을 사용한다.

![트랜스포머2](/images/2024-02-25-transformer/트랜스포머2.jpg)

위의 그림과 같이 입력데이터를 임베딩한 임베딩벡터에 위치정보를 표현한 포지셔널 인코딩 벡터를 더해준다. 여기서 포지셔널 인코딩 벡터는 어떻게 위치정보를 반영하냐면 sin함수와 cos함수를 이용한다.
$$
PE_{(pos, 2i)} = sin(pos/10000^{2i/d_{model}})\\
PE_{(pos, 2i + 1)} = cos(pos/10000^{2i/d_{model}})
$$
pos와 i는 임베딩 행렬에서 행과 열을 의미한다. 즉, pos는 입력 문장에서의 임베딩 벡터의 위치, i는 임베딩 내에서 차원의 인덱스를 의미한다.

## Attention

우선 어텐션은 쉽게 말하자면 특정 작업을 수행하기 위해 입력된 모든 정보를 동등하게 참고하는 것이 아닌 작업과 연관성이 높은 특정 정보에만 집중(Attention)하는 기법이다. 

![image-20240301165039999](/images/2024-02-25-transformer/image-20240301165039999.png)

여러 블로그들을 살펴보면 위의 그림을 흔하게 볼수 있는데, 이는 어텐션의 구조를 단순화시킨 그림이라고 볼 수 있다. 이를 수식으로 나타내면
$$
Attention(Q,\ K,\ V) = Attention\ Value
$$
이고 어텐션을 간단하게 풀어서 설명하면

* Query(궁금한 정보의 시점 t에서의 hidden state)와 Key(입력정보의 hidden state)의 유사도를 구한다.
* 방금 구한 유사도를 Value에 반영한다.
* 유사도를 반영한 Value를 모두 더해서 리턴한다(Attention Value).

처음 어텐션을 공부할 때, 어텐션은 이해가 갔지만 그림과 무슨 관련이 있는지, Q, K, V가 도대체 무엇인지 알 수가 없었다. 뒤에서 내가 이해한대로 천천히 설명해보겠다.

### Query, Key, Value

그렇다면 Attention의 Query, Key, Value란 무엇일까?

![image-20240301165929496](/images/2024-02-25-transformer/image-20240301165929496.png)

위의 그림(기계번역)을 예로 들어서 설명을 하겠다.

* Query
  * 유추해야 하는 시점의 디코더의 hidden state
  * 쉽게 말해서 내가 지금 궁금한 정보의 시점에서 나온 hidden state라고 할 수 있겠다.
  * 그림에서 디코더의 3번째 시점에서 나온 hidden state
* Key
  * 인코더의 input에 대해서 각 셀에서 출력된 hidden state
  * 소스 문장에 대 hidden state, 입력에 관한 정보라고 볼 수 있겠다.
  * 그림에서 인코더 부분에서 입력이 LSTM에서 나온 hidden state(초록색 동그라미)
* Value
  * Query와 Key의 유사도를 반영한 정보이므로 Softmax함수의 출력으로 볼 수 있겠다.

### Transformer Attention

트랜스포머에서 사용되는 어텐션의 종류는 세가지이다. Self-Attention은 말그대로 self, 즉 Q, K, V의 출처가 모두 동일한  어텐션을 의미한다(벡터의 값이 같은것이 아님).

* Encoder self-attention

  ![image-20240227225400750](/images/2024-02-25-transformer/image-20240227225400750.png)

* Masked Decoder self-attention

  ![image-20240227225410132](/images/2024-02-25-transformer/image-20240227225410132.png)

* Encoder-Decoder attention

  ![image-20240227225417439](/images/2024-02-25-transformer/image-20240227225417439.png)

## Encoder

* Q, K, V 행렬 얻기

  ![image-20240301170847028](/images/2024-02-25-transformer/image-20240301170847028.png)

  우선 소스 문장의 단어들을 행렬로 만들어 병렬연산을 한다. 소스 단어의 행렬을 각각 세개의 다른 학습한 가중치들로 이루어진 행렬을 곱해 Q, K, V벡터로 변환을 한다(차원 줄이기).

  소스 단어 벡터의 차원은 1 x $\d_{model}$, 세개의 가중치 행렬 $\W$의 차원은 $\d_{model}$ x $\d_{model} / numheads$이다.

* Scaled dot product

  ![image-20240301172054325](/images/2024-02-25-transformer/image-20240301172054325.png)

  Q와 K 행렬을 내적하면 각 문장의 유사도 행렬을 구할 수 있다. 여기에 스케일링 상수($\sqrt{d_{k}}$)를 나눠준 뒤, 소프트맥스 함수를 취한 뒤 V행렬을 곱한다. 이렇게 하면 각 단어의 어텐션 값으로 이루어진 어텐션 값 행렬이 나오게 된다.

![image-20240301172440829](/images/2024-02-25-transformer/image-20240301172440829.png)

### Multi-head Self-Attention

### Add & Norm

### Position-wise FFNN

## Decoder

### Masked Multi-head Self-Attention

### Multi-head Attention

