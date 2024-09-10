# PLAYER*

> PLAYER*: Enhancing LLM-based Multi-Agent Communication and
Interaction in Murder Mystery Games
> 

> released : 2024 05 17
> 

> Review Date : 2024 09 09
> 

<aside>
💡

Multi agent를 활용한 투표로 결론 내기

</aside>

### Motivation

- LLM의 성능이 올라서 인간과 같은 대답을 생성할수있다. 이러한 특성을 이용하여 multi agent 대화 방식을 연구한 논문들이 많다. 하지만 사용자의 노력이 필요하며 일반화가 잘 안되어있다. 전통적인 multi-agent Reinforcement Learning을 적용할시 리워드와 action space등을 정의하기 힘들다. 또한 평가 방식 정의도 어렵다. 논문에서는 특정 아키텍쳐로 위의 문제들을 해결할 뿐만 아니라 평가 방식도 제안한다.

### Methodology

- 본 논문에서는 아키텍쳐를 효과적으로 실험해 보기 위해서 Murder Mystery Game을 채용했다. 모델은 아래와 같은 단계로 살인자를 찾아낸다.

![Screenshot 2024-09-09 at 19.55.35.png](PLAYER%200b6ecef879254060a11f42abd79286d5/Screenshot_2024-09-09_at_19.55.35.png)

- 각 Agent들은 Game rule, COT, Sensors(결과 도출 위한 특정 정보), Memory Retrieval, Self-reflection(현재 상황에 필요한 경험)을 가지고있다.
- 해당 논문에서는 Sensors을 Emotion, Motivation, Oppotunity 3가지로 나눴다. Emotion agent가 다른 agent한테 가진 감정, Motivation은 동기, Oppotunity는 알리바이가 있는지 아닌지로 나뉜다. Agent는 각 정보를 입력으로 받고 점수로 변환하여 LLM에 입력으로 넣어 좀더 수상한 사람과 할 질문을 유동적으로 결정한다. 또한 Pruner을 통해서 질문할 Agent와 질문 할 대상의 list를 계속 update한다
    
    ![Screenshot 2024-09-09 at 20.29.05.png](PLAYER%200b6ecef879254060a11f42abd79286d5/Screenshot_2024-09-09_at_20.29.05.png)
    
- 또한 좀더 원할한 게임을 위해서 저자들은 WELLPAY라는 데이터셋을 conan에 데이터셋에서 만들었다. 데이터셋은 아래와 같은 3개의 정보를 담는다. 1) 중요 인물 2) 이유 3) 관계등이 있다.

![Screenshot 2024-09-09 at 20.14.33.png](PLAYER%200b6ecef879254060a11f42abd79286d5/Screenshot_2024-09-09_at_20.14.33.png)

- 평가는 마지막에 얻은 총 리워드 포인트/전체 포인트로 나뉜다. 여기서 포인트는 대답에 대한 얻을수있는 정보에 대한 점수다.

### Main result

- 모델에서는 GPT3.5를 이용했다.

![Screenshot 2024-09-09 at 20.30.42.png](PLAYER%200b6ecef879254060a11f42abd79286d5/Screenshot_2024-09-09_at_20.30.42.png)

- 따른 논문에서의 모델과 pp는 오직 agent의 script만 op는 전체 상황과 정보로 판단을 내린다. o-cot는 object COT다. agent가 목표에 따라 누구에 질문할지 생각하고 , reflect하고, 선택한다.

![Screenshot 2024-09-09 at 20.31.13.png](PLAYER%200b6ecef879254060a11f42abd79286d5/Screenshot_2024-09-09_at_20.31.13.png)

- 라운드에 따른 제한된 질문을 할수있는 agent의 성능
    
    ![Screenshot 2024-09-09 at 20.35.41.png](PLAYER%200b6ecef879254060a11f42abd79286d5/Screenshot_2024-09-09_at_20.35.41.png)