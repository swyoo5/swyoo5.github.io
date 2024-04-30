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

# Transformer

## 기존 seq2seq과 비교

트랜스포머는 기존의 seq2seq 모델과 같이 인코더-디코더의 형태를 유지하고 있다. 기존의 seq2seq 모델은 인코더와 디코더에서 총 t개의 recurrent network가 들어가 있었다. 그리고 시계열 데이터를 순차적으로 입력받다보니 **병렬화가 불가능**하여 데이터가 커지면 학습시간이 길어지며 **장기의존성 문제**로 **기울기 소실**이 발생해 멀리 떨어진 항목들간의 연관성은 학습을 하기가 힘들었다. 반면, 이러한 한계를 보완 트랜스포머는 인코더와 디코더가 N개의 레이어로 구성되어있는 구조이다. 트랜스포머를 제안한 논문 "Attention is all you need"에서는 레이어를 6개로 사용한다.



## Recurrent network vs Transformer

이전에 언어모델링, 기계번역과 같은 시퀀스 모델링, 변환 task에서의 SOTA는 RNN, LSTM, GRU였다. 최신의 연구에서 이들은 상당한 성능의 개선이 있었지만 시계열 데이터에서의 연산에서 근본적인 문제(Gradient Vanishing, Long Dependencies)는 여전히 남아있다. 어텐션에서는 시퀀스 모델링과 변환 모델에서 가장 중요한 부분이며 논문에서는 트랜스포머 모델을 제안하며 순환구조를 지양하고 대신 어텐션 메커니즘에 의존한다.



## Transformer의 개요

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

우선 어텐션은 쉽게 말하자면 특정 작업을 수행하기 위해 입력된 모든 정보를 동등하게 참고하는 것이 아닌 작업과 연관성이 높은 특정 정보에만 집중(Attention)하는 기법이다. 많이 사용되는 어텐션은 Additive Attention, dot product attention이다. 이 중 후자의 속도가 더 빠르며 공간효율성이 높다.

![image-20240301165039999](/images/2024-02-25-transformer/image-20240301165039999.png)

여러 블로그들을 살펴보면 위의 그림을 흔하게 볼수 있는데, 이는 어텐션의 구조를 단순화시킨 그림이라고 볼 수 있다. 이를 수식으로 나타내면
$$
Attention(Q,\ K,\ V) = Attention\ Value
$$
이고 어텐션을 간단하게 풀어서 설명하면

* Query(궁금한 정보의 시점 t에서의 hidden state)와 Key(입력정보의 hidden state)의 유사도를 구한다.
* 방금 구한 유사도를 Value에 반영한다.
* 유사도를 반영한 Value를 모두 더해서 리턴한다(Attention Value).

처음 어텐션을 공부할 때, 어텐션은 이해가 갔지만 그림과 무슨 관련이 있는지, Q, K, V가 도대체 무엇인지 알 수가 없었다. 이것에 대해서 뒤에서 천천히 설명해보겠다.

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

  소스 단어 벡터의 차원은 1 x $d_{model}$, 세개의 가중치 행렬 $W$의 차원은 $d_{model}$ x $d_{model} / numheads$이다.

* Scaled dot product

  ![image-20240301172054325](/images/2024-02-25-transformer/image-20240301172054325.png)

  Q와 K 행렬을 내적하면 각 문장의 유사도 행렬을 구할 수 있다. 여기서 Q와 K를 곱한 뒤, padding 토큰의 부분에 매우 작은 음수를 넣어서 소프트맥스 함수를 거치고 나면 0이 되도록 한다(Padding Mask). 여기에 스케일링 상수($\sqrt{d_{k}}$)를 나눠준 뒤, 소프트맥스 함수를 취한 뒤 V행렬을 곱한다. 이렇게 하면 각 단어의 어텐션 값으로 이루어진 어텐션 값 행렬이 나오게 된다.

![image-20240301172440829](/images/2024-02-25-transformer/image-20240301172440829.png)

### Multi-head Self-Attention

![image-20240301172533636](/images/2024-02-25-transformer/image-20240301172533636.png)

멀티헤드 어텐션은 위에서 Q, K, V 행렬을 이용해 어텐션 값을 구한 과정을 여러번에 나눠서 진행하는 방식이다.  즉 $d_{model}$차원의 key, value, query들로 어텐션을 수행하는 대신 $d_{k}, d_{k}, d_{v}$ 차원으로 사영을 시키면 더 효율적이다. 예를 들어, 단어 표현의 차원이 64차원이고 head의 개수가 8개라면, 64/8 = 8 차원의 어텐션 값 행렬 8개를 구한다. 그리고 이 행렬들을 이어붙인다. 그러면 총 64차원의 어텐션값 행렬이 나오게 된다. 

이렇게 한 이유는 아마도 트랜스포머 연구진의 경험적인 결과이겠지만, 단어의 표현에 동일한 가중치 행렬을 곱해 한가지 관점으로만 바라보는것보다는, **모델이 여러 관점에서 단어를 해석**하게 해서 편향되지 않는 모델을 만들기 위함인 것 같다.

![image-20240301173137732](/images/2024-02-25-transformer/image-20240301173137732.png)

> 각기 다른 가중치 행렬을 num_heads개만큼 곱해서 num_heads개 만큼의 어텐션 값 행렬을 만든다.

![image-20240301173614662](/images/2024-02-25-transformer/image-20240301173614662.png)

> 어텐션 값 행렬들을 모두 이어붙인다.

![image-20240301174025175](/images/2024-02-25-transformer/image-20240301174025175.png)

