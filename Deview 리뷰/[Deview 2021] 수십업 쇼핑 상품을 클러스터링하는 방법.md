[TOC]

### 1. 네이버 Search CIC's 쇼핑기술

- 핵심기술
  - 검색
  - 추천



### 2. 상품 클러스터링

- 가능한 서비스

  - 상품 가격 비교
  - 중복 상품제거

  ⇒ 사용자 쇼핑 경험 만족도 ↑

-  But, 어려운점
  - 정보 (제품속성, 특징) 부족
  - 정보 과잉 ⇒ 어떤 상품을 판매하고 있는건지 파악하기 어려움
  - 상품 대표이미지가 없는 경우
  - 저품질 텍스트, 이미지
  - 이미지와 상품명이 다름



### 3. 상품 정보 분석

상품명, 이미지를 통해 상품 정보를 정제하는 것만으로는 해당 TASK를 풀기 부족 ⇒ 상품 정보를 추출하고자 함

- 상품의 가격에 영향을 미치는 '구매 조건' 추출 ⇒ 즉, 단위당 가격 등의 정보들을 추출 (567 종류의 자동속성추출) ⇒ Bi-LSTM + CRF 모델 사용
- 브랜드, 상품 코드 추출 ⇒ transformers XLM-R token classification 모델 사용
- 상품 색상 추출 (from 이미지)



### 4. 클러스터링과 임베딩

- classification VS Clustering : 날마다 신규 상품들이 등록되기에 같은 상품을 묶기위해선 classification보다 clustering이 효율적
- 상품 정보를 벡터 공간에 임베딩 ⇒ sentence Bert 사용 + XLM-R (cross-lingual)



### 5. 대표적인 클러스터링 기법

- K-means Clustering : 클러스터 개수 k에 따라 성능이 좌지우지 됨 (k를 잘 설정하지 않으면, 잘못된 영역을 centroid로 판단하게 됨)

- DBSCAN Clustering : K-means Clustering과 달리 클러스터 갯수를 미리 설정할 필요 X. 작은 클러스터를 생성한 뒤, 근처 데이터를 확인하며 클러스터를 확장해나감.

- 하지만 두 클러스터링 방법은 대규모 데이터에는 적합하지 않음 

  - 이유 1. 알고리즘 자체가 분산처리하기 어려움

  - 이유 2. 시간 복잡도
    - K-means Clustering : O(nkdi) ⇒ linear polynomial.  n : 데이터 개수. k : 그룹개수. d : 데이터 차원. i : centroid 이동 횟수.
    - DBSCAN Clustering : O(n^2) ⇒ quadratic polynomial. 
    - 클러스터링 알고리즘 시간 복잡도 상황 : 상품수는 너무나 빠르게 증가하고, 클러스터 수는 상품 수에 비례. 즉 k가 n에 비례하므로, K-means Clustering 또한 O(n^2)에 근접



### 6. 대규모 병렬 클러스터링

Hadoop과 spark 형태의 분산처리 클러스터링 시스템 구축

- 병렬 클러스터링 시스템 구조

  <img src="./images/deview2203171.png" style="zoom: 43%;" />

  - 모든 상품은 자신을 대표하는 key를 가짐. → 같은 키를 가진 상품끼리 묶고, 묶여진 그룹내에서 클러스터링을 실행 ⇒ 키를 잘 선택하는 것이 중요

- 한 키로 묶여진 그룹에서 정밀도를 높이기 위해 여러 속성을 이용하여 상품 매칭. 즉 한 모델이 만든 상품 feature 임베딩으로 동일 상품인지 판별하는 것이 아니라, 다양한 속성들을 이용해 이를 판별. ⇒ Gradient Boosting Decision Tree(앙상블 방식)를 이용. 이 중, XGBoosting 알고리즘 사용.

  <img src="/Users/aiden/Documents/sangsang/NLP/Deview 리뷰/images/deview2203172.png" style="zoom:33%;" />

- Data Skewness : 한쪽 key에 여러 상품들이 분류되어 Data가 한쪽 그룹으로 치우친 것을 말함. 

  <img src="./images/deview2203173.png" style="zoom:40%;" />

  - 이를 해결하기 위해 Skewness가 발생한 키를 찾아, 이를 물리적으로 나누어 다른 키를 가지도록 만듦
  - 또한 대규모 데이터에서 count-min Sketch 알고리즘(확률적 자료구조)를 통해 작은 메모리를 가지고, 해당 key의 상품 갯수를 파악

- 하지만 동일 상품임에도 불구하고, 다른 클러스터로 나뉠 수 있음

  ⇒ 공통된 key를 가지고, 공통된 이미지 feature를 가진다면 묶어 이를 완화 (But, 의류 카테고리에서는 잘 안됨)

  ⇒ 이때 이미지 feature를 key로 만들어, 이 key가 같은지 아닌지 확인함. key를 만들기 위해 PCA/Deep hash를 사용한 binarization 기법 활용

- 위 방식들을 통해 7단계 클러스터링 진행

  <img src="./images/deview2203175.png" style="zoom:40%;" />



### 7. 대형 클러스터 병합 전략

- 대형 클러스터간 머지 : 분리된 클러스터들 중 같은 상품을 가지는 클러스터는 묶여야함

  <img src="./images/deview2203174.png" style="zoom:40%;" />

  - Single linkage, complete linkage, centroid linkage는 연산량이 작지만 신뢰성이 비교적 높지 않음
  - average linkage는 연산량은 많지만, 신뢰성이 비교적 높음
  - 하지만, 이방법들을 쓰더라도 비교해야할 속성이 여러개이기에 시간이 오래 걸림. 총 비교 횟수가 각 클러스터 상품 간 비교 X 클러스터내 속성 횟수가 된다.

- 좀 더 간단하게 여러속성에 대해 두 클러스터를 비교하길 원함

  - 우선 클러스터 내 동일한 속성을 하나로 묶어 비교할 데이터 수를 줄임

    <img src="./images/deview2203176.png" style="zoom:40%;" />

  - 동일한 속성을 하나로 묶었으므로, 각 속성을 유일한 원소로 표현 가능. 이때 유일한 원소 갯수를 **Cardinality**라고 하자.

  - 클러스터 유사도를 아래의 식으로 표현 가능. 이 값이 크면 클수록 중복된 값이 많은것으로, 즉 유사도가 크다는 의미

    <img src="./images/deview2203177.png" style="zoom:40%;" />

  - 이 때 데이터를 보지 않고 Cardinality만 알 수 있을까? → 이를 해결하면 클러스터 비교가 간단해지고, 일일이 속성 정보를 가지고 있지 않아도 됨

  - ⇒ 해결책으로 HyperLogLog를 사용. 아주 적은 양의 메모리를 사용하여, Cardinality을 추정하는 확률적 자료구조. (오차존재)

    <img src="./images/deview2203178.png" style="zoom:40%;" />

    <img src="./images/deview2203179.png" style="zoom:40%;" />

### 8. 오차의 전파

<img src="./images/deview22031710.png" style="zoom:40%;" />

### 최종정리

<img src="./images/deview22031711.png" style="zoom:40%;" />



### 참고자료

- https://tv.naver.com/v/23652701