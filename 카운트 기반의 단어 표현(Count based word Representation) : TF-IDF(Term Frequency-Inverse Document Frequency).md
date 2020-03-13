# 카운트 기반의 단어 표현(Count based word Representation) : TF-IDF(Term Frequency-Inverse Document Frequency)

### TF-IDF(Term Frequency-Inverse Document Frequency)

- TF-IDF란 : 
  - TF : 단어의 빈도수. 특정 단어가 문서내에 얼마나 자주 등장하는지를 나타내는 값. **빈도수가 높을수록 문서에서 중요한 단어로 판단**
    TF(d, t) : 특정 문서 d에서의 특정 단어 t 등장 횟수
  - IDF : 문서 빈도수에 역을 취한 것. **다른 문서에서도 자주 등장하면 단어의 중요도는 낮아**지기에 이를 역으로 취해서 IDF 값이 높을수록 중요한 단어로 판단
    IDF(t) : 1/(특정 단어 t가 등장한 문서의 갯수)
  - TF-IDF : TF와 IDF를 곱한 값으로, 점수가 높은 단어일수록 **다른 문서에는 자주 등장하지 않고 해당 문서에서 자주 등장하는 단어**를 의미
    TF-IDF(d,t) = TF(d,t) x IDF(t)
  - TF-IDF의 쓰임 : 주로 **문서의 유사도를 구하는 작업, 검색 시스템에서 검색 결과의 중요도를 정하는 작업, 문서내에서 특정 단어의 중요도를 구하는 작업** 등으로 쓰일 수 있음



### 사이킷런을 이용한 DTM과 TF-IDF 실습

- DTM 실습 (CountVectorizer을 이용)

  ```python
from sklearn.feature_extraction.text import CountVectorizer
corpus = [
    'you know I want your love',
    'I like you',
    'what should I do ',    
]
vector = CountVectorizer()
print(vector.fit_transform(corpus).toarray()) # 코퍼스로부터 각 단어의 빈도 수를 기록한다.
print(vector.vocabulary_) # 각 단어의 인덱스가 어떻게 부여되었는지를 보여준다.
  ```

  ```python
[[0 1 0 1 0 1 0 1 1]
 [0 0 1 0 0 0 0 1 0]
 [1 0 0 0 1 0 1 0 0]]
{'you': 7, 'know': 1, 'want': 5, 'your': 8, 'love': 3, 'like': 2, 'what': 6, 'should': 4, 'do': 0}
  ```

- TF-IDF 실습 (TfidfVectorizer을 이용 → 보편적인 TF-IDF 식에서 좀 더 조정된 식을 사용. TF-IDF에 L2 정규화를 이용해 값을 조정하는 등등 )

  ```python
  rom sklearn.feature_extraction.text import TfidfVectorizer
  corpus = [
      'you know I want your love',
      'I like you',
      'what should I do ',    
  ]
  tfidfv = TfidfVectorizer().fit(corpus)
  print(tfidfv.transform(corpus).toarray())
  print(tfidfv.vocabulary_)
  ```

  ```python
  [[0.         0.46735098 0.         0.46735098 0.         0.46735098 0.         0.35543247 0.46735098]
   [0.         0.         0.79596054 0.         0.         0.         0.         0.60534851 0.        ]
   [0.57735027 0.         0.         0.         0.57735027 0.         0.57735027 0.         0.        ]]
  {'you': 7, 'know': 1, 'want': 5, 'your': 8, 'love': 3, 'like': 2, 'what': 6, 'should': 4, 'do': 0}
  ```

  

### 참고자료

- https://wikidocs.net/31698
- https://nesoy.github.io/articles/2017-11/tf-idf

