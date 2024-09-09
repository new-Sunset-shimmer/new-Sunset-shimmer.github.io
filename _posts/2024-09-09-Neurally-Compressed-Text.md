# Neurally Compressed Text

[context compressing][[2404.03626.pdf (arxiv.org)](https://arxiv.org/pdf/2404.03626.pdf)]

![Screenshot 2024-04-08 at 15.34.49.png](Neurally%20Compressed%20Text%2050202ca3d5584beb8700dcc1f7f72729/Screenshot_2024-04-08_at_15.34.49.png)

### **abstract** :

llm의 학습 속도 및 계산량을 줄이기 위해서 long context를 bitstream으로 압축, 해당 bitstream을 기반으로 학습. 

기존 문제점: LLM은 여러 문제점으로 인해서 학습 속도 및 계산량이 비싸다. 그 문제점중 하나는 self-attention이라고 생각한다. 현재 여러 LLM은 긴 context를 받고 self-attention을 계산한다. 이 때 복잡도는

$$
O(n^2d)
$$

가된다.[n = 단어 길이, d 모델 차원]. 여기서 n을 compress 하는 것으로 속도와 계산량을 줄일수있다. compress 된 context을 n만큼의 길이로 늘려서 학습하여 좀더 빠른 학습과 수렴을 기대 가능하다

### Equal-Info Window:

long-context를 한번에 압축하면 비효율적이고 성능에 제한이 많다. 현재 많이 쓰이는 고정된 길이로 텍스트를 segment화 혹은 unigram모델을 사용했을 경우 compression algorithm이 context의 초기 몇 token만 학습하는 문제를 발견. 해당 문제를 해결하기 위해 동적으로 동시에 고정적인 segmentize이 필요하다. 해당 논문에서는 Equal-Info라는 방법을 제시했다. segment의 주체를 word보다는 bit에 초첨을 맞춰 context를 ßbit로 segmenting한다.[ß - hyperparameter]. 

ex: Neural c (8bit token)=01001110 01100101 01110101 01110010 01100001 01101100 00100000 01100011(32bit window) 예시를 이렇게 들었지만 문장의 길이, 혹은 batch등에 인해서 여러 패턴이 관측될수있음

![Screenshot 2024-04-08 at 15.34.49.png](Neurally%20Compressed%20Text%2050202ca3d5584beb8700dcc1f7f72729/d66c7666-7d1a-4e12-b347-7259755f1b57.png)

## **M1 모델:**

M1 모델은 입력 context로 최소한의 길이를 가진 bitstream을 만들기 위해서 학습한다. 그렇기 위해서 M1모델은 context에 대한 손실없는 압축이 필요로 해지며 “modeling”과 “coding” 두가지를 만족 시켜야한다.

### modeling: LM로서의 기능

autogressive 모델의 분포 p(X_0:N) = p(x_i | x_0 … x_N)가 있으면 m1은 p(x_0:N)의 확률분포를 근사 가능해야한다. M1은 모델은 modeling을 위해서 들어온 segment의 다음 byte를 예측하며 학습한다.

### coding: compressor로서의 기능

comressorion algorithm에서 나온 bitstream의 길이 l(X_0:N)는 최소한으로 잛아야한다. 만약 l(X_0:N) 가우시안분포를 따를 경우 최대길이 L은 l(X_0:N)의 기대값이 된다. 고로 p의 가설들 H(p)는 L로 upperbound된다 L ≥ H(P)

## Compression Algorithm(Arithmetic Coding):

![Screenshot 2024-04-08 at 15.34.49.png](Neurally%20Compressed%20Text%2050202ca3d5584beb8700dcc1f7f72729/17d09dd5-06e1-459d-94bb-d61e8eab0421.png)

모델 M1에서 나온 tensor를 bitstream으로 변화시키는 모델이다(학습은 안함), 모델 1은 해당 bitstream의 길이를 짧게 만든다. 논문의 예시를 보면 모든 segment를 concat하고 m2모델로 입력하는 듯하다.

## M2 모델:

 

![Screenshot 2024-04-08 at 15.34.49.png](Neurally%20Compressed%20Text%2050202ca3d5584beb8700dcc1f7f72729/b51d3254-373b-4fa5-9be9-7dba841e89a6.png)

M2는 들어온 token ID 를 기반으로 다음 token을 예측하는 모델이다. 이 때 m2모델은 m1모델보다 커야한다. 논문 실험 결과 m 1모델이 클경우 bitstream이 너무 압축이 너무 잘되어서 예측 및 학습이 안되는 문제가 발견됐다. 결국 m 1모델은 low-level structure[patterns of spelling, word frequency, and basic grammar etc]에 대해서만 학습  m 2는 큰 모델을 사용하여 high-level structure을 학습한다. 주된 예측과 task는 lm2에서 하며, M2 comress된  text가 의미와 내포하는 확률을 학습한다.

논문의 아키텍쳐는 LM모델이 M2까지 있지만, 저자들은 이론적으로 해당 아키텍쳐를 확장하여 M3, M4등 보다 높은 단계의 structure, pattern을 학습 가능하다고 소개되어있다.

# 결론:

compress을 위한 벡터를 학습, 특정 bitstream으로 compress하고 해당 text을 prompt로서 쓴다.