> 이어붙인 어텐션 값 행렬에 새로운 가중치 행렬 Wo를 곱함으로써 선형변환을 해준다.

### Add & Norm

1) 잔차 연결(Residual Connection)

   잔차 연결은 입력 데이터 x와 x에 관한 함수 F(x) 값을 더하는 구조로 잔차 연결을 H(x)라고 하면
   $$
   H(x) = x + F(x)
   $$
   가 된다. 특정 함수와 입력 x를 더해주는 이유가 뭘까?

   우선 잔차 연결에서 함수 F는 **Sublayer**를 의미한다. 잔차연결은 정보 손실의 문제점을 보완한 방식이다. 첫번째로 상위 layer일수록 토큰의 실제 의미는 점점 소실되게 되며 잔차연결은 하위 layer에 존재하는 토큰들의 실제 의미를 잘 전달하는 역할을 한다. 두번째로는 layer가 깊을수록 gradient vanishing이 일어난다. 따라서 잔차연결은 gradient를 상위 layer에서 하위 layer로 전달하는 역할을 한다.

2) 층 정규화(Layer Normalization)

   잔차연결을 거친 뒤 나온 결과는 층 정규화를 거치게 된다. 층 정규화는 말그대로 평균과 분산을 이용해 정규화를 하는 것이다. 우선 잔차연결을 거친 결과값의 각 행들의 평균과 분산을 구한다.
   $$
   \hat{x}_{i,k} = {{x_{i,k}}-\mu_{i}\over{\sqrt{\sigma^{2}+\epsilon}}}
   $$
   

   언제나 그렇듯 분모의 $\epsilon$은 0이 되는것을 방지하는 역할을 한다.

   그 다음, $\gamma$와 $\beta$를 이용해 최종 결과를 만든다.
   $$
   ln_{i} = \gamma\hat{x}_{i}+\beta=LayerNorm(x_{i})
   $$
   

   여기서 $\gamma$와 $\beta$의 초기값은 각각 1벡터와 0벡터이다.

   

### Position-wise FFNN

$$
FFNN(x) = MAX(0,\ xW_{1}+b_{1})W_{2}+b_{2}
$$

여기서 x는 멀티헤드 어텐션의 결과로 나온 (seq_len, $d_{model}$)크기의 행렬을 의미한다.

이 layer는 수식에서 보는것 처럼 두번의 선형결합과 ReLU함수를 통해 이루어져 있으며, ReLU함수를 통해 **비선형성**을 추가해줌으로써 더 정교한 모델을 기대할 수 있다.

## Decoder

인코더와 중복되는 Layer(FFNN, add&norm)는 생략하도록 하겠다.

### Masked Multi-head Self-Attention

Masked Multi-head Self-Attention은 디코더의 첫번째 layer로 **look-ahead mask**가 일어난다. 이게 뭐냐면, 처음에 설명했듯 트랜스포머와 기존의 seq2seq의 입력방식의 차이점은 한꺼번에 입력받냐 아니냐의 차이다. 기존 seq2seq 아키텍처에서는 입력을 순차적으로 받기 때문에 위치정보가 생긴다. 하지만 트랜스포머는 일괄적으로 동시에 입력받기 때문에 현재 시점에서 **미래 시점의 정보를 Cheating**할 수 있게 된다. 따라서 마스킹을 해주게 되는데, 이것이 지금 설명하고 있는 self attention이다.

![img](https://wikidocs.net/images/page/31379/decoder_attention_score_matrix.PNG)

![img](https://wikidocs.net/images/page/31379/%EB%A3%A9%EC%96%B4%ED%97%A4%EB%93%9C%EB%A7%88%EC%8A%A4%ED%81%AC.PNG)

첫번째 그림처럼 Q와 K를 곱하면 유사도 행렬인 Attention score 행렬이 나오게 되는데, 두번째 그림에서처럼 현재시점보다 미래시점의 단어를 모두 마스킹(매우 작은 음수로 설정)하면 softmax 함수를 거쳐 나오면서 0이 되게 된다. 나머지는 인코더에서 설명한 어텐션과 똑같이 작동한다.

### Multi-head Attention

이전의 어텐션과 지금 설명할 어텐션의 차이점은 self인지 아닌지의 차이이다. 이전의 어텐션은 출처가 모두 자기자신(인코더 또는 디코더)에서 나와서 self attention이었고, 지금은 Query의 출처가 디코더, Key의 출처는 인코더이다.

![img](https://wikidocs.net/images/page/31379/%EB%94%94%EC%BD%94%EB%8D%94%EB%91%90%EB%B2%88%EC%A7%B8%EC%84%9C%EB%B8%8C%EC%B8%B5%EC%9D%98%EC%96%B4%ED%85%90%EC%85%98%EC%8A%A4%EC%BD%94%EC%96%B4%ED%96%89%EB%A0%AC_final.PNG)



# Reference

\[1\][블로그1](https://velog.io/@jhbale11/%EC%96%B4%ED%85%90%EC%85%98-%EB%A7%A4%EC%BB%A4%EB%8B%88%EC%A6%98Attention-Mechanism%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)

\[2\][블로그2](https://www.blossominkyung.com/deeplearning/transformer-mha)

\[3\][딥러닝을 활용한 자연어처리 입문 위키독스](https://wikidocs.net/31379)

\[4\][Attention is all you need](https://proceedings.neurips.cc/paper/2017/file/3f5ee243547dee91fbd053c1c4a845aa-Paper.pdf)



