# Scaling Monosemanticity: Extracting Interpretable Features from Claude 3 Sonnet (1)

> 스터디용 리뷰
> 

> 발표 날짜 : 2024-06-03
> 

링크: [Scaling Monosemanticity: Extracting Interpretable Features from Claude 3 Sonnet (transformer-circuits.pub)](https://transformer-circuits.pub/2024/scaling-monosemanticity/index.html#feature-survey-neighborhoods-immunology)

오피셜 요약: [Golden Gate Claude: What is it? - Claude101](https://claude101.com/golden-gate-claude/)

<aside>
💡 LLM에 대한설명

</aside>

### 문제

- 최근 LLM의 성능이 올라갔지만 그만큼 안전 장치 및 제한이 없고. 여러 방법으로 만들어도 jail breaking으로 안전장치를 무시하고 쉽게 “불법적인” 출력을 뽑을수있다. 그러한 문제를 해결하기위해서 여러 실험을한 페이퍼다.

---

### 생략

- 설명에 앞서 모델의 대한 설명은 상당히 어렵고, 길고, 충분한 이해가 없기에 생략하도록하겠다. 만약 원할 경우 링크에서 읽기를 바람.
    
    ![Screenshot 2024-06-03 at 20.22.37.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/772b59ac-085a-49eb-b53c-d26872914da6.png?raw=true)
    

---

### 설명

- 저자들은 모델을 입력의 어느 부분과 선택한 단어의 관련성을 찾을 수 있도록 구현했다.(구현에 대한 내용은 링크에서!). 아래에서 보다 싶이 입력으로 Golde Gate Bridge의 이미지 혹은 텍스트가 들어올시 입력 텍스트에서 어느부분이 관련있는지를 표현할수있다.

![Screenshot 2024-06-03 at 20.25.35.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.25.35.png?raw=true)

- 연결 강도에 따라 4단계로 나뉜다. 여기서부터 연결 강도는 뉴련이 얼마나 활성화한지를 의미하며, Density는 가중치의 dense, sparse인지를 의미한다. 저자들은 가중치(뉴런을) feature(특징)이라고 언급하지만 이해를 높이기 위해 여기서는 가중치라고 언급하겠다.
    - 0: 상관없음
    - 1 : 확실하지 않게 비간접 적으로 연관됨
    - 2 : 직접관련있음
    - 3 : 해당 단어를 대표
        
        ![Screenshot 2024-06-03 at 20.35.15.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.35.15.png?raw=true)
        
- 가중치가 깊어지면 깊어질수록 sparse해지지만 높은 활셩화률을 보인다. 저자들은 하위 가중치가 desnse한 이유는 대부분의 단어들이 하위에서 각각의 의미를 이해하는 파라미터들이 존재한다고 설명했다.

![Screenshot 2024-06-03 at 20.37.19.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.37.19.png?raw=true)

![Screenshot 2024-06-03 at 20.39.19.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.39.19.png?raw=true)

![Screenshot 2024-06-03 at 20.44.10.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.44.10.png?raw=true)

- 영어에서 만 아니라 다른 언어에서도 적용될수있다. 저자들은 위와같이 가중치 활성도와 여러 문제는 향후 연구로 남겼다.

![Screenshot 2024-06-03 at 20.44.38.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.44.38.png?raw=true)

- 위에서 어느 파라미터들이 활성되는걸 확인 가능하다. 그렇다는건 특정 의미에대한 가중치들은 확인가능하다는 거며. 저자들은 해당 가중치들을 증가시켜서 질문을 했다. 놀랍게도 모델은 해당 저자들이 원하는 의미를 반영하여 대답을 했다. 예시로 The Golden Gate Bridge의 가중치를 늘렸을 경우 자신을 설명하라는 질문에 자신을 The Golden Gate Bridge라고 설명했다.
    
    ![Screenshot 2024-06-03 at 20.45.11.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.45.11.png?raw=true)
    
    ![Screenshot 2024-06-03 at 20.45.16.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.45.16.png?raw=true)
    
    ![Screenshot 2024-06-03 at 20.46.04.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.46.04.png?raw=true)
    
    ![Screenshot 2024-06-03 at 20.46.17.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.46.17.png?raw=true)
    
    ![Screenshot 2024-06-03 at 20.46.27.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.46.27.png?raw=true)
    
- 위에서 어떻게 잘못된 코드에도 맞게 대답하는지를 확인했더니 모델이 가중치에 맞게 대답하기위해 코드의 잘못 된 부분을 고치고 대답하는 걸 확인가능했다.
    
    ![Screenshot 2024-06-03 at 20.46.48.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.46.48.png?raw=true)
    
- 저자들은 같은 모델의 파라미터를 키워서 학습했으며 각 각 모델에 대해서 특정 의미에 대해서 근접하는 의미들(단어들을) 확인하여 표시했다. 중간의 the Golden Gate Bridge의 가중치가 있고 다른 의미들이 존재한다.
    
    ![Screenshot 2024-06-03 at 20.47.34.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Scaling%20Monosemanticity%20Extracting%20Interpretable%20F%20f74cd2014a6545c8968c5da2593d0654/Screenshot_2024-06-03_at_20.47.34.png?raw=true)
    

---

### 결론

- 해당 논문의 가장 큰 기여는 지금까지 설명이 힘들던 뉴럴모델 더 나아가 LLM의 대해 설명이 가능하다는 입증이다. 더 나아가 원하는 결과를 안정적으로 출력할수있는 강력한 방법을 소개했다.

---
