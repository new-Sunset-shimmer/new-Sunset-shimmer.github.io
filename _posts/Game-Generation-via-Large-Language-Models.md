# Game Generation via Large Language Models

> Game Generation via Large Language Models
> 

> Released : 2024 05 30
> 

> Review Date : 2024 09 09
> 

<aside>
💡

LLM을 통한 게임 생성

</aside>

### Motivation

- 최근 LLM이 콘텐츠 생성기로서 성공적이다.  Angry bird, Super mario 같은 게임들을 생성을 했다. 하지만 대부분의 논문은 게임의 룰, 레벨을 동시에 생성하지 않는다. 그렇기에 이번 논문에서는 LLM-based framework to generate games(LLMGG)를 제안했다

### Research questions

- LLM은 게임을 올바르게 생성할수있나.

### Methodology

- 본 논문에서는 룰과 레벨을 제공할시 게임으로 바꿔주는 모델인 Vide Game Description Language(VGDL)을 활용한다. 모델이 해당 VGDL에 유저의 prompt와 규칙을 기반으로 룰과 레벨을 생성한다. 마지막에 해당 출력물들을 기반으로 게임을 생성한다.

![Screenshot 2024-09-09 at 18.34.10.png](Game%20Generation%20via%20Large%20Language%20Models%20cd019c50e4584d69810f8458c4b17bc6/Screenshot_2024-09-09_at_18.34.10.png)

- 프롬프트 디자인. <context>는 게임 생성을 위한 설명들이 들어간다. EX) VGDL 문법과 sprite encoding 방법. <game>는 사용자가 원하는 게임 이름을 넣는다. <context>가 제대로 주워졌으며 모델이 임의로 정할수도있다.
- 모델에 prompt를 넣을 때 어떤 경우에 더 잘 만드는지를 확인 하기위해서 총 7개의 prompt가 존재한다. C1은 killSprite으로, C2는 RemoveSprite다.

![Screenshot 2024-09-09 at 18.39.31.png](Game%20Generation%20via%20Large%20Language%20Models%20cd019c50e4584d69810f8458c4b17bc6/Screenshot_2024-09-09_at_18.39.31.png)

![Screenshot 2024-09-09 at 18.42.19.png](Game%20Generation%20via%20Large%20Language%20Models%20cd019c50e4584d69810f8458c4b17bc6/Screenshot_2024-09-09_at_18.42.19.png)

- Base = VGDL 기본 문법, C1 collision 설정, Example 예시다.

### Main result

- Gemma 7B, GPT-4, GPT-3.5를 모델 각각 prompt를 입력시 완벽하게 게임을 생성하는 건 GPT-4와 prompt 7 말고는 없었다.

![Screenshot 2024-09-09 at 18.47.46.png](Game%20Generation%20via%20Large%20Language%20Models%20cd019c50e4584d69810f8458c4b17bc6/Screenshot_2024-09-09_at_18.47.46.png)

- 또한 hallucination 문제에 인해서 게임의 로직에 버그가 나는 경우 있다. 저자들은 incomplete interaction, wrong interaction sentence 로 나눴다. incomplete interaction은 게임의 collision이 잘못된 경우다. 예로 플레이어와 벽이 겹칠경우 후속 행동이 정해지지 않았은 경우다.  wrong interaction sentence 문법적인 오류다. 모델에서는 killSprite으로 prompt를 입력할 경우 goal 도달한 플레이어의 sprite가 삭제 되는 버그를 발견했다. 이유는 모델이 생성한 룰에 goal player  → killSprite가 아닌 player goal  → killSprite로 되어있는걸 확인했다. 또한 위의 문제를 스스로 고치기 힘들어 하는 것도 확인했다.
- 저자들이 player goal  → killSprite가 뭔지 모델에 물어봤다.
    
    ![Screenshot 2024-09-09 at 19.08.08.png](Game%20Generation%20via%20Large%20Language%20Models%20cd019c50e4584d69810f8458c4b17bc6/Screenshot_2024-09-09_at_19.08.08.png)
    
- 위의 문제를 해결화기 위해서 저자들은 RemoveSprite로 prompt를 바꿨다. 위에서 player goal  → killSprite 로 바뀐이유를 subject-verb-object or subject-object-verb 순으로 LLM이 자연어를 이해하기에 내부에서 바뀐게 아닌가 추측한다. 또한 remove로 바꿀시 버그가 fix된 이유는 kill을 object로 이해하는게 아닌가 하는 개인적인 의건도 있다.