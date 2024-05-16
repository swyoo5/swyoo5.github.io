---
layout: single
title:  "11/9 Tensorflow 함수"
categories: [Programming, python, with, tensorflow]
tag: [Programming, python, tensorflow]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

* tf.reduce_mean() .

  ```python
  import tensorflow as tf
  
  x = tf.constant([[1, 2, 3],
                   [4, 5, 6]])
  
  mean_all = tf.reduce_mean(x)
  mean_col = tf.reduce_mean(x, axis = 0)
  mean_row = 
  tf.reduce_mean(x, axis = 1)
  
  print(mean_all.numpy())
  print(mean_col.numpy())
  print(mean_row.numpy())
  ```

  실행 결과

  ```python
  # 3
  # [2 3 4]
  # [2 5]
  ```

  

* tf.Variable()

  텐서플로우의 변수를 생성한다.

  ```python
  # w을 텐서플로우의 변수 2.0으로 초기화한다.
  w = tf.Variable(2.)
  ```

  

* tf.GradientTape()

  Tensorflow에서 제공하는 컨텍스트 매니저이다. 이 컨텍스트 안에서 수행되는 모든 연산을 기록하고 그래디언트를 계산할 수 있게 해준다.

  ```python
  import tensorflow as tf
  
  w = tf.Variable(2.) # tensorflow 변수 생성, w = 2.0으로 초기화
  
  def f(w) :
      y = w ** 2
      z = 2 * y + 5 # 2 * w^2 + 5
      return z
  
  with tf.GradientTape() as tape :
      z = f(w)
      
  gradients = tape.gradient(z, [w])
  ```

* @tf.function

  - tf 1.x 버전 : 그래프의 생성과 실행을 분리, 값을 실행할 때는 Session을 열어서 값을 실행하는 형태였다(분리한 이유는 성능상의 이점때문).

  - tf 2.x 버전 : Session을 삭제하고 바로 값을 실행할 수 있는 Eager Execution이 적용됨. 값을 계산할 때, Session을 열지 않고 편리하게 진행.
  - def 위에 @tf.function을 붙이면 2.x 버전에서도 1.x 버전에서처럼 그래프 생성과 실행이 분리된 형태로 함수 내의 로직이 실행되게 된다.
  - 하지만, 해당 annotation을 붙이면 1.x 버전에서처럼 해당 함수내의 값을 계산해 볼 수 없어서 디버깅이 불편해질 수 있다. 따라서 모든 로직에 대한 프로그래밍이 끝난 상태에서 붙이는 것이 좋다.

* apply_gradients()

  최적화 프로세스에 사용되는 텐서플로우의 함수 중 하나이다. 최적화된 그래디언트를 사용하여 모델의 변수를 업데이트하는데 사용된다.

  ```python
  optimizer.apply_gradients(zip(gradients, [w, b]))
  ```

  apply_gradients 함수는 실제로 변수를 업데이트하는 작업을 수행한다.

* Sequential()

  Sequential()은 텐서플로우에서 신경망 모델을 구축하기 위한 모듈 중 하나이다. Sequential은 순차적으로 레이어를 쌓아 간단한 모델을 만들 때 사용된다.

  ```python
  from tensorflow.keras.models import Sequential
  from tensorflow.keras.layers import Dense
  
  model = Sequential()
  
  # Dense : 완전연결 layer
  model.add(Dense(64, activation = "relu", input_dim = (100,)))
  model.add(Dropout(0.5))
  model.add(Dense(10, activation = "softmax"))
  
  model.compile(optimizer = "adam",
                loss = "categorical_crossentropy",
                metrics = ["accuracy"])
  ```

  