# SLICEGPT: COMPRESS LARGE LANGUAGE MODELS
BY DELETING ROWS AND COLUMNS

> 인턴용 리뷰
> 

> 발표 날짜 : 2024-07-03
> 

링크: [2401.15024 (arxiv.org)](https://arxiv.org/pdf/2401.15024)

깃허브 링크 : [microsoft/TransformerCompression: For releasing code related to compression methods for transformers, accompanying our publications (github.com)](https://github.com/microsoft/TransformerCompression)

<aside>
💡 LLM 경량화 방법에 대한설명

</aside>

### 문제

- LLM은 현재 자연어처리 분야의 가장 큰 근본이 되고있다. 하지마 LLM의 scaling-law으로 인해 나날이 사이즈가 늘어났다. 그로 인해 계산량 증가, 모델 사이즈가 증가하여 추가 저장장치 및 계산장치가 필요하다. 또한 많은 모델은 학습후에 sparse한 가중치를 가지고 있다.

![Screenshot 2024-07-14 at 13.21.59.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-14_at_13.21.59.png)

- scaling-law은 결국 모델의 가중치를 scaling 하는 행위다. 그렇기에 사이즈를 줄이기위해서는 모델의 가중치에 변화를 줘야한다

![Screenshot 2024-07-14 at 13.24.11.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-14_at_13.24.11.png)

---

### 제안 방법

- SLICEGPT는 이러한 문제를 해결하는 pruning 방법이다. 하지만 저자들은 일반적인 pruning 방법은 가중치를 줄이기 위해서 0으로 thershhold 하는 방식은 sparsity를 증가시켜 성능저하가 심하다. 저자들은 Q 가중치를 만들어 선형대수학의 성질을 이용하여 계산상 Q가 pruning 되지 않은 결과가 나오는 방법을 제안했다.

![Screenshot 2024-07-14 at 13.40.49.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-14_at_13.40.49.png)

![Screenshot 2024-07-14 at 13.41.06.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-14_at_13.41.06.png)

![Screenshot 2024-07-03 at 14.23.38.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-03_at_14.23.38.png)

---

### 아키텍쳐

![Screenshot 2024-07-03 at 15.14.44.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-03_at_15.14.44.png)

![Screenshot 2024-07-03 at 15.15.14.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-03_at_15.15.14.png)

기존 transformer 아키텍쳐에 SLICEGPT를 적용시키기 위해서 어느정도의 변화가 많이 필요하다.

- LayerNorm 변화:
    
    LayerNorm은 Q를 적용할때 계산식에 방해가된다. 그렇기에 M과 diag(epsilon)을 주위 Layer로 넘겨야한다. M은 activation 이후 가중치로 diag(epsilon)은 activition 이전의 가중치로 옮긴다. M은 평균을 뺴주는 역할을 하며 epsilon은 scaling을 위해서 존재하는 학습형 파라미터다
    
    ![Screenshot 2024-07-14 at 13.48.22.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-14_at_13.48.22.png)
    
     
    
- Q 가중치 생성:
    
    해당 모델에 맞는 Q 가중치를 생성해야한다. Q를 계산하기 위해서 PCA를 이용해야한다. 학습 데이터 셋에서 특정 부분을 남기고 모델을 지나게 한다. Q는 C행렬의 고유값의 내림차 순이다.
    
    ![Screenshot 2024-07-14 at 13.51.54.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-14_at_13.51.54.png)
    
- QTQ를 생성:
    
    구한 Q들을 기준으로 모델에 적용시켜야한다. 하지만 residual을 위해서 이전 값을 다음 layer의 출력에 더해야하는데 생으로 더할수 없기에 QTQ롤 곱해서 더해준다. ex) XQ * QTQ = XQ
    

---

### 실험결과

![Screenshot 2024-07-03 at 16.45.56.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/bf8c7ab1-55a1-4194-8593-107b463194dd.png)

![Screenshot 2024-07-03 at 16.46.58.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-03_at_16.46.58.png)

![Screenshot 2024-07-14 at 13.59.38.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-14_at_13.59.38.png)

### **실제 활용:**

python run_slicegpt.py --model facebook/opt-125m --save-dir dir/to/save/sliced_model/in --sparsity 0.25 --device cuda:0 --eval-baseline --no-wandb

![Screenshot 2024-07-14 at 19.31.55.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-14_at_19.31.55.png)

![Screenshot 2024-07-14 at 19.50.24.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-14_at_19.50.24.png)

![Screenshot 2024-07-14 at 19.50.58.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-07-14_at_19.50.58.png)

---

![Screenshot 2024-08-15 at 09.03.54.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-08-15_at_09.03.54.png)

![Screenshot 2024-08-15 at 09.01.39.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-08-15_at_09.01.39.png)

![Screenshot 2024-08-15 at 08.22.16.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-08-15_at_08.22.16.png)

![Screenshot 2024-08-15 at 07.31.27.png](SLICEGPT%20COMPRESS%20LARGE%20LANGUAGE%20MODELS%20BY%20DELETIN%20be0966ca8d20406d9604411066a645a8/Screenshot_2024-08-15_at_07.31.27.png)