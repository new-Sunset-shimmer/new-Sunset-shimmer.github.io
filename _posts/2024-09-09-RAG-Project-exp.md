---
layout: post
tags: [explanation]
---

# 구현 설명

- [ ]  크롤링
    - [ ]  CRAWLER
    - [ ]  DB
- [ ]  LLM Chat
    - [ ]  Retriever model
    - [ ]  Langchain
    - [ ]  Instruction tuning
    - [ ]  OUTPUT
- [ ]  [BONUS]
- [ ]  시현

Disclaimer!!!!!!: all of my code based on https://github.com/Wilson-ZheLin/GPT-4-Web-Browsing

# 크롤링

![crawler_new (1).png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/%E1%84%80%E1%85%AE%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%86%E1%85%A7%E1%86%BC%2018cbfd759a094c27a2ed85c806821a67/crawler_new_(1).png?raw=true)

### CRAWLER

Spider을 통해서 url들을 돌아 다니면서 HTML파일들을 가져오고 SCRAPER을 통해서 HTML 파일에서 필요한 정보들을 가져와 DATABASE에 저장한다. requests와 BeautifulSoup로 간단하게 Scraper와 Crawler을 구현. DB는 관리가 쉬우며 동시에 여러 기기로 쉽게 옮길수있는 Langchain에 제공되고있는 Chroma를 썼다. 

### DB

Chroma: 

![chroma-logo-FB287847E7-seeklogo.com.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/%E1%84%80%E1%85%AE%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%86%E1%85%A7%E1%86%BC%2018cbfd759a094c27a2ed85c806821a67/chroma-logo-FB287847E7-seeklogo.com.png?raw=true)

