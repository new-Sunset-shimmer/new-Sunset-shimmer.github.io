# A survey on model compression for Large Language models

[A Survey on Model Compression for Large Language Models](https://arxiv.org/abs/2308.07633)

![Screenshot 2024-05-23 at 01.53.27.png](A%20survey%20on%20model%20compression%20for%20Large%20Language%20m%2058a272d14de646fbb75eda873a789cb2/Screenshot_2024-05-23_at_01.53.27.png)

## Methods:

1. Pruning
    
    Pruning 필요없는  파라미터를 삭제 함으로서 모델의 사이즈와 복잡도를 줄이는 방법. 필요없는 파라미터가 삭제되기에 최고의 경우 성능에 영향을 안끼치면서 사이즈를 줄이는게 가능하다. Pruning은 Unstructed Pruning과 Sturcted Pruning으로 나뉜다.
    
    1. Unstructed Pruning: 특정 파라미터들을 구조와 상관없이 없애는 방법.  대부분은 특정 뉴런 혹은 가중치를 0으로 thresh hold[0이하 값을]합니다. 이러한 방법은 불규칙적인 sparse 모델을 생성합니다. 그렇기에 효과적인 모델 사이즈 앞축과 성능을 위해서 여러 방법들이 제안됐습니다.
        1. [SparseGPT: Massive Language Models Can be Accurately Pruned in One-Shot (arxiv.org)](https://arxiv.org/pdf/2301.00774): 
    2. Sturcted Pruning: 모델의 구조 자체를 그대로 없앱니다. 튜런, 채널 레이어들을 한번에 날립니다.
2. Knowledge Distillation
    
    Knowledge Distillation(KD)은 티쳐 모델에서 정보를 가져 오며 학습함으로서 학생 모델의 성능을 올리는 방식입니다. 좀더 자세하게 설명하면 티쳐모델의 지식을 학습하는 방법입니다. KD는 Black-Box KD와 White-box KD로 나뉩니다.
    
    1. White-box KD : 모델의 내부 구조, 파라미터까지 확인 가능한 경우에 채용됩니다. 학생 모델이 티쳐모델의 내부지식을 깊게 이해할수있습니다. 대부분은 open source 모델들이 티쳐모델로 채용됩니다.
    2. Black-Box KD :  티쳐 모델의 output만 확인 가능한 상황일때 많이 쓰입니다. output을 뽑을 때 in context learning 혹은 chain of thought, instruction following등 output 뽑는 방식을 사전에 정의 합니다.  
    
    ![Screenshot 2024-05-23 at 20.41.53.png](A%20survey%20on%20model%20compression%20for%20Large%20Language%20m%2058a272d14de646fbb75eda873a789cb2/Screenshot_2024-05-23_at_20.41.53.png)
    
    ![Screenshot 2024-05-23 at 20.49.18.png](A%20survey%20on%20model%20compression%20for%20Large%20Language%20m%2058a272d14de646fbb75eda873a789cb2/Screenshot_2024-05-23_at_20.49.18.png)
    
3. Quantization
    
    Quantization은 모델의 데이터 타입을 바꾸는 방법입니다. 현재 대부분의 모델은  fp16을 주로 씁니다. fp16은 크기에 다른 형식(int, fp8)등으로 변환하여 실제 사이즈를 변화시킵니다. Quantization애는 두가지의 방법이 존재합니다. Quantization aware 방식과 post train quantization이 존재합니다.
    
    1. Quantization aware train : 모델이 학습할때 quantization을 하는 방법입니다.
    2. post train quantization : 모델의 합습이 끝난뒤 파라미터들의 값을 quantization 하는 방식입니다.
4. Low-Rank factorization
    
    Low-Rank factorization(LoRA)는 Low Rank 차원들을 생성하여 서로 곱하여 원본 매트릭스를 만드는 방식입니다.
    

### Metrics and Benchmarks:

1. Number of Parameters
2. Model Size
3. Compression Ratio
4. Inference time
5. Floating point operations (FLOPs)

### Benchmarks and Datasets:

1. Common Benchmarks and Datasets
2. BIG-Bench
3. Unseen Instructions Datasets
4. OpenBookQA Dataset

### Challenges and Future Directions:

1. More advanced Methods
2. Performance-Size Trade-offs
3. Dynamic LLM Compression
4. Explainability