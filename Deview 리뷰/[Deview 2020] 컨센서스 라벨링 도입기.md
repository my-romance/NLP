# 컨센서스 라벨링 도입기

[TOC]



## 컨센서스 라벨링 (consensus labeling)

### 컨센서스 라벨링이란

- 투표 기반의 정답 도출방식
- 일반적으로 한명당 일정한 작업을 주면, 이 작업의 검수가 필요
  - 작업 수만큼 검수해야함
  - 작업 품질에 따라 수정, 재확인 작업 추가 발생
  - 작업자 수와 검수자 수의 불균형으로 병목발생
- 따라서, 여러명에게 같은 작업을 주고, 이 작업을 검수하는 것이 아니라 비교를 통해 라벨링 → 즉, 투표 기반으로 정답 도출
  - 단, 여러명의 작업자가 필요해지므로 비용이 더 늘어나게됨



## 라벨링 프로젝트 주요 이슈

### 컨센서스 라벨링을 선택한 이유

1 . 간단하게 끝날 양이 아님

2. 현실적으로 운영, 관리에 쏟을 자원이 없음
3. 학습데이터는 정확하길 바랐음

### 라벨링 프로젝트 주요 이슈

- 작업
  - 작업 운영부담
  - 작업자 모집 및 교육
  - 우수 작업자 선별
- 검수
  - 라벨링 품질관리 부담
  - 작업자에서 전환하는 문제
  - 작업 일관성을 보장하는 문제

### 주요 이슈 해결

- 작업

  - 크라우드 소싱으로 해결
  - MS coco dataset 사례
    - 전문가는 precision이 높았던 반면, 일반인은 recall이 높았음
    - 생각보다 많은 일반 작업자가 high precision에 도달
    - 많은 작업이 고수준 작업자에 의해 완료

- 검수

  - 방법 1 : 품질 상위 작업자를 검수자로 전환

    - 상위 10%를 검수자로 전환시 전체 작업량 24% 감소

    - 고수준 작업자 감소로 전체적 품질하락 초래

      - 상위 10% 작업자를 검수자로 전환했기에

    - 많은 수의 검수자로 인한 반복 검수 발생

      - 판단 기준이 제각각이기에, 작업자가 한 작업에 대해 검수가 반복됨

        <img src="/Users/aiden/Library/Application Support/typora-user-images/image-20210217010533245.png" alt="image-20210217010533245" style="zoom:35%;" />

  - 방법 2 : 컨센서스 라벨링

    - 채택됨



## 컨센서스 라벨링 자동화 시스템 개발: SQIP

### 참고사례

- Crowd Truth : 주고 NLP 관련  lebeling 프로젝트에 활용

  <img src="/Users/aiden/Library/Application Support/typora-user-images/image-20210217011018261.png" alt="image-20210217011018261" style="zoom:50%;" />
  - 모호성을 체크하여, 이 작업이 크라우드 소싱으로 해결 가능한지도 판단가능

  -  작업에 대한 vector representation
    <img src="/Users/aiden/Library/Application Support/typora-user-images/image-20210217011247792.png" alt="image-20210217011247792" style="zoom:50%;" />

  - 유사도를 측정하기 위한 metrics

    <img src="/Users/aiden/Library/Application Support/typora-user-images/image-20210217011335410.png" alt="image-20210217011335410" style="zoom:50%;" />

### SQIP

- 목적 : Crowd Truth를 **이미지에 맞추어** 활용해보자. 반자동화
- 요소별 품질 저해요인과 특징
  - 작업 품질 저해요인
    - 모호한 가이드
    - 모호한 선택지
    - 인지하기 어려운 이미지 등
  - 작업자 품질 저해요인
    - 의도적 : spammer, Bot 등
    - 비의도적 : 잘못된 가이드 이해
  - 작업결과의 특징
    - 다수의 선택이 곧 정답
    - 작업 결과로부터 추론 필요

