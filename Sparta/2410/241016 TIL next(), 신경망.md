#TIL #스파르타코딩클럽 [[2410]]

## next()
### 1. next
- iterator로부터 다음 항목을 반환하는 함수
- iterable한 객체는 `next()`와 직접 사용될 수 없기 때문에 iterator로 변환해야 함
- 반복 가능한 객체 -> `iter()` -> `next()`
```python
# 반복 가능한 객체
numbers = [1, 2, 3]

# 이터레이터로 변환
iterator = iter(numbers)

# next()로 값 하나씩 가져오기
print(next(iterator))  # 1 출력
print(next(iterator))  # 2 출력
print(next(iterator))  # 3 출력
```



## 신경망 (Neural Network)
### 1. 인공 신경망 (ANN)
- 인간의 뇌 구조에서 영감을 받아 개발된 기본적인 신경망
- **층(layer)** 으로 구성되어 있음. 입력층, 은닉층, 출력층을 가짐
- 고정된 크기의 입력과 출력인 경우에 적합


### 2. 합성곱 신경망 (CNN)
- 이미지와 같은 2차원 데이터의 특징을 효과적으로 추출하기 위해 설계된 신경망
- 합성곱 층, 풀링 층, 완전 연결 층으로 구성
	- 합성곱 층: 입력 이미지에 필터를 적용하여 feature map 생성
	- 풀링 층: feature map의 크기를 줄이고, 중요한 특징 추출
	- 완전 연결 층: 추출된 특징을 바탕으로 최종 예측 수행


### 3. 순환 신경망 (RNN)
- 시계열 데이터나 연속된 데이터를 처리하기 위해 설계된 신경망
- 입력 데이터와 이전 시간 단계의 은닉 상태를 입력으로 받아, 현재 시간 단계의 은닉 상태를 출력
- 각 시간 단계에서 동일한 가중치를 공유하여, 시퀀스의 패턴을 학습


### 4. LSTM (Long Short-Term Memory)
- RNN의 장기 의존성 문제를 해결하기 위해 개발된 신경망
- cell state와 gate 구조를 도입, 장의 의존성을 효과적으로 학습 가능


### 5. GRU (Gated Recurrent Unit)
- LSTM의 변형으로, cell state 대신 hidden state 만을 사용하여 구조를 단순화
