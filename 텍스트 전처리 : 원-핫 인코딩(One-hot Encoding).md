# 텍스트 전처리 : 원-핫 인코딩(One-hot Encoding)

원-핫 인코딩 : 각 단어에 고유한 정수 인덱스를 부여한 뒤, (정수인코딩을 통해) **숫자로 바뀐 단어들을 벡터로 표현**할 때 사용하는 **가장 기본적인 표현 방법**.

원-핫의 의미 : **하나만 불이 들어온다.** 하나의 값만 True이고 나머지는 모두 False.



### 원-핫 인코딩(One-hot Encoding)

- 원-핫 인코딩 순서

  1. 단어 집합의 크기를 벡터의 차원으로 설정 및 정수 인코딩(각 단어에 고유한 인덱스 부여)
  2. 표현하고 싶은 단어의 인덱스에는 1의 값을 부여
  3. 나머지 인덱스에는 0의 값을 부여

- 원-핫 인코딩 example

  ```python
  from konlpy.tag import Okt  
  okt=Okt()  
  token=okt.morphs("나는 자연어 처리를 배운다") # konlpy의 Okt 형태소 분석기를 이용한 문장 토큰화
  print(token)
  ```

  ```python
  ['나', '는', '자연어', '처리', '를', '배운다'] 
  ```

  ```python
  # vocab 구축 → 간단한 실습을 위해 빈도수 순대로 정렬 X
  word2index={}
  for voca in token:
       if voca not in word2index.keys():
         word2index[voca]=len(word2index)
  print(word2index)
  ```

  ```python
  {'나': 0, '는': 1, '자연어': 2, '처리': 3, '를': 4, '배운다': 5} 
  ```

  ```python
  def one_hot_encoding(word, word2index):
         one_hot_vector = [0]*(len(word2index))
         index=word2index[word]
         one_hot_vector[index]=1
         return one_hot_vector
  ```

  ```python
  one_hot_encoding("자연어",word2index)
  ```

  ```python
  [0, 0, 1, 0, 0, 0] 
  ```

  "자연어" token에 해당하는 고유 인덱스 2의 값만 1이고 나머지 인덱스의 값은 0인 one-hot vector를 리턴



### 케라스(Keras)를 이용한 원-핫 인코딩(One-hot Encoding)

케라스는 원-핫 인코딩을 수행하는 to_categorical()를 지원.

```python
text="나랑 점심 먹으러 갈래 점심 메뉴는 햄버거 갈래 갈래 햄버거 최고야"
```

```python
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.utils import to_categorical

text="나랑 점심 먹으러 갈래 점심 메뉴는 햄버거 갈래 갈래 햄버거 최고야"

t = Tokenizer()
t.fit_on_texts([text])
print(t.word_index) # 각 단어에 대한 인코딩 결과 출력.
```

```python
{'갈래': 1, '점심': 2, '햄버거': 3, '나랑': 4, '먹으러': 5, '메뉴는': 6, '최고야': 7}
```

```python
sub_text="점심 먹으러 갈래 메뉴는 햄버거 최고야"
encoded=t.texts_to_sequences([sub_text])[0]
print(encoded)
```

```python
[2, 5, 1, 6, 3, 7]
```

```python
one_hot = to_categorical(encoded)
print(one_hot)
```

```python
[[0. 0. 1. 0. 0. 0. 0. 0.] #인덱스 2의 원-핫 벡터
 [0. 0. 0. 0. 0. 1. 0. 0.] #인덱스 5의 원-핫 벡터
 [0. 1. 0. 0. 0. 0. 0. 0.] #인덱스 1의 원-핫 벡터
 [0. 0. 0. 0. 0. 0. 1. 0.] #인덱스 6의 원-핫 벡터
 [0. 0. 0. 1. 0. 0. 0. 0.] #인덱스 3의 원-핫 벡터
 [0. 0. 0. 0. 0. 0. 0. 1.]] #인덱스 7의 원-핫 벡터
```





### 원-핫 인코딩(One-hot Encoding)의 한계

- **원-핫 인코딩의 단점**

  1. 단어의 개수가 늘어날수록, 벡터를 저장하기 위한 메모리 공간 필요 

  2. sparse vector : 비효율적
  3. 단어의 유사도를 구할 수 없음 → 검색 시스템에서의 심각한 문제
     house와 home의 유사도가 0이 됨

- **원-핫 인코딩의 단점을 해결하는 다차원 공간에 벡터화**

  - 카운트 기반(어떤 단어가 많이 쓰였는가)의 벡터화 방법 : LSA, HAL
  - 예측 기반(단어가 어떤 순서로 쓰였는가, 어떤 단어가 같이 쓰였는가)의 벡터화 : NNLM, RNNLM, Word2Vec, FastText
  - 카운트 기반과 예측 기반의 벡터화 : GloVe



### 출처

- https://wikidocs.net/22592
