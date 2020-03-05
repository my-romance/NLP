# 텍스트 전처리 : 정수 인코딩(Integer Encoding)

컴퓨터는 텍스트보다 숫자를 더 잘 처리할 수 있음 → **텍스트를 숫자로 바꾸는 작업 필요.**

이를 위해선 각 **token을 고유한 정수에 맵핑(mapping)**시키는 전처리 작업이 필요. (사전을 구축하고 각 token에 index를 부여. 이때 사전 구축과 index 부여 기준은 보통 corpus에서 출현 빈도가 높은 token 순) 



### 정수 인코딩 (Integer Encoding)

- token에 정수를 부여(정수 인코딩)하는 순서

  1. token을 빈도수 순으로 정렬한 단어 집합(vocabulary) 구축 
  2. 빈도수가 높은 순서대로 차례로 정수를 부여
  3. 각 token을 부여받은 정수로 mapping

- 코딩 방법:  

  1. dictionary 사용하기
  2. **Counter 사용하기** (이것만 다룸)
  3. NLTK의 FreqDist 사용하기

- **Counter 사용하기** (dictionary 사용하기, NLTK의 FreqDist 사용하기는 출처 참고)

  ```python
  from nltk.tokenize import sent_tokenize
  from nltk.tokenize import word_tokenize
  from nltk.corpus import stopwords
  from collections import Counter
  ```

  ```python
  text = "A barber is a person. a barber is good person. a barber is huge person. he Knew A Secret! The Secret He Kept is huge secret. Huge secret. His barber kept his word. a barber kept his word. His barber kept his secret. But keeping and keeping such a huge secret to himself was driving the barber crazy. the barber went up a huge mountain."
  ```

  ```python
  # 문장 토큰화
  text = sent_tokenize(text)
  print(text)
  ```

  ```python
  ['A barber is a person.', 'a barber is good person.', 'a barber is huge person.', 'he Knew A Secret!', 'The Secret He Kept is huge secret.', 'Huge secret.', 'His barber kept his word.', 'a barber kept his word.', 'His barber kept his secret.', 'But keeping and keeping such a huge secret to himself was driving the barber crazy.', 'the barber went up a huge mountain.']
  ```

  기존의 텍스트 데이터가 문장 단위로 토큰화 됨

  ```python
  # 정제와 단어 토큰화
  sentences = []
  stop_words = set(stopwords.words('english'))
  
  for i in text:
      sentence = word_tokenize(i) # 단어 토큰화를 수행합니다.
      result = []
  
      for word in sentence: 
          word = word.lower() # 모든 단어를 소문자화하여 단어의 개수를 줄입니다.
          if word not in stop_words: # 단어 토큰화 된 결과에 대해서 불용어를 제거합니다.
              if len(word) > 2: # 단어 길이가 2이하인 경우에 대하여 추가로 단어를 제거합니다.
                  result.append(word)
      sentences.append(result) 
  print(sentences)
  ```
  
  ```python
  [['barber', 'person'], ['barber', 'good', 'person'], ['barber', 'huge', 'person'], ['knew', 'secret'], ['secret', 'kept', 'huge', 'secret'], ['huge', 'secret'], ['barber', 'kept', 'word'], ['barber', 'kept', 'word'], ['barber', 'kept', 'secret'], ['keeping', 'keeping', 'huge', 'secret', 'driving', 'barber', 'crazy'], ['barber', 'went', 'huge', 'mountain']]
  ```
  
  문장 단위의 토큰화에서 단어 단위의 토큰화
  
  ```python
  words = sum(sentences, [])
  # 위 작업은 words = np.hstack(sentences)로도 수행 가능.
  print(words)
  ```
  
  ```python
  ['barber', 'person', 'barber', 'good', 'person', 'barber', 'huge', 'person', 'knew', 'secret', 'secret', 'kept', 'huge', 'secret', 'huge', 'secret', 'barber', 'kept', 'word', 'barber', 'kept', 'word', 'barber', 'kept', 'secret', 'keeping', 'keeping', 'huge', 'secret', 'driving', 'barber', 'crazy', 'barber', 'went', 'huge', 'mountain']
  ```
  
  token 빈도수를 알기 위해 문장 경계 제거 
  
  ```python
  vocab = Counter(words) # 파이썬의 Counter 모듈을 이용하면 단어의 모든 빈도를 쉽게 계산가능
  print(vocab)
  ```
  
  ```python
  Counter({'barber': 8, 'secret': 6, 'huge': 5, 'kept': 4, 'person': 3, 'word': 2, 'keeping': 2, 'good': 1, 'knew': 1, 'driving': 1, 'crazy': 1, 'went': 1, 'mountain': 1}) # type : dict
  ```
  
  ```python
  print(vocab["barber"]) # 'barber'라는 단어의 빈도수 출력
  ```
  
  ```python
  8
  ```
  
  token이 키(key)로, 단어에 대한 빈도수가 값(value)인 dict 구축
  
  ```python
  vocab_size = 5
  vocab = vocab.most_common(vocab_size) # 등장 빈도수가 높은 상위 5개의 단어만 저장
  vocab
  ```
  
  ```python
  [('barber', 8), ('secret', 6), ('huge', 5), ('kept', 4), ('person', 3)]
  ```
  
  most_common()를 통해 상위 빈도수를 가진 주어진 수의 단어만을 리턴. **이를 사용하여 등장 빈도수가 높은 단어들을 원하는 개수만큼만 얻을 수 있음**
  
  ```python
  word_to_index = {}
  i = 0
  for (word, frequency) in vocab :
      i = i+1
      word_to_index[word] = i
  print(word_to_index)
  ```
  
  ```python
  {'barber': 1, 'secret': 2, 'huge': 3, 'kept': 4, 'person': 5}
  ```
  
  최종적으로 vocabulary 구축 
  
  ```python
  word_to_index['OOV'] = len(word_to_index) + 1
  ```
  
  'OOV' index 추가
  
  ```python
  encoded = []
  for s in sentences:
      temp = []
      for w in s:
          try:
              temp.append(word_to_index[w])
          except KeyError:
              temp.append(word_to_index['OOV'])
      encoded.append(temp)
  print(encoded)
  ```
  
  ```python
  [[1, 5], [1, 6, 5], [1, 3, 5], [6, 2], [2, 4, 3, 2], [3, 2], [1, 4, 6], [1, 4, 6], [1, 4, 2], [6, 6, 3, 2, 6, 1, 6], [1, 6, 3, 6]]
  ```
  
  

