# DEVIEW 2020 :  (Rule의 역습) NLU에서 머신러닝 기술을 보조/보완할 수 있는 정규표현식 언어 nlu_script

[TOC]

## 음성 대화 시스템과 NLU

- 서비스에서 실용적/현실적인 NLU 문제정의
  - sentence labels 추출 분류 : domatin, intent
  - slots 추출 및 정규화



## Machine Learning VS Rule

### ML 단점

1. 데이터 구축의 어려움
2. 긴 훈력 시간
3. 검증 및 테스트 어려움
4. 특수화 비용 문제
5. Out of domain 문제
6. Wildcard slot 훈련문제

### Rule 단점

1. Wildcard(양날의 검) → wildcard를 사용하지 않으면 low recall, wildcard 사용하면 low precision
2. 유지보수의 어려움
3. No Robustness
4. 중의성 → 해결책 : POS



## Rule 기반 모델 서비스 활용 사례 정리

- 날짜, 시간, 숫자 추출 및 정규화

-  slot을 추출하여 ML-feature로 활용
- Hybrid-NLU
  - rule based NLU를 통해 고빈도 입력을 처리 → high-precision
  - 이후 ml based NLU를 통해 high-recall