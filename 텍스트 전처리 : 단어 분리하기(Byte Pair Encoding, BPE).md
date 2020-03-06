# 텍스트 전처리 : 단어 분리하기(Byte Pair Encoding, BPE)

테스트 단계에서 기계가 배우지 못한 모르는 단어가 등장하면 그 단어를 단어 집합에 없는 단어란 의미에서 OOV(Out-Of-Vocabulary) 또는 UNK(Unknown Token)라고 표현 → 기계가 자연어를 이해하기가 어려워지고 주어진 task를 해결하기 어려워짐. 이와 같이 **모르는 단어(OOV)로 인해 문제를 제대로 풀지 못하는 상황**을 **OOV 문제**라고 함

OOV 문제를 완화하는 방법 : 단어 분리. 단어분리 중 대표 방법 : **BPE(Byte Pair Encoding)**

**단어 분리(subword segmenation)의 의미** :  하나의 단어는 의미있는 여러 내부 단어들(subwords)의 조합으로 구성된 경우가 많기 때문에, **하나의 단어를 여러 내부 단어로 분리해서 단어를 이해**하겠다.

<br>

### BPE(Byte Pair Encoding)

기본적인 BPE 알고리즘 → 1994년에 제안된 데이터 압축 알고리즘. **연속적으로 가장 많이 등장한 글자의 쌍을 찾아서 하나의 글자로 병합하는 방식**

Example : "aaabdaaabac" 문자열이 주어졌을 때, BPE 수행

```
ZabdZabac
Z=aa
```

```
ZYdZYac
Y=ab
Z=aa
```

```
XdXac
X=ZY
Y=ab
Z=aa
```

<br>

### 자연어 처리에서의 **BPE(Byte Pair Encoding)**

자연어 처리에서의 BPE는 단어 분리(word segmentation) 알고리즘

- 기존의 접근

  ```python
  # dictionary
  # 훈련 데이터에 있는 단어와 등장 빈도수
  low : 5, lower : 2, newest : 6, widest : 3
  ```

  ```python
  # vocabulary
  low, lower, newest, widest
  ```

  기존의 방법의 단어의 집합은 중복을 배제한 단어들의 집합을 의미. 이 경우 테스트 과정에서 **"lowest"란 단어가 등장한다면 기계는 이 단어를 OOV라 취급**하고 OOV 문제가 발생

- BPE 알고리즘을 사용한 경우

  1. 우선 딕셔너리의 모든 단어들을 글자 단위로 분리. 

  ```python
  # dictionary
  l o w : 5,  l o w e r : 2,  n e w e s t : 6,  w i d e s t : 3
  ```

  초기 단어 집합. 즉, **초기 구성은 글자 단위로 분리된 상태**

  

  2. 알고리즘의 동작은 몇 회 반복할 것인지를 정한 뒤 **그 횟수만큼 가장 빈도수가 높은 유니그램의 쌍을 하나의 유니그램으로 통합**.  
     1회 - 딕셔너리를 참고로 하였을 때 빈도수가 9로 가장 높은 (e, s)의 쌍을 es로 통합

     ```python
     # dictionary update!
     l o w : 5,
     l o w e r : 2,
     n e w es t : 6,
     w i d es t : 3
     ```

     ```python
     # vocabulary update!
     l, o, w, e, r, n, w, s, t, i, d, es
     ```

     2회 - 빈도수가 9로 가장 높은 (es, t)의 쌍을 est로 통합

     ```python
     # dictionary update!
     l o w : 5,
     l o w e r : 2,
     n e w est : 6,
     w i d est : 3
     ```

     ```python
     # vocabulary update!
     l, o, w, e, r, n, w, s, t, i, d, es, est
     ```

     3회 - 빈도수가 7로 가장 높은 (l, o)의 쌍을 lo로 통합

     ```python
     # dictionary update!
     lo w : 5,
     lo w e r : 2,
     n e w est : 6,
     w i d est : 3
     ```

     ```python
     # vocabulary update!
     l, o, w, e, r, n, w, s, t, i, d, es, est, lo
     ```

     이와 같은 방식으로 총 10회 반복하였을 때의 vocabulary은 아래와 같다

     ```python
     # dictionary update!
     low : 5,
     low e r : 2,
     newest : 6,
     wi d est : 3
     ```

     ```python
     # vocabulary update!
     l, o, w, e, r, n, w, s, t, i, d, es, est, lo, low, ne, new, newest, wi, wid, widest
     ```

  BPE를 사용한 경우 테스트 과정에서  "lowest"란 단어가 등장한다면, 기계는 **"lowest"를 "low"와 "est"** 두 단어로 인코딩. 이 두 단어는 둘 다 단어 집합에 있는 단어로 **OOV가 아님** 

- 코드 실습하기

  ```
  import re, collections
  ```

  ```
  num_merges = 10 # BPE 수행 횟수 설정
  ```

   각 단어는 글자(character) 단위로 분리

  ```
  vocab = {'l o w </w>' : 5,
           'l o w e r </w>' : 2,
           'n e w e s t </w>':6,
           'w i d e s t </w>':3
           }
  ```

  이 때 </w>는 단어의 맨 끝에 붙이는 특수 문자이며, 단어 단위를 알 수 있도록 붙임.

  ```python
  def get_stats(vocab):
      pairs = collections.defaultdict(int)
      for word, freq in vocab.items():
          symbols = word.split()
          for i in range(len(symbols)-1):
              pairs[symbols[i],symbols[i+1]] += freq
      return pairs
  
  def merge_vocab(pair, v_in):
      v_out = {}
      bigram = re.escape(' '.join(pair))
      p = re.compile(r'(?<!\S)' + bigram + r'(?!\S)')
      for word in v_in:
          w_out = p.sub(''.join(pair), word)
          v_out[w_out] = v_in[word]
      return v_out
  
  for i in range(num_merges):
      pairs = get_stats(vocab)
      best = max(pairs, key=pairs.get) # 가장 빈도수가 높은 유니그램의 쌍을 반환
      vocab = merge_vocab(best, vocab) # 
      print(best)
  ```

  ```python
  # best의 출력 결과
  ('e', 's')
  ('es', 't')
  ('est', '</w>')
  ('l', 'o')
  ('lo', 'w')
  ('n', 'e')
  ('ne', 'w')
  ('new', 'est</w>')
  ('low', '</w>')
  ('w', 'i')
  ```

<br>

### 센텐스피스 **(Sentencepiece)**

google에서 공개한 모델로, 자연어처리의 실무에서 단어 분리를 위해서 사용.

1. 기존 BPE 알고리즘은 느림, 센텐스피스가 좀 더 빠름
2. 사전 토큰화 작업없이 단어 분리 토큰화를 수행 → 각 언어를 위한 단어 토큰화 작업이 필요없음. 언어에 종속되지 X.

### 출처 
- https://wikidocs.net/33274
