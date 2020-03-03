# 텍스트 전처리 : 어간 추출(Stemming)와 표제어 추출(Lemmatization)

정규화 기법 (표기는 다르지만 같은 의미를 가지고 있어 하나의 단어로 일반화 → 코퍼스의 복잡성 ↓ )

- 표제어 추출 (Lemmatization)
- 어간 추출 (Stemming)

- 표제어 추출과 어간 추출의 **차이**
  - **표제어 추출**은 어간 추출과는 달리 **단어의 형태가 보존**. 
  - 표제어 추출은 문맥을 고려하기에, **표제어 추출의 결과는 해당 단어의 품사 정보 보존**.

<br>

### **표제어 추출 (Lemmatization)**

- 표제어 : "기본 사전형 단어"

- **표제어 추출** :  단어들이 다른 형태를 가지더라도 그 뿌리 단어를 찾아가서 단어의 개수를 줄일 수 있는지 판단. 즉, **단어들로부터 표제어를 찾아가는 과정.**
  Example :

  - am, are, is → be(표제어)

- 표제어 추출의 가장 섬세한 방법 : **형태학적 파싱**을 이용한 표제어 추출

  - 형태학 : 형태소로부터 단어들을 만들어가는 학문

  - 형태소 : 의미를 가진 가장 작은 단위. 크게 실질형태소와 형식형태소로 나뉨

    - 어간(실질형태소) : 단어의 의미를 담고 있는 단어의 핵심 부분
    - 접사(형식형태소) : 단어에 추가적인 의미를 주는 부분

  - 형태학적 파싱 : 어간과 접사를 분리하는 작업

    Example :

    - cats → cat(어간) + -s(접사)
    - cat → cat(어간)

<br>

### 어간 추출 (Stemming)

- 어간 추출 : 정해진 규칙만 보고 단어의 어미를 자르는 작업. 표제어 추출에 비해 단순.

- 포터 알고리즘 (Porter Algorithm)을 이용한 영어 어간 추출

  ```python
  from nltk.stem import PorterStemmer
  from nltk.tokenize import word_tokenize
  s = PorterStemmer()
  text="This was not the map we found in Billy Bones's chest, but an accurate copy, complete in all things--names and heights and soundings--with the single exception of the red crosses and the written notes."
  words=word_tokenize(text)
  print(words)
  ```

  ```python
  ['This', 'was', 'not', 'the', 'map', 'we', 'found', 'in', 'Billy', 'Bones', "'s", 'chest', ',', 'but', 'an', 'accurate', 'copy', ',', 'complete', 'in', 'all', 'things', '--', 'names', 'and', 'heights', 'and', 'soundings', '--', 'with', 'the', 'single', 'exception', 'of', 'the', 'red', 'crosses', 'and', 'the', 'written', 'notes', '.']
  ```

  ```python
  print([s.stem(w) for w in words])
  ```

  ```python
  ['thi', 'wa', 'not', 'the', 'map', 'we', 'found', 'in', 'billi', 'bone', "'s", 'chest', ',', 'but', 'an', 'accur', 'copi', ',', 'complet', 'in', 'all', 'thing', '--', 'name', 'and', 'height', 'and', 'sound', '--', 'with', 'the', 'singl', 'except', 'of', 'the', 'red', 'cross', 'and', 'the', 'written', 'note', '.']
  ```

  ```python
  words=['formalize', 'allowance', 'electricical']
  print([s.stem(w) for w in words])
  ```

  ```python
  ['formal', 'allow', 'electric']
  ```

<br>

### 한국어에서의 어간 추출 

- 한국어의 5언 9품사

  | 언       | 품사               |
  | -------- | ------------------ |
  | 체언     | 명사, 대명사, 수사 |
  | 수식언   | 관형사, 부사       |
  | 관계언   | 조사               |
  | 독립언   | 감탄사             |
  | **용언** | **동사, 형용사**   |

  이 중 용언에 해당되는 '동사'와 '형용사'는 **어간(stem)과 어미(ending)의 결합**으로 구성됨

- **활용 (conjugation)**

  - 활용 : 용언의 어간이 어미를 가지는 일
    - **어간(stem)** : 용언을 활용할 때, 원칙적으로 모양이 변하지 않는 부분. 활용에서 어미에 선행하는 부분. 때론 어간의 모양도 바뀔 수 있음(예: 긋다, 긋고, 그어서, 그어라)
    - **어미(ending)** : 용언의 어간 뒤에 붙어서 활용하면서 변하는 부분. 문법적 기능 수행

  - 활용의 종류
    1. **규칙 활용** : 어간이 어미를 취할 때, 어간의 모습이 일정
       Example : 잡(어간) + 다(어미) → 잡다
    2. **불규칙 활용** : 어간이 어미를 취할 때, 어간이나 어미의 모습이 변함
       Example : 오르(어간)+ 아/어(어미)→올라,  하(어간)+아/어(어미)→하여

- 어간 분리 방법

  - 규칙 활용인 경우 : 어간과 어미가 붙기 전의 모습과 어미가  붙은 후의 모습이 같으므로, **규칙 기반으로 어간을 단순히 분리**

  - 불규칙 활용인 경우 : 어간이 어미가 붙는 과정에서 어간의 모습이 바뀌므로, 단순한 분리만으로 어간 추출이 되지 않고 **좀 더 복잡한 규칙 필요**

<br>

### 어근과 어간의 차이

1. 어근에 해당하는 단어 : 조사를 제외한 8품사(명사, 대명사, 수사, 관형사, 부사, 동사, 형용사, 감탄사)
2. 어간에 해당하는 단어 : 동사, 형용사, (명사)이다 
3. **어근**은 동사와 형용사에서 **접사를 포함하지 않지만**, **어간**은 동사와 형용에서 **접사 포함**
   1. 먹다 : 먹고/먹어서/먹으니/먹는다
      - 어근 : "먹-", 어간 : "먹-"
   2. 샛노랗다 : 샛노랗고/샛노랗지만/샛노랗더라도
      - 어근 : "노랗-", 어간 : "샛노랗-"
   3. 입히다:입히고/입히니/입히니까/입힌다.
      - 어근 : "입-", 어간 : "입히-"

<br>

### 출처

- https://wikidocs.net/21707
- https://m.blog.naver.com/PostView.nhn?blogId=zzangdol57&logNo=221546199789&proxyReferer=https%3A%2F%2Fwww.google.com%2F
