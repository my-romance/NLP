# DistilBERT, a distilled version of BERT: smaller, faster, cheaper and lighter 논문 리뷰



## Introduction

최근 2년간 NLP 테스크에서 large-scale pre-trained language models을 많이 사용

![distilBert1](https://raw.githubusercontent.com/my-romance/NLP/master/pic/distilBert1.png)



**large-scale pre-trained language models**

- several hundred million parameters
- leads to better performances on downstream tasks.



**concerns about large-scale models**

- environmental cost of exponentially scaling these models’ computational requirements
- growing computational and memory requirements of these models may hamper wide adoption on device



**purpose**

- reach similar performances on many downstream-tasks using much smaller language models pre-trained with knowledge distillation →

  - lighter and faster at inference time
  - smaller computational training budget

- keep the flexibility of larger models

  

**result**

- our compressed models are small enough to run on the edge, e.g. on mobile devices.
- 40% smaller Transformer pre-trained can achieve similar performance on downstream tasks -> 60% faster at inference time
- Use triple loss → important for best performances



## Knowledge distillation

compact 모델(학생)이 더 큰 모델(교사) 또는 앙상블 모델의 동작을 재현하도록 훈련되는 압축 기술.
총 3가지의 loss를 통해 이를 훈련.

 

**train loss, using a triple loss** 

**$ L_{ce} $**

student가 teacher가 유사하게 확률분포를 가지도록 loss 설정
$$
L_{ce} = \sum_it_i \times \log(s_i). \\
(\ t_{i}\ is\ a\ probability\ estimated\ by\ the\ teacher\ model, \\
s_{i}\ is\ a\ probability\ estimated\ by\ the\ student\ model\ )
$$
이때, softmax-temperature을 사용
$$
p_i = \frac{exp(z_i/T)}{\sum_j exp(z_j/T)}. \\
(\ T\ is\ a\ controler\ the\ smoothness\ of\ the\ output\ distribution)
$$
at infernece, $T$ is to 1 to recover a standard softmax 



- softmax-temperature

  - 예시 : V = [1, 2, 3, 4, 5, 6]

    - softmax

      <img src="https://t1.daumcdn.net/cfile/tistory/999CD3435DCE569E1D" alt="img" style="zoom:90%;" align='left' />

    - softmax-temperature ($T$ = 2)

      <img src="https://t1.daumcdn.net/cfile/tistory/998A48435DCE569E14" alt="img" style="zoom:90%;" align='left'/>

    - softmax-temperature ($T$ = 10)

      <img src="https://t1.daumcdn.net/cfile/tistory/99DCEB435DCE569F24" alt="img" style="zoom:90%;" align='left'/>

    

**$ L_{mlm} $**

masked language modeling loss

**$ L_{cos} $** 

cosine embedding loss → align the directions of the student and teacher hidden state vectors.



## DistilBERT : a distilled version of BERT

최근 Transformer architecture에서 사용되는 많은 연산(linear layer and layer normalization)은 linear algebra frameworks에서 고도로 최적화되었으며, computation efficiency을 기준으로 last tensor의 차원 변동보다 layer의 수가 더 많은 영향을 미치는 것을 알았다.

따라서, layer의 수를 줄이는 것에 focus를 두었다.

**Student architecture**

- remove token-type embeddings

  ```python
  tokenized = tokenizer.encode_plus("I ate a clock yesterday.", "It was very time consuming.")
  
  '''
  tokenized 출력
  {
  	'input_ids': [101, 1045, 8823, 1037, 5119, 7483, 1012, 102, 2009, 2001, 2200, 2051, 15077, 1012, 102],
   	'token_type_ids': [0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1],
  	'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
  }
  '''
  ```

- remove pooler
  ![img](http://freesearch.pe.kr/wp-content/uploads/Screenshot_2019-04-20-1810-04805-pdf.png)

- the number of layers is reduced by a factor of 2



**Student(DistilBERT) initialization**

initialize the student from the teacher **by taking one layer out of two.**



**Distillation**

DistilBERT is distilled on very large batches leveraging gradient accumulation (up to 4K examples per batch) **using dynamic masking and without the next sentence prediction objective.**



**Data and compute power**

- triain DistilBERT on the **same corpus** as the original BERT model
- DistilBERT was trained on 8 16GB V100 GPUs for approximately 90 hours
- For the sake of comparison, the RoBERTa model [Liu et al., 2019] required 1 day of training on 1024 32GB V100.




## Experiments

**General Language Understanding**

General Language Understanding Evaluation (GLUE) benchmark을 통해 language understanding과 generalization을 평가

- GLUE : a collection of 9 datasets for evaluating natural language understanding systems



result :

![distilBert2](https://raw.githubusercontent.com/my-romance/NLP/master/pic/distilBert2.png)

- DistilBERT also compares surprisingly well to BERT, retaining 97% of the performance with 40% fewer parameters.



**Downstream tasks**

 a classification task (IMDb sentiment classification) and a question answering task (SQuAD v1.1)

result : 

<img src="https://raw.githubusercontent.com/my-romance/NLP/master/pic/distilBert3.png" alt="distilBert3" style="zoom:30%;" align="left"/>



또한, 두 단계의 distillation을 적용하여 성능을 더욱 향상 시킴

1. one during the pre-training phase
2. one during the adaptation phase

fine-tuning DistilBERT on SQuAD using a BERT model previously fine-tuned on SQuAD as a teacher for an additional term in the loss (knowledge distillation).



**Size and inference speed**

Inference time : a full pass time of GLUE task STS-B (sentiment analysis) on CPU with a batch size of 1

result : 

<img src="https://raw.githubusercontent.com/my-romance/NLP/master/pic/distilBert4.png" alt="distilBert4" style="zoom:30%;" align="left"/>

- DistilBERT has 40% fewer parameters than BERT and is 60% faster than BERT



**On device computation**

on-the-edge 적용 확인을 위해 QA mobile application을 만들어, iPhone 7 Plus의 average inference time 측정

result :

- Excluding the tokenization step, DistilBERT is 71% faster than BERT

- the DistilBERT model weighs 207 MB



**Ablation study**

student model의 initialization과 triple loss의 영향성을 알기위해, 구성을 각각 다르게 학습하여 실험

![distilBert5](https://raw.githubusercontent.com/my-romance/NLP/master/pic/distilBert5.png)

- random weights = not use teacher weights initialization



## 참고자료

- https://arxiv.org/pdf/1910.01108.pdf
- https://3months.tistory.com/491

