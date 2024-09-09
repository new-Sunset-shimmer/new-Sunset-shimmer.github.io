# MoRA: High-Rank Updating for Parameter-Efficient Fine-Tuning (1)

[https://arxiv.org/pdf/2405.12130](https://arxiv.org/pdf/2405.12130)

> 스터디용 리뷰
> 

> 발표 날짜 : 2024-05-27
> 

<aside>
💡 새로운 PEFT방식 제안

</aside>

### 문제

- 최근 나오는 모델들은 많은 파라미터수를 가지고있다. 이러한 모델들을 효과적으로 학습하는 방법으로 PEFT를 사용한다. 가정 유면한 방법은 LoRA이지만 저자들은 LoRA가 정보를 “기억(memorize)”할수없기에 성능적으로 문제가 있다고 지적했다. 저자들은 Low rank들을 생성하지 않고 처음부터 하나의 큰 매트릭스로 학습한다.

---

### 가정

- LoRA가 정보를 “기억(memorize)”할수없기에 문제가 있다고 지적했다. 저자들은 Low rank들을 생성하지 않고 처음부터 하나의 큰 매트릭스로 학습한다.

---

### 방법

- 해당 논문에서는 **MoRA**라는 새로운 방식을 제안했다.
    - MoRA : LoRA는 r x k 차원을 가진 행렬 2개를 만드는 방식이라면 MoRA는 r x r 행렬을 만들어 학습한다. function comp는 d x k 차원의 입력을 d x r 차원으로 맵핑하며. function decomp은 d x r차원을 d x r차원으로 맵핑시키는 방식이다.
        
        ![Screenshot 2024-05-27 at 20.51.07.png](https://raw.githubusercontent.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/master/_posts/MoRA%20High-Rank%20Updating%20for%20Parameter-Efficient%20Fi%2048d72388289e43f4bcacbc393e821890/Screenshot_2024-05-27_at_20.51.07.png?raw=true)
        
        ![Screenshot 2024-05-27 at 20.45.48.png](https://raw.githubusercontent.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/master/_posts/MoRA%20High-Rank%20Updating%20for%20Parameter-Efficient%20Fi%2048d72388289e43f4bcacbc393e821890/Screenshot_2024-05-27_at_20.45.48.png?raw=true)
        

---

### 평가

![Screenshot 2024-05-27 at 20.51.51.png](https://raw.githubusercontent.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/master/_posts/MoRA%20High-Rank%20Updating%20for%20Parameter-Efficient%20Fi%2048d72388289e43f4bcacbc393e821890/Screenshot_2024-05-27_at_20.51.51.png?raw=true)

![Screenshot 2024-05-27 at 20.51.35.png](https://raw.githubusercontent.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/master/_posts/MoRA%20High-Rank%20Updating%20for%20Parameter-Efficient%20Fi%2048d72388289e43f4bcacbc393e821890/Screenshot_2024-05-27_at_20.51.35.png?raw=true)

![Screenshot 2024-05-27 at 20.52.18.png](https://raw.githubusercontent.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/master/_posts/MoRA%20High-Rank%20Updating%20for%20Parameter-Efficient%20Fi%2048d72388289e43f4bcacbc393e821890/Screenshot_2024-05-27_at_20.52.18.png?raw=true)

![Screenshot 2024-05-27 at 20.52.40.png](https://raw.githubusercontent.com/new-Sunset-shimmer/new-Sunset-shimmer.github.io/master/_posts/MoRA%20High-Rank%20Updating%20for%20Parameter-Efficient%20Fi%2048d72388289e43f4bcacbc393e821890/Screenshot_2024-05-27_at_20.52.40.png?raw=true)

### 결론

- 새로운 MoRA라는 방식을 제안. 기존에 없던 외우는 방식으로 PEFT가능하다.

---
