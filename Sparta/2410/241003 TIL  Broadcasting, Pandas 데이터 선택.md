#TIL #스파르타코딩클럽 [[2410]]

## 브로드캐스팅(broadcasting)
### 1. 브로드캐스팅(broadcasting)
- `NumPy`에서 서로 다른 크기의 배열 간의 연산을 가능하게 하는 기능
- 크기가 다른 배열이라도 특정 규칙을 따를 경우, 크기를 맞추는 것처럼 연산을 수행


### 2. 브로드캐스팅의 기본 원칙
1) 두 배열의 차원 수가 다르면, 더 작은 배열의 앞쪽(왼쪽)에 1을 추가하여 차원 수를 맞춤
```python
import numpy as np

A = np.array([1, 2, 3])
B = 2

# 배열 A에 스칼라 B를 브로드캐스팅하여 덧셈
C = A + B
print(C)  # 출력: [3 4 5]
```

2) 두 배열의 각 차원에서 크기가 다를 때, 어느 한쪽이 1이면 그 배열의 해당 차원은 다른 배열과 맞춰짐
```python
import numpy as np

A = np.array([[1, 2, 3], [4, 5, 6]])  # (2, 3) 배열
B = np.array([10, 20, 30])            # (3,) 배열

# B가 A의 (2, 3) 크기로 브로드캐스팅되어 덧셈
C = A + B
print(C)
# 출력:
# [[11 22 33]
#  [14 25 36]]
```
```python
A = np.array([[1], [2], [3]])  # (3, 1) 배열
B = np.array([10, 20, 30])     # (3,) 배열

# A는 (3, 1), B는 (3,) 크기이므로 B가 (1, 3)으로 변환되어 연산 가능
C = A + B
print(C)
# 출력:
# [[11 21 31]
#  [12 22 32]
#  [13 23 33]]
```
3) 크기가 1이 아닌 차원에서 배열의 크기가 맞지 않으면 오류가 발생


### 브로드캐스팅의 유용성
- 메모리 효율성: 브로드캐스팅은 실제로 배열을 복사하여 크기를 맞추는 것이 아니라, 메모리를 효율적으로 사용하면서 연산을 처리
- 간결성: 코드가 간결해지고, 배열의 크기를 맞추기 위해 별도의 작업이 필요 없음



## 데이터 선택
### 1. 인덱스를 사용한 데이터 선택
1) `set_index()`로 인덱스 설정
	- `inplace`: `set_index()`메서드가 원본 데이터프레임 자체를 수정할 지 말지
	- `drop`: 지정된 열을 인덱스로 설정하면서 기존 열에서는 삭제할 지 말지
```python
import pandas as pd

# 예시 데이터프레임 생성
data = {
	'Name': ['Alice', 'Bob', 'Charlie'],
	'Age': [25, 30, 35],
	'City': ['New York', 'Los Angeles', 'Chicago']
}
df = pd.DataFrame(data)

# inplace=False (기본값): 원본 데이터프레임은 수정되지 않고, 새로운 데이터프레임을 반환
# drop=True (기본값): 지정된 열을 인덱스로 설정하면서 기존 열에서는 삭제
new_df = df.set_index('Name', inplace=False, drop=True)
print(new_df)  # 'Name'이 새로운 인덱스
print(df)  # 원본 데이터프레임은 변하지 않음

# inplace=True: 원본 데이터프레임이 수정됨
df.set_index('Name', inplace=True, drop=True)
print(df)  # 'Name'이 인덱스가 된 원본 데이터프레임

# drop=False: 지정된 열이 인덱스로 설정되지만, 여전히 데이터프레임의 열로 남음
df.set_index('Age', inplace=True, drop=False)
print(df)  # 'Age'가 인덱스가 된 원본 데이터프레임

-------------------------------------------------
# 출력
# new_df (inplace=False로 반환된 새로운 데이터프레임)
        Age         City
Name                      
Alice    25     New York
Bob      30  Los Angeles
Charlie  35      Chicago

# df (원본 데이터프레임은 변화 없음)
       Name  Age         City
0     Alice   25     New York
1       Bob   30  Los Angeles
2   Charlie   35      Chicago

# df (inplace=True 후)
         Age         City
Name                      
Alice     25     New York
Bob       30  Los Angeles
Charlie   35      Chicago

# df (drop=False 후)
    Age City
Age 
25  25 New York
30  30 Los Angeles
35  35 Chicago
```

2) `df.loc[]`를 사용해 인덱스로 데이터 선택
```python
print(new_df.loc['Bob'])
------------------------
# 출력
Age              30
City    Los Angeles
Name: Bob, dtype: object
```


### 2. 열(Column)을 지정하여 데이터 선택
1) 단일 열 선택
```python
import pandas as pd

# 예시 데이터프레임 생성
data = {
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age': [25, 30, 35],
    'City': ['New York', 'Los Angeles', 'Chicago']
}

df = pd.DataFrame(data)

print(df['Age'])  # 단일 열 선택
-----------------------------------------------------
# 출력
0    25
1    30
2    35
Name: Age, dtype: int64
```

2) 여러 열 선택
```python
print(df[['Age', 'City']])
--------------------------
# 출력
   Age         City
0   25     New York
1   30  Los Angeles
2   35      Chicago
```


### 3. 행(Row)을 지정하여 데이터 선택하기
1) `df.loc[]`: **레이블(이름)** 을 사용하여 행 선택
	- 인덱스 라벨로 특정 행 선택
	- 여러 행(열)을 선택할 때는 리스트나 슬라이싱을 통해 선택 (`df.loc[행, 열]` 구조)
```python
data = {
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age': [25, 30, 35],
    'City': ['New York', 'Los Angeles', 'Chicago']
}

df = pd.DataFrame(data)
df.set_index('Name', inplace=True)

print(df.loc['Bob'])  # 단일 행 선택
-----------------------------------------------------
# 출력
Age    30
City   Los Angeles
Name: Bob, dtype: object
```
```python
print(df.loc[0:1, 'Age'])  # df.loc에서 슬라이싱(a:b)은 a부터 b까지
--------------------------
0    25
1    30
2    35
Name: Age, dtype: int64
```

2) `df.iloc[]`: **정수 인덱스(위치)** 를 사용하여 행을 선택
	- 정수 인덱스로 특정 행 선택
	- 여러 행(열)을 선택할 때는 리스트나 슬라이싱을 통해 선택 (`df.iloc[행, 열]` 구조)
```python
data = {
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age': [25, 30, 35],
    'City': ['New York', 'Los Angeles', 'Chicago']
}

df = pd.DataFrame(data)
df.set_index('Name', inplace=True)

print(df.iloc[1])  # 단일 행 선택
-----------------------------------------------------
# 출력
Age    30
City   Los Angeles
Name: Bob, dtype: object
```
```python
print(df.iloc[0:1, [1, 2]])  # df.iloc에서 슬라이싱(a:b)은 a부터 b-1 까지
---------------------------
   Age      City
0   25  New York
```