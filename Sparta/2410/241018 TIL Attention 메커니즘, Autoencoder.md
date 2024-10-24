#TIL #스파르타코딩클럽 [[2410]]

## Attention 메커니즘
### 1. Attention 매커니즘
- 시퀀스 데이터에서 **중요한 부분에 더 많은 가중치를 할당**하여 정보를 효율적으로 처리하는 기법


### 2. Attention이 필요한 이유
- RNN 구조는 LSTM, GRU와 같이 변형을 거쳤음에도 긴 문장을 처리할 때 기억력 한계가 생기고, 문장의 앞뒤 정보가 잘 연결되지 않는 문제가 발생
- 문장 내의 특정 단어에 가중치를 부여하여 더 효율적으로 문맥을 파악


### 3. Attention 매커니즘의 작동 원리
1) **Query, Key, Value**
	- Query: 현재 위치에서 어느 단어가 중요할 지 묻는 역할을 하는 벡터.
	- Key: 문장의 각 단어가 얼마나 중요한 지 나타내는 정보 벡터
	- Value: 문장 내의 단어 자체의 정보가 담긴 벡터

2) Query와 Key의 내적
	- Query와 Key 간의 유사도를 계산

3) Softmax로 가중치 정규화
	- 모든 유사도 값들을 Softmax 함수에 넣어 각 단어의 가중치를 확률처럼 정규화

4) 가중치를 Value에 곱하기
	- 각 단어의 Value 벡터에 해당 가중치를 곱함
	- 중요도가 높은 단어는 더 크게 반영되고, 중요도가 낮은 단어는 덜 반영함

5) 최종 출력 계산
	- 가중치가 곱해진 모든 Value 벡터들을 더해 최종 출력을 만듦
	- 이 결과가 다음 레이어나 모델의 예측에 사용



## Autoencoder
### 1. Autoencoder
- 입력 데이터를 압축하고, 다시 복원하는 데 사용되는 비지도 학습 모델
- 주로 데이터의 잠재 표현(Latent Representation)을 찾는 데 사용


### 2. 구조
- Encoder: 입력 데이터를 압축 -> 잠재 표현
- Decoder: 잠재 표현 -> 원래 입력 형태로 복원
