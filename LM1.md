# 언어모델이란

### 1. 언어 모델(Language Model)

언어 모델은 단어 시퀀스에 확률을 할당하는 일을 하는 모델. (즉, **가장 자연스러운 단어 시퀀스를 찾아내는 모델**) 

언어 모델을 만드는 방법 

1. 통계를 이용한 방법

2. 인공 신경망을 이용한 방법

   1. 이전 단어들이 주어졌을 때 다음 단어를 예측

   2. 주어진 양쪽의 단어들로부터 가운데 비어있는 단어를 예측하는 언어 모델 (masked language model)

      

### 2. 단어 시퀀스의 확률 할당의 필요성

1. 기계번역

   $P$(나는 버스를 탔다) $>$ $P$(나는 버스를 태운다) 

   : 언어모델은 두 문장을 비교하여 더 매끄러운 문장으로 번역하는데 이용된다.

2. 오타교정

   선생님이 교실로 부리나케

   $P$(달려갔다) $>$ $P$(잘려갔다)

   :언어모델은 두 문장을 비교하여 더 매끄러운 문장으로 오타 교정하는데 이용된다.

3. 음성인식

   $P$(나는 메론을 먹는다) $> P$(나는 메롱을 먹는다) 

   :언어모델은 두 문장을 비교하여 더 적절한 문장으로 음성인식하는데 이용된다.



### 3. 주어진 이전 단어들로부터 다음 단어 예측하기

1. 단어 시퀀스의 확률
   $$
   P(W) = P(w_1,w_2,w_3,...,w_n)
   $$
   $w$ : 하나의 단어, $W$ : 단어 시퀀스, $n$ : 시퀀스의 단어 총 개수 

2. 다음 단어 등장 확률
   $$
   P(w_n|w_1,w_2,w_3,...,w_{n-1})
   $$
   전체 단어 시퀀스 $W$의 확률은 모든 단어가 예측되고 나서야 알 수 있기에 단어 시퀀스의 확률을 아래 식과 같다.
   $$
   P(W) = P(w_1,w_2,w_3,...,w_n) = \prod_{i=1}^n P(w_n|w_1,w_2,w_3,...w_{n-1})
   $$
   예 : 나는 학교에 간다

   $P$(나는 학교에 간다) = $P$(나는, 학교에, 간다) = $P$(나는) $\times$ $P$(학교에|나는) $\times$ $P$(간다|나는, 학교에)

   - 보통 n-gram 확률 모델을 사용함 

     예 : "나는 학교에 간다"라는 시퀀스를 bi-gram 확률 모델을 적용하여 $P(W)$ 구하기

     $P$(나는 학교에 간다) = $P$(나는, 학교에, 간다) = $P$(나는) $\times$ $P$(학교에|나는) $\times$ $P$(간다|학교에)

     즉, 앞 단어 1개만 보고 전체 단어 시퀀스 등장 확률을 근사한 것이 bigram 확률 모델

     
출처 : https://wikidocs.net/21668