### 케라스(Keras)를 이용한 텍스트 전처리

케라스에서는 기본적인 전처리를 위한 도구들을 제공

```python
from tensorflow.keras.preprocessing.text import Tokenizer
```

```python
sentences=[['barber', 'person'], ['barber', 'good', 'person'], ['barber', 'huge', 'person'], ['knew', 'secret'], ['secret', 'kept', 'huge', 'secret'], ['huge', 'secret'], ['barber', 'kept', 'word'], ['barber', 'kept', 'word'], ['barber', 'kept', 'secret'], ['keeping', 'keeping', 'huge', 'secret', 'driving', 'barber', 'crazy'], ['barber', 'went', 'huge', 'mountain']]
```

```python
tokenizer = Tokenizer()
tokenizer.fit_on_texts(sentences) # fit_on_texts()안에 코퍼스를 입력으로 하면 빈도수를 기준으로 단어 집합을 생성
```

```python
print(tokenizer.word_index)
```

```python
{'barber': 1, 'secret': 2, 'huge': 3, 'kept': 4, 'person': 5, 'word': 6, 'keeping': 7, 'good': 8, 'knew': 9, 'driving': 10, 'crazy': 11, 'went': 12, 'mountain': 13}
```

```python
print(tokenizer.word_counts)
```

```python
OrderedDict([('barber', 8), ('person', 3), ('good', 1), ('huge', 5), ('knew', 1), ('secret', 6), ('kept', 4), ('word', 2), ('keeping', 2), ('driving', 1), ('crazy', 1), ('went', 1), ('mountain', 1)])
```

```python
print(tokenizer.texts_to_sequences(sentences))
```

```python
[[1, 5], [1, 8, 5], [1, 3, 5], [9, 2], [2, 4, 3, 2], [3, 2], [1, 4, 6], [1, 4, 6], [1, 4, 2], [7, 7, 3, 2, 10, 1, 11], [1, 12, 3, 13]]
```



-**vocab 크기 정하기** 

```
vocab_size = 5
tokenizer = Tokenizer(num_words = vocab_size + 1) # 상위 5개 단어만 사용
tokenizer.fit_on_texts(sentences)
```

또한 **vocab의 크기**를 정할 때는 tokenizer = Tokenizer(num_words=숫자) 방법을 이용할 수 있음. 이때 num_words에서 +1을 더해서 값을 넣어주는 이유는 num_words는 숫자를 0부터 카운트하기 때문. 이때 **0은 padding으로 사용됨**. 

```
print(tokenizer.word_index)
```

```
{'barber': 1, 'secret': 2, 'huge': 3, 'kept': 4, 'person': 5, 'word': 6, 'keeping': 7, 'good': 8, 'knew': 9, 'driving': 10, 'crazy': 11, 'went': 12, 'mountain': 13}
```

```
print(tokenizer.word_counts)
```

```
OrderedDict([('barber', 8), ('person', 3), ('good', 1), ('huge', 5), ('knew', 1), ('secret', 6), ('kept', 4), ('word', 2), ('keeping', 2), ('driving', 1), ('crazy', 1), ('went', 1), ('mountain', 1)])
```

상위 5개 token만을 사용하겠다고 선언하였지만 word_index와 word_counts에서는 13개의 token이 모두 출력됨. 하지만 **texts_to_sequences를 적용할 때는 상위 5개의 token만** 이용됨.

```python
print(tokenizer.texts_to_sequences(sentences))
```

```python
[[1, 5], [1, 5], [1, 3, 5], [2], [2, 4, 3, 2], [3, 2], [1, 4], [1, 4], [1, 4, 2], [3, 2, 1], [1, 3]]
```



**OOV 인덱스 추가하기**

```python
vocab_size = 5
tokenizer = Tokenizer(num_words = vocab_size + 2, oov_token = 'OOV')
# 빈도수 상위 5개 단어만 사용. 숫자 0과 OOV를 고려해서 단어 집합의 크기는 +2
tokenizer.fit_on_texts(sentences)
```

```python
print('단어 OOV의 인덱스 : {}'.format(tokenizer.word_index['OOV']))
```

```python
단어 OOV의 인덱스 : 1
```

```python
print(tokenizer.texts_to_sequences(sentences))
```

```python
[[2, 6], [2, 1, 6], [2, 4, 6], [1, 3], [3, 5, 4, 3], [4, 3], [2, 5, 1], [2, 5, 1], [2, 5, 3], [1, 1, 4, 3, 1, 2, 1], [2, 1, 4, 1]]
```

빈도수 상위 5개의 단어는 2 ~ 6까지의 인덱스를 가졌으며, 그 외 단어 집합에 없는 'good'과 같은 단어들은 전부 'OOV'의 인덱스인 1로 인코딩됨

### 출처

- https://wikidocs.net/31766
