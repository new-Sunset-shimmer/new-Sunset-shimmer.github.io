# Make Your LLM Fully Utilize the Context(2024)

> Make Your LLM Fully Utilize the Context
> 

> 스터디용 리뷰
> 

> 발표 날짜 : 2024-05-50
> 

<aside>
💡 새로운 학습방법과 데이터셋으로 모델이 Long-Context를 효과적으로 인식하기

</aside>

### 문제

- 여러 LLM이 강력한 성능을 보여주고있다. 특히 최근에는 모델들의 Tokens수도 늘면서 이전과 비교해도 좋은 성능을 보여주고있다. 하지만 Tokens수가 늘면서 Context를 완벽하게 이해하지 못하는 상황이 보이며, context의 중간에 있는 내용에 앞, 뒤에 있는 내용에 비해 제대로 집중 못하는  lost-in-the-middle[[arXiv:2307.03172](https://arxiv.org/abs/2307.03172)] 문제가 보인다. 이를  인간은 쉽게 풀수있는 Needle-in-the-Haystack problem과 passkey retrieval problem에 자주 실패하는 현상으로 관측이 가능하다고 저자들은 서술했다. 이번 논문에서는 해당 문제를 해결하기 위한 여러 방법론을 소개하고있다.

---

### 가정

- 저자들은 해당 문제가 학습중에 나온 편향에 의해 생기는 문제라 인식했다. 사전 학습, 미세 조정했을 경우에도 positional encoding에 편향이 생겨 context의 중간 위치들은 덜 중요하게 생각한다고 가정했다.

---

### 방법

- 해당 논문에서는 **INformation-INtensive (IN2) training** 소개했으며 추가로 model이 context를 제대로 이해하고있는지를 확인하기위해 새로운 평가 데이터셋 **VArious Long-context (VAL)**을 소개했다.
    - **IN2** : 모델의 context를 전부 이해하기위해서 즉 positional encoding의 편향 제거하기 위해 제안한 데이터 증강 방법이다. In2 에는 두가지의 데이터가 존재한다. 하나는 **Fine-Grained Information Awareness** 그리고 **Integration and Reasoning of Information**이다. 공통적으로 fixed windows로 Raw text 즉 문서를 쪼깨고 각 segment 혹은 segments에 대해 질문과 답변을 만들고 무작위 segments[임의로 생성]와 결합하여 Long context를 생성한다. Fine-Grained Information Awareness은 segment 하나에 대해 , Integration and Reasoning of Information은 여러 segment를 이용하여 Long context를 생성한다.
        
        ![IN 학습방법](Make%20Your%20LLM%20Fully%20Utilize%20the%20Context(2024)%209d501b275e0e4b57b0ac4f1d4beef00c/Screenshot_2024-05-20_at_15.59.28.png)
        
        IN 학습방법
        
    - **VAL** : 기존  Needle-in-the-Haystack problem을 확인하기 위한 기존 데이터셋들은 forward retrieval 패턴을 가지고있으며 이러한 문제는 problem자체를 쉽게 만드면 genral하지 않다. 그렇기에 general한 패턴을 가진 평가 데이터셋을 만들기 위해서 Bi-Direction, forward, backward retrieval 패턴들을 가진 데이터셋을 저자들은 제안했다. 위의 3개의 패턴에 적합한 스타일의 문서들을 각각 할당했다.
        - Bi-Direction : Document Sentence Retrieval
        - forward : Code Function Retrieval
        - backward : Database Entity Retrieval
        
    
    ![데이터셋 예시](Make%20Your%20LLM%20Fully%20Utilize%20the%20Context(2024)%209d501b275e0e4b57b0ac4f1d4beef00c/Screenshot_2024-05-20_at_16.23.08.png)
    
    데이터셋 예시
    

---

### 평가

- 저자들은 Mistral-7B모델을 IN2 데이터셋을 적용하여 pretrain했으며 이를 FILM-7B이라고 명명했다.

![Screenshot 2024-05-20 at 16.24.01.png](Make%20Your%20LLM%20Fully%20Utilize%20the%20Context(2024)%209d501b275e0e4b57b0ac4f1d4beef00c/Screenshot_2024-05-20_at_16.24.01.png)

![Screenshot 2024-05-20 at 16.24.34.png](Make%20Your%20LLM%20Fully%20Utilize%20the%20Context(2024)%209d501b275e0e4b57b0ac4f1d4beef00c/Screenshot_2024-05-20_at_16.24.34.png)

![Screenshot 2024-05-20 at 16.26.52.png](Make%20Your%20LLM%20Fully%20Utilize%20the%20Context(2024)%209d501b275e0e4b57b0ac4f1d4beef00c/Screenshot_2024-05-20_at_16.26.52.png)

### 결론

- 저자들은 이번 논문에서 제안한 IN2방법으로 short context에대한 성능도 유지하면서, long context에 대한 성능도 눈에 띄게 올릴수있다고 결론을 내렸다.

---