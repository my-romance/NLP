# 텍스트 전처리 : 토큰화(Tokenization)

전처리 : 크롤링 등으로 얻어낸 코퍼스 테이터를 용도 및 필요에 맞게 토큰화&정제&정규화 등을 하는 작업. 

**토큰화 : 코퍼스에서 토큰이라 불리는 단위로 나누는 작업**, 보통 의미있는 단위로 토큰을 정의

 <br>

### 단어 토큰화(word tokenization)

**단어 토큰화 : token의 기준을 단어(word)로 하는 경우**, 이 때 단어(word)는 단어 단위 외에도 단어구, 의미를 갖는 문자열로 간주되기도 함

Example : 띄어쓰기 기준으로 토큰화

- 입력 : "나는 학교에 간다"
- 출력 : "나는","학교에", "간다"

<br>

### 토큰화 중 생기는 선택의 순간

토큰화를 하다보면 예상하지 못한 경우가 생기기에 토큰화의 기준을 생각해봐야 하는 경우 발생 → 어떤 용도로 사용할 것인지에 따라, 그 용도에 영향을 주지 않는 기준으로 토큰화.

Example : 영어권 언어에서 아포스트로피(')가 들어가 있는 단어의 토큰화

- 문장 :  **Don't be fooled by the dark sounding name, Mr. Jone's Orphanage is as cheery as cheery goes for a pastry shop.**

- Don't의 토큰화 : Don't/Don t/ Dont/ Do n't

- jone's의 토큰화 : jone's/jone s/ jone/ jones

- NLTK를 이용한 문장 토큰화 

  ```python
  from nltk.tokenize import word_tokenize  
  print(word_tokenize("Don't be fooled by the dark sounding name, Mr. Jone's Orphanage is as cheery as cheery goes for a pastry shop."))  
  ```

  ```python
  ['Do', "n't", 'be', 'fooled', 'by', 'the', 'dark', 'sounding', 'name', ',', 'Mr.', 'Jone', "'s", 'Orphanage', 'is', 'as', 'cheery', 'as', 'cheery', 'goes', 'for', 'a', 'pastry', 'shop', '.']  
  ```

- wordPunctTokenizer를 이용한 문장 토큰화

  ```python
  from nltk.tokenize import WordPunctTokenizer  
  print(WordPunctTokenizer().tokenize("Don't be fooled by the dark sounding name, Mr. Jone's Orphanage is as cheery as cheery goes for a pastry shop."))
  ```

  ```python
  ['Don', "'", 't', 'be', 'fooled', 'by', 'the', 'dark', 'sounding', 'name', ',', 'Mr', '.', 'Jone', "'", 's', 'Orphanage', 'is', 'as', 'cheery', 'as', 'cheery', 'goes', 'for', 'a', 'pastry', 'shop', '.']  
  ```

- keras를 이용한 문장 토큰화

  ```python
  from tensorflow.keras.preprocessing.text import text_to_word_sequence
  print(text_to_word_sequence("Don't be fooled by the dark sounding name, Mr. Jone's Orphanage is as cheery as cheery goes for a pastry shop."))
  ```

  ```python
  ["don't", 'be', 'fooled', 'by', 'the', 'dark', 'sounding', 'name', 'mr', "jone's", 'orphanage', 'is', 'as', 'cheery', 'as', 'cheery', 'goes', 'for', 'a', 'pastry', 'shop']
  ```

<br>

### 토큰화에서 고려해야할 사항

1. 구두점이나 특수문자를 **단순** 제외해서는 안된다.
   - 갖고 있는 코퍼스에서 단어들을 걸러낼 때, 구두점이나 특수 문자를 단순히 제외하는 것은 옳지 않습니다. 코퍼스에 대한 정제 작업을 하다보면, 구두점조차도 하나의 토큰으로 분류하기도 합니다. 
     Example : "m.p.h", "$45.55", "01/02/06", "123,457,645"
2. 줄임말과 단어 내에 띄어쓰기가 있는 경우
   - 토큰화 작업에서 종종 영어권 언어의 아포스트로피(')는 압축된 단어를 다시 펼치는 역활
     Example : what're → what are, we're → we are
   - 하나의 단어이지만 중간에 띄어쓰기가 존재하는 경우 → 사용 용도에 따라 처리
     Example : New York, rock n roll

3. 표준 토큰화 예제 : Penn Treebank Tokenization

   - 규칙 1 : 하이푼으로 구성된 단어는 하나로 유지

   - 규칙 2 : doesn't와 같이 아포스트로피로 '접어'가 함께하는 단어는 분리

   - Example : "Starting a home-based restaurant may be an ideal. it doesn't have a food chain or restaurant of their own."

     ```python
     from nltk.tokenize import TreebankWordTokenizer
     tokenizer=TreebankWordTokenizer()
     text="Starting a home-based restaurant may be an ideal. it doesn't have a food chain or restaurant of their own."
     print(tokenizer.tokenize(text))
     ```

     ```python
     ['Starting', 'a', 'home-based', 'restaurant', 'may', 'be', 'an', 'ideal.', 'it', 'does', "n't", 'have', 'a', 'food', 'chain', 'or', 'restaurant', 'of', 'their', 'own', '.']
     ```

<br>

### **문장 토큰화(Sentence Tokenization)**

**문장 토큰화 : 토큰의 단위가 문장**, 이 작업은 갖고 있는 코퍼스 내에서 문장 단위로 구분하는 작업으로 sentence segmentation라고 부르기도 함

코퍼스가 어떤 국적의 언어인지 또는 해당 코퍼스 내에서 특수문자들이 어떻게 사용되고 있는지 따라 직접 규칙들을 정의해 볼 수 있다. 관련 논문도 많기 때문에 찾아보면 좋을 듯 하다. 

- 한국어 문장 토큰화 도구 : KSS(Korean Sentence Splitter)

  ```python
  import kss
  text='딥 러닝 자연어 처리가 재미있기는 합니다. 그런데 문제는 영어보다 한국어로 할 때 너무 어려워요. 농담아니에요. 이제 해보면 알걸요?'
  print(kss.split_sentences(text))
  ```

  ```python
  ['딥 러닝 자연어 처리가 재미있기는 합니다.', '그런데 문제는 영어보다 한국어로 할 때 너무 어려워요.', '농담아니에요.', '이제 해보면 알걸요?']
  ```

<br>

### 이진 분류기**(Binary Classifier)**

문장 토큰화에서의 예외 사항을 발생시키는 **온점처리를 위한 이진 분류기**

이진 분류기 → 두 개의 클래스

1. 온점(.)이 단어의 일부분일 경우. 즉, 온점이 약어로 쓰이는 경우
2. 온점(.)이 문장의 구분자일 경우.

<br>

### 한국어 토큰화의 어려움

- 영어의 토큰화 : New York과 같은 합성어나 he's 와 같이 줄임말에 대한 예외처리만 한다면, 띄어쓰기(whitespace)를 기준으로 하는 띄어쓰기 토큰화를 수행해도 단어 토큰화가 잘 작동
- 한국어의 토큰화 : 한국어의 경우에는 띄어쓰기 단위를 '어절'이라고 하는데 즉, 어절 토큰화는 한국어 NLP에서 지양됨. ← 한국어는 조사, 어미 등을 붙여서 만든 언어인 교착어

1. 한국어는 교착어이다.
   - 그라는 단어 하나에도 '그가', '그에게', '그를', '그와', '그는'과 같이 다양한 조사가 '그'라는 글자 뒤에 띄어쓰기 없이 붙음.
   - 자연어 처리를 하다보면 같은 단어임에도 서로 다른 조사가 붙어서 다른 단어로 인식이 되면 자연어 처리가 힘들고 번거로짐 → 한국어 NLP에서 조사 분리의 필요성 → **형태소의 필요성**
   - **형태소 : 뜻을 가진 가장 작은 말의 단위**
2. 한국어는 띄어쓰기가 영어보다 잘 지켜지지 않는다.
   - 한국어는 영어와는 달리 띄어쓰기가 지켜지지 않아도 글을 쉽게 이해할 수 있는 언어라는 점을 가지고 있기에 영어에 비하여 띄어쓰기가 잘 지켜지지 않음

<br>

### 품사 태**깅(Part-of-speech tagging)**

품사 태깅 : 단어의 의미를 제대로 파악하기 위해 해당 단어가 어떤 품사로 쓰였는지 알 수 있도록 하는 지표. 즉, **단어 토큰화 과정에서 각 단어가 어떤 품사로 쓰였는지 구분**
Example : "못"

- 망치를 사용해서 목재 따위를 고정하는 물건 → 명사
- "먹는다", "달린다"와 같은 동작 동사를 할 수 없다는 의미 → 부사

<br>

### KoNLPy를 이용한 한국어 토큰화 실습

코엔엘파이를 통해서 사용할 수 있는 형태소 분석기

1. Okt(Open Korea Text)
2. 메캅(Mecab)
3.  코모란(Komoran)
4. 한나눔(Hannanum)
5. 꼬꼬마(Kkma)

<br>

- KoNLPy의 Okt를 이용한 한국어 토큰화 : (Okt는 기존에는 Twitter라는 이름을 갖고있었으나 0.5.0 버전부터 이름이 변경되어 인터넷에는 아직 Twitter로 많이 알려져있음)

  1. morphs : 형태소 추출

  ```python
  from konlpy.tag import Okt  
  okt=Okt()  
  print(okt.morphs("열심히 코딩한 당신, 연휴에는 여행을 가봐요"))
  ```

  ```python
  ['열심히', '코딩', '한', '당신', ',', '연휴', '에는', '여행', '을', '가봐요']  
  ```

  2. pos : 품사 태깅(Part-of-speech tagging)

  ```python
  print(okt.pos("열심히 코딩한 당신, 연휴에는 여행을 가봐요")) 
  ```

  ```python
  [('열심히','Adverb'), ('코딩', 'Noun'), ('한', 'Josa'), ('당신', 'Noun'), (',', 'Punctuation'), ('연휴', 'Noun'), ('에는', 'Josa'), ('여행', 'Noun'), ('을', 'Josa'), ('가봐요', 'Verb')]  
  ```

  3. nouns : 명사 추출

  ```python
  print(okt.nouns("열심히 코딩한 당신, 연휴에는 여행을 가봐요"))  
  ```

  ```python
  ['코딩', '당신', '연휴', '여행']
  ```

- KoNLPy의 꼬꼬마 형태소 분석기를 이용한 한국어 토큰화

  1. morphs : 형태소 추출

  ```python
  from konlpy.tag import Kkma  
  kkma=Kkma()  
  print(kkma.morphs("열심히 코딩한 당신, 연휴에는 여행을 가봐요"))
  ```

  ```python
  ['열심히', '코딩', '하', 'ㄴ', '당신', ',', '연휴', '에', '는', '여행', '을', '가보', '아요']  
  ```

  2. pos : 품사 태깅(Part-of-speech tagging)

  ```python
  print(kkma.pos("열심히 코딩한 당신, 연휴에는 여행을 가봐요"))  
  ```

  ```python
  [('열심히','MAG'), ('코딩', 'NNG'), ('하', 'XSV'), ('ㄴ', 'ETD'), ('당신', 'NP'), (',', 'SP'), ('연휴', 'NNG'), ('에', 'JKM'), ('는', 'JX'), ('여행', 'NNG'), ('을', 'JKO'), ('가보', 'VV'), ('아요', 'EFN')]  
  ```

  3. nouns : 명사 추출

  ```python
  print(kkma.nouns("열심히 코딩한 당신, 연휴에는 여행을 가봐요")) 
  ```

  ```python
  ['코딩', '당신', '연휴', '여행']  
  ```
- 각 형태소 분석기는 성능과 결과가 다르게 나오기 때문에, 형태소 분석기의 선택은 사용하고자 하는 필요 용도에 어떤 형태소 분석기가 가장 적절한지를 판단하고 사용. 예를 들어서 속도를 중시한다면 메캅을 사용할 수 있음.

<br>

### 출처 

- https://wikidocs.net/21698
