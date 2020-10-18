### 백오프 및 스무딩의 목적

n-gram 언어 모델의 문제는 어떤 시퀀스가 코퍼스에서 한번도 등장하지 않았다면 이 n-gram 언어모델은 이 시퀀스에 대한 확률로 0을 반환

ex : 학습데이터에 `아이는` 다음에 `또바기` 라는 단어가 한 번도 등장하지 않았다면 이 언어 모델은 예측 단계에서 `그 아이는 또바기 인사를 잘한다`는 자연스런 한국어 문장이 등장할 확률을 0으로 부여하게 됨.

이 문제를 완화하기 위한 방법이 **백오프**와 **스무딩**



### 백오프 (back-off)

- interporpation

  - **다른 Language Model을 linear하게 일정비율($\lambda$)로 섞는 것.**
  - EX : general domain LM + domain specific LM = general domain에서 잘 동작하는 domain adapted LM

  - 
    $$
    \hat{P}(w_n|w_{n-k},...,w_{n-1}) = \lambda{P_1}(w_n|w_{n-k},...,w_{n-1}) +(1-\lambda){P_2}(w_n|w_{n-k},...,w_{n-1})
    $$

- Back-off

  - **N-gram 등장 빈도를 n보다 작은 범위의 단어 시퀀스 빈도로 근사하는 방식.** $n$을 크게 하면 할수록 등장하지 않는 케이스가 많아질 가능성이 높기 때문. UNK work가 없다면 확률이 0이 되진 않음.

  - $$
    \hat{P}(w_n|w_{n-k},...,w_{n-1}) = \lambda_1P(w_n|w_{n-k},...,w_{n-1}) \\
     + \lambda_2P(w_n|w_{n-k+1},...,w_{n-1})  \\ 
     +\ ...\  \\
     + \lambda_kP(w_n) \\ \\
    $$

    $$
    where \sum_i{\lambda_i} = 1
    $$

  - **하지만**, unseen sequence를 위해 back-off를 거치는 순간 확률 값이 매우 낮아져 버림



### 스무딩 (smoothing)

- **단어 등장 빈도수에 모두 k만큼 더하는 기법.** 이 때문에 Add-k 스무딩리라고 부르기도 함
  - 만약 $k$를 1로 설정한다면 이를 특별히 라플라스 스무딩이라고 함
- 스무딩을 시행하면 높은 빈도를 가진 문자열 등장 확률을 일부 깎고, 학습데이터에 전혀 등자하지 않은 케이스들에는 작게나마 일부확률을 부여

- Ex : `내 마음 속에 영원히 기억될 최고의 명작이다.`의 빈도수는 $k (=0+k)$.

- $$
  P(w_t|w_{<t}) \approx \frac{C(w_{1:t})+k}{C(w_{1:t-1})+k\times|V|} \\
  \approx \frac{C(w_{1:t})+\frac{m}{|V|}}{C(w_{1:t-1})+m} \\
  $$

- take more generation : 
  $$
  P(w_t|w_{<t}) \approx \frac{C(w_{1:t})+m\times P(w_t)}{C(w_{1:t-1})+m} \\
  where\ P(w_t)is unigram probability.
  $$
  

참고자료

- 한국어 임베딩
- 김기현의 자연어처리 강의