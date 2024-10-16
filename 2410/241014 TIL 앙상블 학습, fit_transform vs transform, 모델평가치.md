#TIL #스파르타코딩클럽 [[TIL]]

## 앙상블 학습
### 1. 앙상블 학습
- 여러 개의 머신러닝 모델을 결합하여 예측 성능을 향상시키는 기법
- 하나의 모델이 예측할 때 발생할 수 있는 오차를 줄이고 더 안정적이고 정확한 결과를 얻을 수 있음


### 2. 앙상블 학습방법의 종류
1) 배깅(Bagging)
	- 여러 개의 모델을 병렬적으로 학습하고, 각 모델의 결과를 평균 내거나 다수결로 결정
	- 모델의 분산을 줄여주기 때문에, 단일 모델보다 더 나은 성능을 보일 수 있음
2) 부스팅(Boosting)
	- 모델을 순차적으로 학습하면서, 이전 모델이 틀린 부분에 더 가중치를 두어 학습하는 방법
3) 스태킹(Stacking)
	- 개별 모델들이 예측한 결과를 입력으로 하는 또 다른 모델을 만들어 최종 예측을 수행


### 3. 앙상블 학습의 장점
- 다양한 모델의 예측을 결합함으로써 하나의 모델보다 더 강력하고 안정적인 예측 성능을 얻을 수 있음
- 데이터에 노이즈가 있거나 모델이 오버피팅될 가능성이 있는 경우에 유용



## fit_transform vs transform
### 1. fit_transform
- 역할: 주어진 데이터를 학습하고, 학습한 내용을 바탕으로 데이터를 변환
- 작동 방식: 먼저 데이터를 사용하여 내부적으로 필요한 계산을 수행하여 학습한 뒤, 그 데이터를 변환
- 사용 시기: 학습 데이터를 처음으로 전처리할 때 사용


### 2. transform
- 역할: 이미 학습한 정보를 바탕으로 새로운 데이터를 변환
- 작동 방식: 이전에 `fit()`메서드나 `fit_transform()`메서드를 통해 학습한 정보를 사용하여 입력된 데이터를 변환만 수행
- 사용 시기: 학습된 모델을 바탕으로 데스트 데이터나 새로운 데이터를 변환할 때 사용

```python
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)  # X_train 데이터로 학습하고 변환
X_test_scaled = scaler.transform(X_test)  # 학습된 scaler로 X_test 데이터만 변환
```



## 모델 평가치
### 1. Accuracy

- 정의: 모델이 올바르게 예측한 비율. 즉, 전체 샘플 중에서 맞게 예측된 샘플의 비율
- 계산: $$Accuracy=\frac{TP+TN}{TP+TN+FP+FN}$$ **TP(True Positive), TN(True Negative), FP(False Positive), FN(False Negative)**

### 2. Precision (정밀도)
- 정의: 모델이 양성이라고 예측한 것 중 실제로 양성인 데이터의 비율. 즉, '1' 클래스라고 예측한 것들 중에서 실제로 '1'인 경우의 비율
- 계산: $$Precision=\frac{TP}{TP + FP}​$$
- 해석: Precision은 잘못된 양성 예측(FP)을 줄이는 데 중점을 둔 지표

### 3. Recall (재현율)
- 정의: 실제 양성(Positive)인 데이터 중에서 모델이 올바르게 양성으로 예측한 비율. 즉, 실제로 '1'인 경우를 모델이 얼마나 잘 예측했는지 나타냄.
- 계산: $$Recall=\frac{TP}{TP + FN}$$
- 해석: Recall은 잘못된 음성 예측(FN)을 줄이는 데 중점을 둔 지표

### 4. F1-score
- 정의: 정밀도와 재현율 사이의 균형을 나타내는 조화 평균. F1-score는 둘 중 하나라도 낮으면 낮아지기 때문에, 정밀도와 재현율 사이의 균형이 중요한 문제에서 유용
- 계산: $$F1=2 \times \frac{Precision \times Recall}{Precision + Recall}$$
- 해석: F1-score는 Precision과 Recall의 균형을 나타냄

### 5. Support
- 정의: 각 클래스에 속한 실제 샘플의 수를 나타냄

### 6. Macro Avg (평균)

- 정의: 각 클래스의 성능 지표를 단순히 평균 낸 값. 각 클래스의 중요도를 동일하게 반영

### 7. Weighted Avg (가중 평균)
- 정의: 각 클래스의 support에 비례하여 성능 지표를 가중 평균낸 값. 즉, 데이터셋에서 많이 등장하는 클래스에 더 큰 가중치를 부여

### 8. Confusion Matrix (혼동행렬)
```
[[40  3]
 [ 3 68]]
```

|            | Predicted No | Predicted Yes |
| :--------: | :----------: | :-----------: |
| Actual No  |   TN = 40    |    FP = 3     |
| Actual Yes |    FN = 3    |    TP = 68    |

- 정의: 모델의 예측과 실제 값 간의 관계를 행렬 형태로 나타낸 것
- 해석:
    - 40: 실제 '0'이고 '0'으로 예측한 경우(True Negative).
    - 3: 실제 '0'인데 '1'로 잘못 예측한 경우(False Positive).
    - 3: 실제 '1'인데 '0'으로 잘못 예측한 경우(False Negative).
    - 68: 실제 '1'이고 '1'으로 예측한 경우(True Positive).

