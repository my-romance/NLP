# 텍스트 전처리 : 불용어(stopword)

갖고 있는 데이터에서 유의미한 단어 토큰만을 선별하기 위해서 **큰 의미가 없는 단어 토큰(불용어)** 제거하는 작업. 여기서 큰 의미가 없다라는 것은 **자주 등장하지만 task에 있어서는 큰 도움이 되지 않는단어** → **불용어**

Example : 

- 영어 (word 단위) : a, the, of...
- 한국어 (형태소 단위) : 을,를,이,가...

<br>

### NLTK에서 영어 불용어 확인하기

```python
from nltk.corpus import stopwords  
stopwords.words('english')[:10]  
```

```python
['i', 'me', 'my', 'myself', 'we', 'our', 'ours', 'ourselves', 'you', 'your'] 
```

 불용어 리스트 중 10개만을 출력

<br>

### NLTK를 통해서 영어 불용어 제거하기

```python
from nltk.corpus import stopwords 
from nltk.tokenize import word_tokenize 

example = "Family is not an important thing. It's everything."
stop_words = set(stopwords.words('english')) 

word_tokens = word_tokenize(example)

result=[word for word in word_tokens if not word in stop_words]

print(word_tokens) 
print(result) 
```

```python
['Family', 'is', 'not', 'an', 'important', 'thing', '.', 'It', "'s", 'everything', '.']
['Family', 'important', 'thing', '.', 'It', "'s", 'everything', '.']
```

<br>

### 한국어에서 불용어 제거하기

- 한국어 불용어 제거 방법 : **토큰화 후에 조사, 접속사 등을 제거**
- 하지만 불용어를 제거하려고 하다보면 **명사, 형용사와 같은 단어들 중에 불용어로서 제거**하고 싶은 단어들이 생김 → 사용자가 직접 **불용어 사전 만듦**

- 실습 : 불용어 정의 및 불용어 제거

  ```python
  from nltk.corpus import stopwords 
  from nltk.tokenize import word_tokenize 
  
  example = "고기를 아무렇게나 구우려고 하면 안 돼. 고기라고 다 같은 게 아니거든. 예컨대 삼겹살을 구울 때는 중요한 게 있지."
  stop_words = "아무거나 아무렇게나 어찌하든지 같다 비슷하다 예컨대 이럴정도로 하면 아니거든"
  # 위의 불용어는 명사가 아닌 단어 중에서 임의로 선정한 것으로 실제 의미있는 선정 기준이 아님
  stop_words=stop_words.split(' ')
  word_tokens = word_tokenize(example)
  
  result=[word for word in word_tokens if not word in stop_words]
  
  print(word_tokens) 
  print(result)
  ```

  ```python
  ['고기를', '아무렇게나', '구우려고', '하면', '안', '돼', '.', '고기라고', '다', '같은', '게', '아니거든', '.', '예컨대', '삼겹살을', '구울', '때는', '중요한', '게', '있지', '.']
  ['고기를', '구우려고', '안', '돼', '.', '고기라고', '다', '같은', '게', '.', '삼겹살을', '구울', '때는', '중요한', '게', '있지', '.']
  ```

- 한국어 불용어 리스트를 보여주는 링크 : https://www.ranks.nl/stopwords/korean

<br>

### 출처 

- https://wikidocs.net/22530



