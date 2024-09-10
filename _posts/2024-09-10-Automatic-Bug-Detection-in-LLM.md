---
layout: post
tags: [review]
---

# Automatic Bug Detection in LLM

> Automatic Bug Detection in LLM-Powered Text-Based Games Using LLMs
> 

> Released : 2024 09 10
> 

> Review Date : 2024 06 10
> 

<aside>
💡

LLM 생성형 텍스트 게임을 위한 버그 찾는 방법

</aside>

### Motivation

- 최근 LLM의 성능이 올라 이를 통한 게임 제작이 시도되고있다. 하지만 LLM의 hallucinations과 기억 문제, prompt등 오해등 많은 문제인해서 게임에 버그 혹은 이상한 log가 이상하게 나오는 문제가있다. 이 논문에서는 해당 문제를 즉 버그를 찾는 방법을 제안한다.

### Methodology

- 본 논문에서는 Dejaboom!이라는 LLM 생성형 게임을 주력 게임으로 선택했다. Dejaboom의 게임이 끝날때까지 나온 모든 텍스트와 게임의 로직(규칙 및 설명)을 모델에 입력으로 넣어 한줄로 요약한다.(매 step을 요약, 한꺼번에 요약하지 않음).

![Screenshot 2024-09-10 at 12.36.16.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Automatic%20Bug%20Detection%20in%20LLM%2083194a2154cf4f63bd136de6261fea2f/Screenshot_2024-09-10_at_12.36.16.png?raw=true)

![Screenshot 2024-09-10 at 12.41.59.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Automatic%20Bug%20Detection%20in%20LLM%2083194a2154cf4f63bd136de6261fea2f/Screenshot_2024-09-10_at_12.41.59.png?raw=true)

- 해당 요약들을 모델은 다음 step에 얼마나 영향을 악영향을 끼쳤는지 에따라 점수를 매겨 낮은 점수 그리고 이상할 정도로 높은 점수를 가진 step들을 표시한다.

![Screenshot 2024-09-10 at 12.44.49.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/Automatic%20Bug%20Detection%20in%20LLM%2083194a2154cf4f63bd136de6261fea2f/Screenshot_2024-09-10_at_12.44.49.png?raw=true)

### Main result

- 모델은 올바른 버그들이 낮은 점수 (20 프로 이하)를 매기고있다. 예를 들어 npc한테 스토리 진행을 위한 도구를 받을려고할때 알수없는 물체를 가져오라는 요구나 스토리에 아무런 도움이 안되는 npc가 갑자기 나타난다던가 하는 상황에 제대로 점수를 매긴다.