Chroma는 오픈 소스 임베딩 데이터베이스입니다. Chroma를 사용하면 지식, 사실, 기술을 LLM에 플러그할 수 있게 만들어 LLM 앱을 쉽게 구축할 수 있습니다. 출처:[🏡 Home | Chroma (trychroma.com)](https://docs.trychroma.com/)

![hrm4.svg](https://raw.githubusercontent.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/b2bc7f4a31a9162e4efa96a19315caea88b6b0b2/_posts/%E1%84%80%E1%85%AE%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%86%E1%85%A7%E1%86%BC%2018cbfd759a094c27a2ed85c806821a67/hrm4.svg)

문서들을 Vector로 Embedding 하여 저장하고 Retrieve할 때도 Query를 Embedding하여 vector기반으로 관련이 높은 문서들을 찾는다. Embedding을 즉 Vector을 만들때 인공지능 모델을 쓰는 경우가 있으며 https://github.com/ssisOneTeam/Korean-Embedding-Model-Performance-Benchmark-for-Retriever 의 성능 지표를 따라 가장 성능이 높은 jhgan/ko-sroberta-multitask을 쓰고 있다.

## LLM Chat

![LLM.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/%E1%84%80%E1%85%AE%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%86%E1%85%A7%E1%86%BC%2018cbfd759a094c27a2ed85c806821a67/LLM.png?raw=true)

### Retriever Model

앞서 설명한 langchain에서 Chroma DATABASE를 그대로 Retriever Model로서 쓸수있다. Query를 Vector로 변환시키고 그 Vector와 가장 유사한 TOP k개의 문서들을 찾는다. 

### LLM

LLM은 대규모 언어 모델이다. Query와 TOP K의 문서들을 기반으로 정보를 제공한다. 정보를 제공하기 위해서 IN-CONTEXT LEARNING으로 TEMPLATE을 작성하여 PROMPT TUNING으로 성능을 올리려고한다.

### PROMPT TUNING

PROMPT TUNING을 위한 TEMPLATE은 아래와 같은 방식으로 작성했다.

### GEMINI, GPT4o

```yaml
template: |
  Web search result: {context_str}

  Personal Information: {information}

  Instructions: You are a/an {profile}. Using the provided web search results and Personal Information, write a comprehensive and detailed but not long text reply to the given query. 
  Make sure to cite results using [number] notation after the reference.
  At the end of the answer, list the corresponding references with indexes, each reference contains the urls and quoted sentences from the web search results by the order you marked in the answer above and these sentences should be exactly the same as in the web search results.
  We also provide a Personal Information, if user ask any self information then provide from Personal Information and dont reference any link.
  Here is an example of a reference:
  [1] URL: https://www.pocketgamer.biz/news/81670/tencent-and-netease-dominated-among-chinas-top-developers-in-q1/
  self information example:
          학번, 이름, 학과, 성적
  Answer in language: {language}
  Query: {query}
  Output Format: {format}
  Please organize your output according to the Output Format. If the Output Format is empty, you can ignore it.
key_word_template: |
  Instructions: You are a/an {profile}. Make keyword of {query} constucted by under 5 tokens. Keyword mustn't be answer of {query}. Keyword must be main context of {query} and must understandable by human.
  There is no need to add another text with keyword when answering. Answering only keyword. 
  Here is an examples of how to make keyword:
    Question -> keyword
      [1] 전북대학교 컴퓨터공학부 졸업 조건을 알려줘 -> 컴퓨터공학부 졸업 조건
      [2] 지금 휴학 가능한가요? 일반휴학입니다 -> 휴학 
      [3] 자퇴신청 어제 까진데 또 언제할수있음? -> 자퇴신청 일정
      [4] 전북대학교 영어영문학과 졸업 조건을 알려줘 -> 영어영문학과 졸업 조건
      [5] 내 학번을 알려줘 -> 학번 질문
  Answer in language: {language}
  Query: {query}
  Output Format: {format}
search_template: |
  Instructions: please classify(labeling) {query} by comprehensive enough to answer. 1 is label of comprehensive enough and 2 is NOT comprehensive enough.
  Here is an examples of sentences labeled for help to classify. but not too srictly classify to 2:
    query : label
      저학년이 고학년 벨트 미리 딸 수 있나요 제가 2학년인데요 : 1
      교수님께서 lms에서 개인별 성적란 누르면 확인된다하셨는데 성적누르면 해당 강의에 대한 성적은 비공개되어 확인할 수 없습니다라고 떠요 어디서 볼수있나요? 메일 드려야하나요? : 1  
      과목코드는 어디서 봐? : 2 
      대학생활계획서는 어디서 써? : 2    
      전진설 4번 하는거 2,3 학년말고 4학년에 하는것도 포함해서 4번하는건가요? 2,3학년에 3번하고 4학년에 1번해도 되는건가요? : 2
      중도 앞에서 오늘 행사 함? : 1
      오늘 휴학 가능할까? : 1
      링크를 요약해줘 : 1
  Answer in language: {language}
  Query: {query}
  Output Format: [
                class of sentence :
                Reason: 
                Solution:
                 ]
```

### Llama3, other local LLM

```yaml
template: |
  <|begin_of_text|>
  <|start_header_id|>
    system
  <|end_header_id|>
      Web search result: {context_str}

      Personal Information: {information}

      Instructions: You are a/an {profile}. Using the provided web search results and Personal Information, write a comprehensive and detailed reply to the given query. 
      Make sure to cite results using [number] notation after the reference.
      At the end of the answer, list the corresponding references with indexes, each reference contains the urls and quoted sentences from the web search results by the order you marked in the answer above and these sentences should be exactly the same as in the web search results.
      We also provide a Personal Information, if user ask any self information then provide from Personal Information and dont reference any link.
      Here is an example of a reference:
      [1] URL: https://www.pocketgamer.biz/news/81670/tencent-and-netease-dominated-among-chinas-top-developers-in-q1/
          Quoted sentence: Tencent accounted for roughly 50% of domestic market revenue for the quarter, compared to 40% in Q1 2022.
      self information example:
          학번, 이름, 학과, 성적
      Answer in language: {language}
      Output Format: {format}
      Please organize your output according to the Output Format. If the Output Format is empty, you can ignore it.
  <|eot_id|>
  <|start_header_id|>
      user
  <|end_header_id|>
    Query: {query}
    Answer in language: {language}
  <|eot_id|>
  <|start_header_id|>
    assistant
  <|end_header_id|>
  
key_word_template: |
  <|begin_of_text|>
  <|start_header_id|>
    system
  <|end_header_id|>
      Instructions: You are a/an {profile}. Make keyword of {query} constucted by under 5 tokens. Keyword mustn't be answer of {query}. Key word must be main context of {query} and must understandable by human.
      There is no need to add another text with keyword when answering. Answering only keyword. 
      Here is an examples of how to make keyword:
        Question -> keyword
          [1] 전북대학교 컴퓨터공학부 졸업 조건을 알려줘 -> 컴퓨터공학부 졸업 조건
          [2] 지금 휴학 가능한가요? 일반휴학입니다 -> 휴학 
          [3] 자퇴신청 어제 까진데 또 언제할수있음? -> 자퇴신청 일정
          [4] 전북대학교 영어영문학과 졸업 조건을 알려줘 -> 영어영문학과 졸업 조건
          [5] 내 학번을 알려줘 -> 학번 질문
      Answer in language: {language}
      Output Format: {format}
  <|eot_id|>
  <|start_header_id|>
      user
  <|end_header_id|>
    Query: {query}
  <|eot_id|>
  <|start_header_id|>
    assistant
  <|end_header_id|>
```

### OUTPUT EXAMPLE

### GPT4o’s EXAMPLE OUTPUT

```jsx
Q:계절학기 수강신청 완료 확인 지금 안되는 거 맞아?? 취업교과목 하나 신청 했는데 안뜨는 것 같아서

A:{'answer': '계절학기 수강신청 완료 확인이 안 되는 상황에 대해 걱정이 많으시겠네요. 전북대학교의 계절학기 수강신청 관련 정보를 참고하여 답변드리겠습니다.\n\n전북대학교의 계절학기 수강신청은 보통 5월과 11월 초순에 진행됩니다. 수강신청 후 수강료 납부, 폐강과목 공고 및 수강정정 등의 절차가 이어집니다. 수강신청 인원이 30명 이상인 경우에만 강의가 개설되며, 전공과목의 경우 20명 이상으로 변경되었습니다[1][2].\n\n수강신청이 완료되었는지 확인하려면, 전북대학교 오아시스 시스템에 로그인하여 수강신청 내역을 확인해보시기 바랍니다. 만약 수강신청 내역이 보이지 않는다면, 수강신청이 제대로 완료되지 않았을 가능성이 있습니다. 이 경우, 학교의 학사관리팀이나 해당 학부에 문의하여 확인하는 것이 좋습니다.\n\n취업교과목을 신청했는데 수강신청 내역에 나타나지 않는다면, 다음과 같은 절차를 따라 확인해보세요:\n1. 오아시스 시스템에 로그인합니다.\n2. 수강관리 메뉴에서 수강신청 내역을 확인합니다.\n3. 수강신청이 제대로 완료되지 않았다면, 수강신청 기간 내에 다시 시도하거나 학사관리팀에 문의합니다.\n\n추가로, 계절학기 수강신청 관련 공지사항을 확인하여 변경된 사항이나 추가 안내를 참고하시기 바랍니다[3].\n\n참고로, 전북대학교의 계절학기 수강신청 관련 주요 내용은 다음과 같습니다:\n- 수강자격: 본교 및 협력대학 재학생, 본교 휴학생\n- 신청학점: 6학점 이내\n- 개설과목: 교양과목 및 전공과목\n- 설강기준: 수강신청 인원 30명 이상 (전공과목은 20명 이상)\n- 수업시간: 3학점의 일반강의 과목 기준으로 1주당 15시간 (총 3주 45시간)[1][2].\n\n이 정보를 바탕으로 수강신청 내역을 다시 확인해보시고, 문제가 지속될 경우 학교에 문의하시기 바랍니다.\n\n### References\n[1] URL: https://www.jbnu.ac.kr/kor/?menuID=139&category=%EA%B8%B0%ED%83%80&pno=2388\n    Quoted sentence: "수강자격 : 본교 및 협력대학 재학생, 본교 휴학생(단, 휴학생의 경우 당해학기 계절수업을 이수하여 졸업학점을 충족하더라도 졸업은 불가)"\n[2] URL: https://www.jbnu.ac.kr/kor/?menuID=140&category=%EA%B5%90%EC%9B%90%EC%B1%84%EC%9A%A9&pno=204\n    Quoted sentence: "설강기준 : 수강신청 인원 30명 이상인 경우 설강"\n[3] URL: https://top.jbnu.ac.kr/bbs/bie/1180/301133/artclView.do?layout=unknown\n    Quoted sentence: "계절수업 개설 기준 인원이 변경되었으니 확인하시어 신청 시 참고하여 주시면 감사하겠습니다. 전공과목 : 30명 → 20명 교양과목 : 30명 유지"\n', 'keyword': '계절학기 수강신청 확인'}
```

```jsx
Q:전북대 홈페이지에 5월 1일에 중간고사 완료라고 뜨는데.. 정확히 며칠부터 중간고사 기간인가요?

A:{'answer': '전북대학교의 학사일정에 따르면, 중간고사 기간은 명확히 명시되어 있지 않습니다. 다만, 중간고사 완료일은 5월 1일로 표시되어 있습니다[5]. 일반적으로 중간고사는 학기 중반에 시행되며, 보통 8주차에 시작됩니다. 따라서, 중간고사 기간은 4월 중순부터 5월 초까지로 예상할 수 있습니다.\n\n### References\n[5] URL: https://likehome.jbnu.ac.kr/home/main/inner.php?sMenu=D5000&mode=view&no=5349\n    Quoted sentence: "전북대학교 학사일정에 시험기간이라는 것은 명시 되어있지 않습니다. 그렇기 때문에 야간출입허용기간은 학사일정에 명시되어있는 중간시험 종료를 기점으로 합니다."\n', 'keyword': '중간고사 기간'}
```

### GEMINI’s EXAMPLE OUTPUT

```
Q:전북대학교 컴퓨터공학부 졸업 조건을 알려줘

A:전북대학교 컴퓨터공학부 졸업 조건은 다음과 같습니다.

1. 총 130학점 이상 이수
2. 전공 필수 과목 42학점 이상 이수
3. 전공 선택 과목 24학점 이상 이수
4. 교양 과목 30학점 이상 이수
5. 실무 훈련 6학점 이상 이수
6. 졸업 논문 6학점 이상 이수

[1] 2015년 : 전북대 IT정보공학과 공학 학사
2017년 : 전북대 컴퓨터공학과 공학 석사
2017년 ~ 현 : 전북대 컴퓨터공학과 박사과정
관심 분야: privacy-preserving, cloud computing, DNN
2019년 ~ 현 : IT정보공학과 학부 과정
관심 분야: cloud computing, AI, DNN
```

```
Q:나의 학과는 뭐임?

A:컴퓨터인공지능학과 [1]

[1] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=47&number=9 Quoted sentence: The Journal of Korean Institute of Communications and Information Sciences
```

### Llama3 8B 4bit-Quant instruct

```
Q:전북대학교 컴퓨터공학부 졸업 조건을 알려줘

A:According to the provided web search results, the graduation requirements for Jeonbuk National University's Computer Science Department are as follows:

Firstly, it is mentioned that the department has a comprehensive design project development requirement for graduates. This suggests that students must complete a significant project that showcases their skills and knowledge in computer science.

Secondly, the department's webpage [2] states that the graduation requirements include satisfying the credits required for the degree, which implies that students must complete a certain number of credits or courses to graduate.

Lastly, the department's webpage [3] and [4] mention that visitors are welcome to explore the department's website, but it does not provide specific information on the graduation requirements. However, it is likely that the department has its own set of guidelines and regulations for graduation.

As a conscientious researcher, I would recommend checking with the university or the department directly for the most up-to-date and accurate information on the graduation requirements.

References: [2] URL: https://it.jbnu.ac.kr/it/14016/subview.do Quoted sentence: 전북대학교 학수구분별 졸업학점

[3] URL: https://cse.jbnu.ac.kr/cse/3577/subview.do Quoted sentence: 컴퓨터공학부를 방문해주신 고등학생, 학부모, 입시담당 선생님께 감사드립니다.

[4] URL: https://csai.jbnu.ac.kr/cse/3577/subview.do Quoted sentence: 컴퓨터공학부를 방문해주신 고등학생, 학부모, 입시담당 선생님께 감사드립니다.
```

```
Q:나의 학과는 뭐임?

A:Your department is 컴퓨터인공지능학과 (Computer and Information Sciences). According to your personal information, you are a student of this department.

References: [None]
```

### maywell/Llama-3-Synatra-11B-v1

```
Q:전북대학교 컴퓨터공학부 졸업 조건을 알려줘

A:전북대학교 컴퓨터공학부 졸업 조건은 다음과 같아요. 먼저, 전북대 컴퓨터공학부에서는 졸업 조건을 만족하기 위해 학생들은 종합설계 작품을 개발해야 해요. 이 작품은 학생들이 학습한 지식과 기술을 실제 문제 해결에 적용하는 것을 목표로 하며, 이를 통해 학생들은 실제 세계의 문제를 해결할 수 있는 능력을 키울 수 있어요.
또한, 졸업 조건을 만족하기 위해서는 학생들은 졸업자격 인증제 이수기준을 만족해야 해요. 이 기준은 학생들이 전북대에서 취득한 학점을 기반으로 하며, 이를 통해 학생들이 전북대에서 충분한 학업을 이수했는지를 판단해요.
전북대 컴퓨터공학부 졸업 조건은 이러한 두 가지 요소를 포함하며, 학생들이 실제 세계의 문제를 해결할 수 있는 능력을 키울 수 있도록 도와줘요. 더 궁금한 점이 있으면 언제든지 물어봐 주세요!
References: [1] [https://sites.google.com/view/jbnu-dblab/people](https://sites.google.com/view/jbnu-dblab/people) [2] [https://it.jbnu.ac.kr/it/14016/subview.do](https://it.jbnu.ac.kr/it/14016/subview.do) [3] [https://cse.jbnu.ac.kr/cse/3577/subview.do](https://cse.jbnu.ac.kr/cse/3577/subview.do) [4] [https://csai.jbnu.ac.kr/cse/3577/subview.do](https://csai.jbnu.ac.kr/cse/3577/subview.do) [5] [https://csai.jbnu.ac.kr/csai/29037/subview.do](https://csai.jbnu.ac.kr/csai/29037/subview.do)
```

```
Q:나의 학과는 뭐임?

A:컴퓨터인공지능학과에요. [1] [2] [3] [4] [5] [6]
```

### Mistral instruct 7B 4bit-Quant instruct

```
Q:전북대학교 컴퓨터공학부 졸업 조건을 알려줘

A:전북대학교 컴퓨터공학부에서의 졸업 조건에 대해 알아드리겠습니다. according to the provided web search results and Personal Information.

First, let me clarify that there are two different programs within the Computer Science Department at Jeonbuk National University: the Computer Science program ([Webpage[3], [Webpage[4]]) and the Computer Engineering and Artificial Intelligence program ([Webpage[5]]). Based on the Personal Information provided, you are enrolled in the Computer Engineering and Artificial Intelligence program.

Now, let's discuss the general graduation requirements for Jeonbuk National University students as mentioned in [Webpage[2]]. To graduate, a student must earn the required number of credits, obtain the necessary certifications, complete a comprehensive design project, and meet other specific departmental requirements. However, the details of these requirements may vary depending on the specific program.

For Computer Engineering and Artificial Intelligence students, the focus areas are privacy-preserving, cloud computing, and deep neural networks (DNN) as per [Webpage[1]]. It is essential to note that the Personal Information provided indicates that you are currently pursuing a Bachelor's degree in IT Information Science within this department. The graduation requirements for undergraduate students may differ from those of graduate students.

To summarize, the specific graduation requirements for Computer Engineering and Artificial Intelligence students at Jeonbuk National University can be found by consulting the departmental guidelines or speaking with an academic advisor. These requirements likely include earning a certain number of credits, completing specific coursework in privacy-preserving, cloud computing, DNN, and other related fields, and potentially completing a thesis or capstone project.

References: [1] URL: https://sites.google.com/view/jbnu-dblab/people Quoted sentence: 관심 분야: privacy-preserving, cloud computing, DNN

[2] URL: https://it.jbnu.ac.kr/it/14016/subview.do Quoted sentence: 졸업자격인증 인증구분별 졸업학점 (2024.2월 졸업자까지만 적용)

[3] URL: https://csai.jbuku.ac.kr/cse/3577/subview.do Quoted sentence: 컴퓨터공학부를 방문해주신 고등학생, 학부모, 선배 여러분 감사합니다.

[4] URL: https://csai.jbuku.ac.kr/cse/3577/subview.do Quoted sentence: 컴퓨터공학과에서는 컴퓨터 공학, 소프트웨어 공학, 인форма션 통신 공학 등 다양한 분야로부터 전문가를 양성하고 있습니다.

[5] URL: https://csai.jbuku.ac.kr/cse/3577/subview.do Quoted sentence: 컴퓨터공학과에서는 인간-컴퓨터 인터페이스, 데이터베이스 시스템, 네트워크 기술, 운영 체제, 소프트웨어 공학, 컴퓨터 구조와 설계, 인공지능, 데이터 분석 등 다양한 분야에서 전문가를 양성합니다.
```

```
Q:나의 학과는 뭐임?

A:당신의 학과는 "컴퓨터인공지능학과"입니다. [1] 이 학과에서 최근에 발표된 논문들에 대해 소개하겠습니다.

[1] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5 Quoted sentence: In this issue, we present papers on various topics including a new energy-based latent-variable model for unsupervised feature learning [2], jamming detection algorithm using the kurtosis of the received signal [3], rate-matching precoder design based on rate-splitting multiple access for heterogeneous traffic demands [4], performance evaluation for UAV-Based UL-NOMA with channel coding [5], performance analysis of signal source direction finding algorithms for PS-LTE networks [6], hybrid beamforming in Sub-THz LoS MIMO systems [7], on uplink radio resource allocation for prioritized two-step random access [8], development of robust crack segmentation and thickness measurement model using deep learning [9], domain adaptive deep learning-based crack detection for building inspection [10].

References: [1] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5 Quoted sentence: In this issue, we present papers on various topics including a new energy-based latent-variable model for unsupervised feature learning [2], jamming detection algorithm using the kurtosis of the received signal [3], rate-matching precoder design based on rate-splitting multiple access for heterogeneous traffic demands [4], performance evaluation for UAV-Based UL-NOMA with channel coding [5], performance analysis of signal source direction finding algorithms for PS-LTE networks [6], hybrid beamforming in Sub-THz LoS MIMO systems [7], on uplink radio resource allocation for prioritized two-step random access [8], development of robust crack segmentation and thickness measurement model using deep learning [9], domain adaptive deep learning-based crack detection for building inspection [10].

[2] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5 Quoted sentence: A New Energy-Based Latent-Variable Model for Unsupervised Feature Learning

[3] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5 Quoted sentence: Jamming Detection Algorithm Using the Kurtosis of the Received Signal

[4] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5 Quoted sentence: Rate-Matching Precoder Design Based on Rate-Splitting Multiple Access for Heterogeneous Traffic Demands

[5] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5 Quoted sentence: Performance Evaluation for UAV-Based UL-NOMA with Channel Coding

[6] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=47&number=6 Quoted sentence: Performance Analysis of Signal Source Direction Finding Algorithms for PS-LTE Networks

[7] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5 Quoted sentence: Hybrid Beamforming in Sub-THz LoS MIMO Systems

[8] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5 Quoted sentence: On Uplink Radio Resource Allocation for Prioritized Two-Step Random Access

[9] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5 Quoted sentence: Development of Robust Crack Segmentation and Thickness Measurement Model Using Deep Learning

[10] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5 Quoted sentence: Domain Adaptive Deep Learning-Based Crack Detection for Building Inspection
```

### **phi3** 4B 4bit-Quant instruct

```
Q:전북대학교 컴퓨터공학부 졸업 조건을 알려줘

A:전북대학교의 컴퓨터공학부에서 졸업을 거래하는 조건은 다음과 같아요. [1] 생년도 2017년부터 전북대학교의 컴퓨터공학과 공학 강사로 이사하고, 그에 따라 전북대학교의 졸업 기계에서 공학 교육을 받아들여요. [2] 졸업자가 정보를 제공하면, 그는 전북대학교의 공학교육인지 검사하고, 공학 교육에서 확장되어질 수 있는 주제를 찾아내는 과정을 걸어요. [3] 전북대학교의 컴퓨터공학부를 방문하면, 졸업자가 전북대학교의 입학 본부로 이동하도록 하는 것을 기울시요. [4] 졸업자가 공학 교육에서 확장되어질 수 있는 주제를 찾아내는 과정은, 전북대학교의 공학 교육 기계에서 공학 교육을 받아들여요. [5] 전북대학교의 공학교육 기계는 공학 교육에 확장되어질 수 있는 주제를 찾아내고, 졸업자가 공학 교육에서 확장되어질 수 있는 주제를 찾아내는 과정을 걸어요.

이름: John Doe 학년도: Fall 2 Written by: David Sirota Published: April 1, 2009

The United States has a long history of using its military might to influence global politics and economics. From the Spanish-American War in 1898 to the invasion of Iraq in 2003, American interventionism has shaped the world we live in today. However, this approach is not without controversy. Critics argue that it often leads to unintended consequences and can undermine democratic values both at home and abroad.

One example of U.S. military intervention was the invasion of Iraq in 2003. The Bush administration justified this action by claiming that Saddam Hussein's regime possessed weapons of mass destruction (WMD) and posed a threat to regional stability. However, no WMD were ever found, leading many to question the true motives behind the invasion.

The consequences of U.S. interventionism are far-reaching. In some cases, it has led to increased instability in regions where American forces have been deployed. For example, after the 2003 Iraq War, sectarian violence escalated and contributed to the rise of extremist groups like ISIS. Additionally, U.S. military interventions can strain relationships with allies and create resentment among local populations who may view American forces as occupiers rather than liberators.

On the other hand, proponents argue that there are situations where U.S. intervention is necessary to protect human rights or promote democracy. For instance, during the Cold War, America's involvement in conflicts like Korea and Vietnam was seen by some as a way to contain communism and prevent its spread.

In conclusion, while American military intervention has played an important role in shaping global politics, it is essential for policymakers to carefully consider the potential consequences of such actions. Balancing national interests with respect for international norms and human rights should be at the forefront of any decision to deploy U. Written by: David Sirota Published: April 1, 2009

The United States has a long history of using its military might to influence global politics and economics. From the Spanish-American War in 1898 to the invasion of Iraq in 2003, American interventionism has shaped the world we live in today. However, this approach is not without controversy. Critics argue that it often leads to unintended consequences and can undermine democratic values both at home and abroad.

One example of U.S. military intervention was the invasion of Iraq in 2003. The Bush administration justified this action by claiming that Saddam Hussein's regime possessed weapons of mass destruction (WMD) and posed a threat to regional stability. However, no WMD were ever found, leading many to question the true motives behind the invasion.

The consequences of U.S. interventionism are far-reaching. In some cases, it has led to increased instability in regions where American forces have been deployed. For example, after the 2003 Iraq War, sectarian violence escalated and contributed to the rise of extremist groups like ISIS. Additionally, U.S. military interventions can strain relationships with allies and create resentment among local populations who may view American forces as occupiers rather than liberators.

On the other hand, proponents argue that there are situations where U.S. intervention is necessary to protect human rights or promote democracy. For instance, during the Cold War, America's involvement in conflicts like Korea and Vietnam was seen by some as a way to contain communism and prevent its spread.

In conclusion, while American military intervention has played an important role in shaping global politics, it is essential for policymakers to carefully consider the potential consequences of such actions. Balancing national interests with respect for international norms and human rights should be at the forefront of any decision to deploy U.S. forces abroad.

The following are some key points that can help in understanding the complexities of American interventionism:

Historical context: The United States has a long history of using its military power to influence global events, dating back to colonial times and continuing through various conflicts such as World Wars I and II, Korea, Vietnam, and more recent engagements like Afghanistan and Iraq. This historical perspective helps us understand the motivations behind U.S. interventionism and how it has evolved over time.

Motivations: The reasons for American military interventions vary widely, ranging from protecting national security interests to promoting democracy and human rights. However, critics argue that these justifications are often used as a cover for pursuing economic or geopolitical goals. Understanding the motivations behind U.S. interventionism can help us evaluate its effectiveness and potential consequences.

Consequences: The impact of American military interventions is complex, with both positive and negative outcomes. While some argue that interventions have helped promote democracy and protect human rights in certain cases, others point to the unintended consequences such as increased violence, political instability, and resentment towards U.S. forces among local populations.

International norms: The United States' approach to military interventionism has often been criticized for not aligning with international norms and laws governing the use of force. This includes issues such as sovereignty, proportionality, and accountability in cases where civilian casualties occur during conflicts.

Alternatives: Some scholars and policymakers advocate for alternative approaches to addressing global challenges that do not rely on military interventionism. These include diplomacy, economic development, and multilateral cooperation through international organizations like the United Nations. Evaluating these alternatives can help us better understand the role of U.S. interventionism in a broader context.

In summary, American interventionism is a complex issue with both supporters and critics. While it has played an important role in shaping global politics, policymakers must carefully consider its potential consequences and explore alternative approaches to addressing international challenges.
```

```
Q:나의 학과는 뭐임?

A:안녕하세요, 제 이름은 John Doe입니다. 나의 학과는 컴퓨터인공지능학과입니다. [1] 과제에 관한 내용을 찾아보세요.

[1] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 이 분야에서 나는 과제를 해보세요.

[2] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5: "Vol. 48, No. 5, May 2 Written in the year 1930 by a young man named John Doe, this letter was found among his personal belongings after he passed away. The letter reveals insights into his life and thoughts during that time period."

이렇게 생각했습니다. 1930년에 올해 생각한 소년이의 일상과 생각을 보여주는 이렇게 쓸 노약성적 편지를 찾아볼까요.

[3] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 이 분야에서 1930년대의 일상과 생각을 보여주는 편지를 찾아볼까요. Written in the year 1930 by a young man named John Doe, this letter was found among his personal belongings after he passed away. The letter reveals insights into his life and thoughts during that time period.

[4] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[5] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[6] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5: "Vol. 48, No. 5, May 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[7] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=48&number=5: "Vol. 48, No. 5, May 2 Written in the year 1930 by a young man named John Doe, this letter was found among his personal belongings after he passed away. The letter reveals insights into his life and thoughts during that time period."

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[8] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[9] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[10] URL: https://journal.kics.or.kr/journals/jkics/digital-library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[11] URL: https://journal.kics.or.kr/journals/jkics/digital_library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[12] URL: https://journal.kics.or.kr/journals/jkics/digital_library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[13] URL: https://journal.kics.oroe.kr/journals/jkics/digital_library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[14] URL: https://journal.kics.or.kr/journals/jkics/digital_library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[15] URL: https://journal.kics.or.kr/journals/jkics/digital_library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[16] URL: https://journal.kics.or.kr/journals/jkics/digital_library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[17] URL: https://journal.kics.or.kr/journals/jkics/digital_library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[18] URL: https://journal.kics.or.kr/journals/jkics/digital_library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[19] URL: https://journal.kics.or.kr/journals/jkics/digital_library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.

[20] URL: https://journal.kics.or.kr/journals/jkics/digital_library/publication?volume=47&number=6: "Vol. 47, No. 6, Jun. 2022"

이렇게 생각했습니다. 1930년대의 일상과 생각을 보여주는 이력을 찾아볼까요.
```

### [BONUS]

LLM을 통해서 QUERY를 행당 QUERY를 잘 설명하는 압축 문장으로 요약하여 저장합니다. 

EXAMPLE:

GEMINI:

나의 학과를 알려줘 → 학과 정보 

나의 학과를 알려줘 → 학과 확인

Llama 3B instruct:

나의 학과를 알려줘 → 학과

Mistral:

나의 학과를 알려줘 → 학과 (Department)

### [시현]

![2024-06-0202-33-41-ezgif.com-video-to-gif-converter.gif](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/%E1%84%80%E1%85%AE%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%86%E1%85%A7%E1%86%BC%2018cbfd759a094c27a2ed85c806821a67/2024-06-0202-33-41-ezgif.com-video-to-gif-converter.gif?raw=true)

![2024-06-0202-26-29-ezgif.com-video-to-gif-converter.gif](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/%E1%84%80%E1%85%AE%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%86%E1%85%A7%E1%86%BC%2018cbfd759a094c27a2ed85c806821a67/2024-06-0202-26-29-ezgif.com-video-to-gif-converter.gif?raw=true)

![Screenshot 2024-06-02 at 02.35.18.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/%E1%84%80%E1%85%AE%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%86%E1%85%A7%E1%86%BC%2018cbfd759a094c27a2ed85c806821a67/Screenshot_2024-06-02_at_02.35.18.png?raw=true)

![Screenshot 2024-06-02 at 02.35.26.png](https://github.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/blob/master/_posts/%E1%84%80%E1%85%AE%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%86%E1%85%A7%E1%86%BC%2018cbfd759a094c27a2ed85c806821a67/Screenshot_2024-06-02_at_02.35.26.png?raw=true)
