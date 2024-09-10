# Generative Agents

> Generative Agents: Interactive Simulacra of Human Behavior
> 

> released : 2023 8 6
> 

> written : 2024 9 9
> 

<aside>
💡

멀티 에이전트를 활용한 가상 시뮬레이션

</aside>

### Motivation

- 최근 LLM의 성능이 사람과 구별이 안될정도로 좋아졌다. LLM은 학습과정에서 사람의 행동 패턴또한 학습을 했다. 이번 논문은 그런 LLM으로 사람의 해동을 근사하여 사회에 같은 문제가 생겼을시 해결책 또는 문제점 올바르게 찾을수있는 기반을 마련한다고 생각한다. 그를 위해서 agent의 롤플레잉과 유사 사회를 기반으로한 가상세계 마을을 만들었다.
- 논문에서는 아래와 같은 기여를 한다고 소개한다.
    - 환경에 따른 인간같은 변화를 보이는 유동적인 Generative agent
    - 기억, 탐색(Retrieve), 반영(reflect), 다른 agent들과 소통 할수있는 아키텍쳐
    - end-to-end, 통제가능한 평가 방식

### Research questions

- Agent들을 어떻게 효과적으로 평가할것인지를

### Methodology

- 마을에는 총 25의 특별한 agent가 존재하며 각각 다른 스프라이트를 가진다. 저자들은 agent의 직업, 성격, 다른 agent들과의 관계등 여러 정보를 설정했다.

![Screenshot 2024-09-09 at 12.01.54.png](Generative%20Agents%20a294abb1ee404ad0978b7f6557187ef4/Screenshot_2024-09-09_at_12.01.54.png)

- Agent끼리는 자연어로 서로 의사소통한다. agent의 모든 행동은 자연어로 서술되며 게임에서는 이모티콘으로 표현된다. 위의 모든 해동은 agent의 아키텍쳐에 의해서 결정된다.
- 유저 컨트롤: 유저는 agent들과 자어로 의사소통이 가능하며 그들의 내부 생각을 출력으로 받을수있다.
- 주위의 모든 물체들은 자연어로 서술되어 agent에게 전달 된다. 가스레인지를 키는지 음식이 타는지도 서술되다. 파이프에서 물이 센다는 서술로 agent는 파이프를 고치는 행동도 가능하다. 물론 행동에 따른 결과도 실제 게임에 반영된다. 커피를 만드면 커피머신이 움직이고 원두가 떨어지면 원두가 없다는 표시도 떠야한다.
- information Diffusion : Agent들은 서로 대화하면서 정보를 다른 agent들에게 퍼트린다. 예: 선거에 대한 이야기를 다른 agent에게 들어 또 다른 agent에게 퍼트린다
- Relationship Memory : 다른 Agent의 행동 또는 말을 기억한다. 예를들어 사진 찍는 과제를 하는 Agent의 행동 혹은 말을 기억하여 나중에 다시 만났을 때 그에 대해 물어본다.
- Coordination : Agent끼리 서로 협력하기도 한다. 서로 초청하거나 놀러가거나 서로의 행동에 영향을 끼친다.

![Screenshot 2024-09-09 at 10.55.52.png](Generative%20Agents%20a294abb1ee404ad0978b7f6557187ef4/Screenshot_2024-09-09_at_10.55.52.png)

- Retrive : 메모리에 대화랑 올바른 기억을 가져오기 위해서는 기억들에 점수를 매긴다. recency 최신성, importance 중요도, relevance 연과도를 각각 점수를 매겨 가장 높은 점수의 정보를 가져온다. memry stream에는 reflection과 observation이 존재한다.

![Screenshot 2024-09-09 at 15.38.52.png](Generative%20Agents%20a294abb1ee404ad0978b7f6557187ef4/Screenshot_2024-09-09_at_15.38.52.png)

- Reflect : Agent는 이전의 reflection, plan, 관측된 정보들을 합산하여 하나의 고차원의 abstract한 정보로 압축한다. 해당 압축된 정보를 reflection이라고 부른다.

![Screenshot 2024-09-09 at 15.51.40.png](Generative%20Agents%20a294abb1ee404ad0978b7f6557187ef4/Screenshot_2024-09-09_at_15.51.40.png)

- plan : Agent는 사전에 어떤 활동을 할지 시간별로 정해둔다. 만약 논문을 쓸려는 경우 1시에서 부터 8시 까지 공부, 9시에 저녁, 10시에 잠 이런식으로 짤수있다. 중간 중간 plan을 바꿀수있다. 만약 공부하다가 다른 agent가 올시 질문, 대화를 할수있으며 대화의 결과 agent의 plan이 바뀔수있다.
- 평가 :  controlled evalutuoin은 인터뷰 형식으로 진행되며 여기서 planing, reflection 그리고 memory retrieving이 제한될수있다. 인터뷰에는 self-knowledge, memory, plan, Reactions, reflections(완벽한 reflection은 아님)이 존재한다.
    
    ![Screenshot 2024-09-09 at 16.07.50.png](Generative%20Agents%20a294abb1ee404ad0978b7f6557187ef4/Screenshot_2024-09-09_at_16.07.50.png)
    
- 평가 각각 아키텍쳐의 특정 기능을 제한했으며 Human Crowdworker은 인간을 agent로서 인터뷰한 기능이다. TrueSkill Rating은 Xbox Live 게임들에서 유저의 실력, 기능을 평가하는 지표다.

### Limitations

- 본 논문에서도 언급했듯이 이틀동안의 게임의 text들을 평가했다. Agent들은 처음에 룰을 잘다랐지만 게임 세계의 물리엔진에 인해서 생기는 비인간적인 행동들도 많았다. 예를 들어 화장실 칸에 여러명이 들어가거나 대부분의 agent들이 일과를 마친후 술집으로 자주 가기 시작했다던가.
- Agent들의 사전 정의가 그 Agent의 행동에 많은 영향을 끼쳤다. 예를 들어 발렌타인 파티를 개최를 정의한 Agent는 마을 공동체에 헌신했으며 거절을 잘하지 않았다

---