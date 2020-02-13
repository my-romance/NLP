# Phrase queries and positional indexes

IR system이 어떻게 phrase queries를 문서에 mapping시킬지가 point 

### Phrase queries

- We want to be able to answer queries such as "**stanford university**" - as a phrase
- Thus the sentence "I went to university at stanford " is not match.
  - The concept of phrase queries has proven easily understood by users; one of the few "advanced search" ideas that works
  - Many more queries are implicit phrase queries
- For this, it no longer suffices to store only < term:docs > entries
  즉, < term:docs >구조로는 2개의 단어(phrase)를 포함하는 문서는 찾을 수 있지만 2개의 단어가 인접해있는 문서만을 찾을 수는 없음  




### Solution 1 : Biword indexes

- index every consecutive pair of terms in the test as a phrase

- For example the test "Friends, Romans, Countrymen" would generate the biwords

  - friends romans

  - romans coutrymen

    → (**friends romans**) AND (**romans coutrymen**)

- Each of these biwords (like freinds romans, romans country) is now a dictionary term

- Two-word phrase query-processing is now immediate.



### Longer phrase queries

- Longer phrases can ve processed by breaking then down

- **stanford university palo alto** can be broken into the Boolean query on biwords:
  (**stanford university**) And (**university palo**) And (**palo alto**)
  즉, **A B C D**라는 phrase query가 들어올 때 word를 두개씩 묶어 (**A B**)  AND (**B C**) AND (**C D**)이 True인 document를 찾는다 

- can have false positives 
  즉, **A B C D**라는 phrase의 의미를 파악하고 관련 document를 찾는 것이 아니라 **A B**, **B C**, **C D** phrase이 포함되는 document를 찾는 것이기에 false positives가 생길 수 있다.

  

### Issues for biword indexes

- False positives
- **Index blowup** due to bigger dictionary
  - infeasible for more than biwords, big even for them
- Biword indexes are **not** the **standard solution** but can be part of a **compound strategy**



### Extended biwords

기존 biword indexes 방법은 단어 2개가 의미,관계가 없는데 묶어지게 될 수 있음 
 → pos tag를 이용하여 의미, 관계가 있는 단어를 묶자 (False positives 해결)

- parse the indexed text and perform part-of-speech-tagging(POST)
- bucket the terms into Nouns("명사")(N) and articles("관사")/prepositions("전치사") (X)
- now deem any string of terns of the form NX*N to be an extended biword
  - example : catcher(**N**) in(**X**) the(**X**) rye(**N**) 
- query processing : parse it into N's and X's
  - segment query into enhanced biwords
  - look up index



### Solution 2 : Positional indexes

: 위치를 포함