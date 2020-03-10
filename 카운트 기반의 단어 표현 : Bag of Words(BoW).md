# 카운트 기반의 단어 표현(Count based word Representation) : Bag of Words(BoW)

### Bag of Words란?

Bag of Words : 단어들의 순서는 고려하지 않고, **단어들의 출현빈도에만 집중**하여 텍스트 데이터 수치화한 표현 방법. 



- BoW를 만드는 단계
  1. 각 단어에 고유한 정수 인덱스를 부여
  2. 각 인덱스의 위치에 단어 토큰의 등장 횟수를 기록한 벡터를 만듦.

- 실습 : 한 문서(**정부가 발표하는 물가상승률과 소비자가 느끼는 물가상승률은 다르다.**)의 BoW 만들기

  ```python
  from konlpy.tag import Okt
  import re  
  okt=Okt()  
  
  token=re.sub("(\.)","","정부가 발표하는 물가상승률과 소비자가 느끼는 물가상승률은 다르다.")  
  # 정규 표현식을 통해 온점을 제거하는 정제 작업입니다.  
  token=okt.morphs(token)  
  # OKT 형태소 분석기를 통해 토큰화 작업을 수행한 뒤에, token에다가 넣습니다.  
  
  word2index={}  
  bow=[]  
  for voca in token:  
           if voca not in word2index.keys():  
               word2index[voca]=len(word2index)  
  # token을 읽으면서, word2index에 없는 (not in) 단어는 새로 추가하고, 이미 있는 단어는 넘깁니다.   
               bow.insert(len(word2index)-1,1)
  # BoW 전체에 전부 기본값 1을 넣어줍니다. 단어의 개수는 최소 1개 이상이기 때문입니다.  
           else:
              index=word2index.get(voca)
  # 재등장하는 단어의 인덱스를 받아옵니다.
              bow[index]=bow[index]+1
  # 재등장한 단어는 해당하는 인덱스의 위치에 1을 더해줍니다. (단어의 개수를 세는 것입니다.)  
  print(word2index)  
  ```

  ```python
  ('정부': 0, '가': 1, '발표': 2, '하는': 3, '물가상승률': 4, '과': 5, '소비자': 6, '느끼는': 7, '은': 8, '다르다': 9)  
  ```

  ```python
  print(bow)
  ```

  ```python
  [1, 2, 1, 1, 2, 1, 1, 1, 1, 1]  
  ```



### 여러 문서가 존재하는 경우 문서의 BoW

- BoW를 만드는 단계

  1. 여러 문서의 단어 집합을 합침
  2. 해당 단어 집합에 대한 각 문서의 BoW를 만듦

- example

  문서 1 : **정부가 발표하는 물가상승률과 소비자가 느끼는 물가상승률은 다르다.**

  문서 2 : **소비자는 주로 소비하는 상품을 기준으로 물가상승률을 느낀다.**
  

  1. 여러 문서의 단어 집합을 합침
     ```python
  ('정부': 0, '가': 1, '발표': 2, '하는': 3, '물가상승률': 4, '과': 5, '소비자': 6, '느끼는': 7, '은': 8, '다르다': 9, '는': 10, '주로': 11, '소비': 12, '상품': 13, '을': 14, '기준': 15, '으로': 16, '느낀다': 17) 
     ```

  2. 해당 단어 집합에 대한 각 문서의 BoW를 만듦
     ```python
     단계 1의 단어 집합에 대한 문서1 BoW : [1, 2, 1, 1, 2, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0]  
     단계 1의 단어 집합에 대한 문서2 BoW : [0, 0, 0, 1, 1, 0, 1, 0, 0, 0, 1, 1, 1, 1, 2, 1, 1, 1]  
     ```

- BoW가 사용 사례 : BoW는 각 단어가 등장한 횟수를 수치화하는 텍스트 표현 방법이기에, 주로 **어떤 단어가 얼마나 등장했는지를 기준**으로 문서가 **어떤 성격의 문서인지를 판단**하는 작업에 쓰임. 즉, **분류 문제나 여러 문서 간의 유사도**를 구하는 문제에 주로 쓰임
  example 
  
  - '달리기', '체력', '근력'과 같은 단어가 자주 등장하면 해당 문서를 체육 관련 문서로 분류할 수 있음
  - 미분', '방정식', '부등식'과 같은 단어가 자주 등장하면 수학 관련 문서로 분류할 수 있음



### **CountVectorizer 클래스로 BoW 만들기**

사이킷 런에서는 단어의 빈도를 Count하여 Vector로 만드는 CountVectorizer 클래스를 지원. 하지만 CountVectorizer는 단지 띄어쓰기만을 긴으로 단어를 자르기에 한국어의 경우 알맞은 토큰화(ex : pos)를 먼저 진행해야함.

```python
from sklearn.feature_extraction.text import CountVectorizer
corpus = ['you know I want your love. because I love you.']
vector = CountVectorizer()
print(vector.fit_transform(corpus).toarray()) # 코퍼스로부터 각 단어의 빈도 수를 기록한다.
print(vector.vocabulary_) # 각 단어의 인덱스가 어떻게 부여되었는지를 보여준다.
```

```python
[[1 1 2 1 2 1]]
{'you': 4, 'know': 1, 'want': 3, 'your': 5, 'love': 2, 'because': 0}
```



### CountVectorizer 클래스로 불용어를 제거한 BoW 만들기

CountVectorizer는 불용어를 지정하면, 불용어는 제외하고 BoW를 만들 수 있도록 불용어 제거 기능 지원

- 사용자가 직접 정의한 불용어 사용

  ```python
  from sklearn.feature_extraction.text import CountVectorizer
  
  text=["Family is not an important thing. It's everything."]
  vect = CountVectorizer(stop_words=["the", "a", "an", "is", "not"])
  print(vect.fit_transform(text).toarray()) 
  print(vect.vocabulary_)
  ```

  ```python
  [[1 1 1 1 1]]
  {'family': 1, 'important': 2, 'thing': 4, 'it': 3, 'everything': 0}
  ```

- CountVectorizer에서 제공하는 자체 불용어 사용

  ```python
  from sklearn.feature_extraction.text import CountVectorizer
  
  text=["Family is not an important thing. It's everything."]
  vect = CountVectorizer(stop_words="english")
  print(vect.fit_transform(text).toarray())
  print(vect.vocabulary_)
  ```

  ```python
  [[1 1 1]]
  {'family': 0, 'important': 1, 'thing': 2}
  ```

- NLTK에서 지원하는 불용어 사용

  ```python
  from sklearn.feature_extraction.text import CountVectorizer
  from nltk.corpus import stopwords
  
  text=["Family is not an important thing. It's everything."]
  sw = stopwords.words("english")
  vect = CountVectorizer(stop_words =sw)
  print(vect.fit_transform(text).toarray()) 
  print(vect.vocabulary_)
  ```

  ```python
  [[1 1 1 1]]
  {'family': 1, 'important': 2, 'thing': 3, 'everything': 0}
  ```



### 출처

- https://wikidocs.net/22650
