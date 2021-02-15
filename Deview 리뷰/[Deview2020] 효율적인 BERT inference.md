# Deview2020 : 효율적인 BERT inference

[TOC]

## Model Compression

### 작지만 정확한 모델

- 일단 큰 모델을 만들고 작게 줄이기
- 종류
  - Distillation : 큰모델과 유사한 예측을 하도록 학습
  - Pruning : 덜 중요한 부분을 제거한 모델
  - Quantization : 모델 weight나 activation의 비트수를 줄인 모델 (성능은 좀 많이 떨어질 수 있음)

### DistilBERT

- Knowledge Distillation

- 종류
  - 사전 학습에 적용하는 방법
  - 파인튜닝에 적용하는 방법

### DistilBERT외 Distillation된 BERT 모델들

- TinyBERT
- MiniLM
- MobileBERT
- BERT-of-Theseus



## Better Attention Mechanism

### 다양한 X-former들

- BERT 모델을 입력 길이에 따라 속도가 느림
- Self-attention 연산이 입력 시퀀스 길이의 제곱에 비례하기 때문
- Self-attention의 연산 복잡도를 개선하여, Long-term context에도 반영가능하도록 Transformer 모델 변형("X-former")



## Memory Augmentation

### Product Key Memory (PKM)

- feed-forword layer를 PKM으로 대체
- 속도는 많이 느려지지 않으면서 더 좋은 정확도
- 참조 논문
  - End-To-EndMemoryNetworks
  - LargeMemoryLayerswithProductKeys

### PKM을 PLM에 적용

- 메모리를 잘 활용하고 있는지가 중요
  - Catastrophic drift 현상 : 잘 사용되지 않는 memory slot이 생김
    - 해결방법:
      1. 우선 PKM을 적용하지 않고 transformer 부분을 학습시킨 후, 이후 PKM을 이어붙여서 학습시킴
      2. 기존처럼 Feed-forward layer를 메모리로 바꾸는 것이 아니라, residual 방식으로 메모리를 붙임
- BEET-base에 PKM을 추가하여, BERT-Large보다 빠르면서 비슷한 정확도



## Anytime prediction

### 상황에 따라 선택할 수 있는 모델

- 하나의 모델로 어떨 때는 좀 느리더라도 정확히, 어떨때는 좀 틀리더라도 빠르게 사용할 수 있도록

### 모델 구조 선택

- 다른 사이즈의 모델을 하나의 share된 weight로 동시에 학습함
  - 참조논문 : Slimmable Neural Networks (2019)
- 학습 방법이 중요하다며 (sandwich rule과 implace distillation) 방법 제시
  - 참조논문 : Universally Slimmable Networks and Improved Training Techniques (2019)
- width와 depth를 adaptive하게 선택
  - 참조논문 : DynaBERT
- Encoder-Decoder 모델에 다양한 adaptive dimension 
  - Hardward dependent latency budget에 맞게 진화 탐색
  - 참조논문 : HAT

### 중간에 빠져 나가기

- 입력 example에 따라 다른 난이도 → 쉬울수록 많은 layer를 **거치지 않도록**
- 각 레이어별 분류기들을 동시에 학습한 후, inference할때는 예측값을 확신하면 중간에 빠져나가기
- 각 레이어마다 classfier가 있어야 함
- 참조 논문
  - BranchyNet (2017)
  - Adaptive Computation Time for RNN (2016)
- BERT 적용된 모델
  - DeeBERT (2020)
  - The Righ tTool for the Job : Matching Model and Instance Complexities (2020)
  - FastBERT (2020)
  - Depth-AdaptiveTransformer : 문장단위가 아닌 단어 단위로 빠져나감

### 참다가 중간에 빠져나가기

- 연속해서 같은 예측을 하면 빠져나가기
- BERT 적용된 모델
  - BERT Loses Patience : Fast and Robust Inference with Early Exit (2020)

### 레이어 줄이기 (Pruning 방식)

- 학습때는 임의의 레이어를 drop 시키고 (Layer Drop) 나중에는 일부 레이어만을 골라서 사용
- 처음부터 적은 layer를 가진 모델을 학습 시키는 것보다 pruning을 적용시켰을때 좋은 성능을 보임
- 참고자료 
  - Reducing Transformer Depth on Demand with Structured Dropout (2020)
- 이때 레이어를 단순히 랜덤하게 Drop시키는 것이 아닌 좀더 전략을 가지고 Drop 시키는 방법도 존제
  - 참고 자료 : Poor Man's BERT : Smaller and Faster Transformer Models (2019)

### 길이 줄이기 

- 정보 손실의 가능성
- 처음부터 sequence length를 줄이는 것이 아닌, layer를 거쳐나가면서 길이를 줄임
- 사실 입력 길이를 줄인다긴보단, 정보를 압축(pool)한다는 것이 맞을 듯
- 참고자료 
  - Funnel-Transformer (2020)
  - Multi-scale Transformer Language Models (2020)
- 각 transformer layer를 지나면서 얼마나 남길지 길이를 정하는 모델도 있음
  - **Attention 점수 기반으로 덜 중요한 단어 제거**
  - Token lever의 classification 문제는 해결 못함
  - 참고자료 : PoWER-BERT











## 참고자료

- https://tv.naver.com/v/16970751
- https://deview.kr/data/deview/session/attach/1400_T2_%EA%B9%80%EA%B7%9C%EC%99%84_%ED%9A%A8%EC%9C%A8%EC%A0%81%EC%9D%B8_BERT_Inference_.pdf