- 데이터의 표현

  - 작업자들의 결과물을 바탕으로 Alpha map을 만듦

  <img src="/Users/aiden/Library/Application Support/typora-user-images/image-20210217011935908.png" alt="image-20210217011935908" style="zoom:50%;" />
  - Alpha map 픽셀마다 값 설정

    <img src="/Users/aiden/Library/Application Support/typora-user-images/image-20210217012122729.png" alt="image-20210217012122729" style="zoom:50%;" />

  - Alpha map 픽셀마다 값 설정 예시

    <img src="/Users/aiden/Library/Application Support/typora-user-images/image-20210217012225349.png" alt="image-20210217012225349" style="zoom:50%;" />

- 지표 정의

  - Alpha Score(AS)

    - 목적 : 작업결과 및 작업자의 **성향 파악(과소표시, 과대표시)**
    - 의미 : 특정 작업결과에 대한 나머지 작업결과들의 일치도

    <img src="/Users/aiden/Library/Application Support/typora-user-images/image-20210217012533598.png" alt="image-20210217012533598" style="zoom:50%;" />

    - Alpha Score를 통해 작업 결과물의 목적(recall, precision)에 따라 작업자 선별가능

  - Task-Worker Similarty(TWS)

    - 목적 : **일반적인 작업경향에서 얼마나 벗어**났는지를 판별
    - 의미 : 선택된 작업자가 다른 작업자들의 결과와 유사한 정도
    - 하지만 정확도에 따른 차이가 적어 직관적인 판단이 어렵
      - 예시로 정말 잘하는 작업자의 score는 0.996, 정말 못하는 작업자의  score는 0.936

  - Alpha Map Score(AMS)

    - 목적 : **TWS보다 더 직관적인** 수치
    - 의미 : 선택된 작업결과와 AlphaMap의 일치도
    - TWS보다 score의 차이가 더 벌어짐

  - Image Clarity Score(ICS)

    - 목적 : 작업의 모호함이나 어려운 정도를 판별 ← 어렵고 모호할수록 작업자 간 이견 커짐
    - 의미 : 작업결과들의 차이 정도
    - ICS를 통해 어려운 데이터는 수준 높은 작업자에게 주거나, 담당자가 직접 하거나 할 수 있음

- 영향력 차등화

  - spammer 또는 bot으로 인해 정답이 아닌 값이 다수결 선택으로 채택될수 있음. 즉 오답이 정답이 됨
  - spammer 등을 사전 차단하기 위한 튜토리얼 작업 준비
    - 어려울수록 가점 부여

- 불량작업 제거 로직

  - ICS와 TWS의 기준 score을 정하고, 이 기준 score에 부합하지 못한 작업은 다시 작업하도록 함
  - 특이값 저게를 위한 허용범위 설정

- Image Matting

  - Dense-CRF를 활용한 Ground Truth 추론



## 적용 사례

### Instance Segmentaion

- 작업 1개당 7~14명 할당

### Image Classification

- 이진 분류 문제
- 작업 1개당 2명에게 할당 후, 불일치시 4명에게 다시 할당..

### Scene Text Location



## 정리

- 장점
  - 예상보다 적은 비용 : 튜토리얼 도입 후 1작업당 3명 할당까지 가능
  - 운영소요 경감 : 검수 작업 90%이상 감소

- 단점
  - 각 라벨링 프로젝트별로 대양해야 할 다양한 도메인 특성 존재
  - 평가 로직 개발에 생각보다 많은 시간 소요

- 컨센서스 라벨링 사용여부 결정 기준 
  - 라벨링 **작업 종류가 얼마나 많은지**
  - 라벨링 작업이 얼마나 **복잡**한지

  <img src="/Users/aiden/Library/Application Support/typora-user-images/image-20210217020413473.png" alt="image-20210217020413473" style="zoom:50%;" />

  

## 참고자료

- [발표자료](https://deview.kr/data/deview/session/attach/DEVIEW2020_%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EB%9D%BC%EB%B2%A8%EB%A7%81%20%EB%84%88%EB%AC%B4%20%EA%B7%80%EC%B0%AE%EC%95%84%EC%9A%94_%EC%8B%A0%EC%9C%A4%EC%8B%9D.pdf)
- [발표영상](https://tv.naver.com/v/16969174)

