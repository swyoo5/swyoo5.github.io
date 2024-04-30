---
layout: single
title:  "4/12 HuggingFace AutoTrain 파라미터 정리"
categories: [Programming, huggingface, autotrain]
tag: [Programming, huggingface, autotrain]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

 AI hub에서 다운로드 받은 [한국어-영어 번역 말뭉치](https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=126)를 기반으로 한국어를 영어로 번역하는 task를 수행하도록 파인튜닝하는 도중에 허깅페이스의 autotrain의 파라미터들이 많아서 정리하려고 한다. 허깅페이스에서 제공하는 설명은 [깃허브](https://github.com/huggingface/autotrain-advanced/blob/main/src/autotrain/cli/run_llm.py#L17)에 올라와있다.

# autotrain 

* --train : 모델 훈련
* --inference : 추론 수행
* --model : 사용할 모델
* --data-path : 사용할 훈련 데이터
* --text-column : 사용할 열(column)
* --peft : peft(Parameter Efficient Fine Tuning) 사용
* --quantization : 가중치와 활성화를 int8과 같은 낮은 정밀도의 데이터 타입을 사용함으로써 계산, 메모리 비용을 줄이는 기법이다. ["int4", "int8", "None"]
* --trainer : 사용할 trainer 타입(sft(Supervised Fine Tuning))