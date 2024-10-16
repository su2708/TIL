#TIL #스파르타코딩클럽 [[TIL]]

## 조건부 필터링
### 1. 조건부 필터링
```python
import pandas as pd

data = {
	'Name': ['A', 'B', 'C', 'D'],
	'Age': [14, 26, 22, 57],
	'Job': ['student', 'tutor', 'tutor', 'pilot']
}
df = pd.DataFrame(data)
```
- 기본 조건부 필터링
	- <, >, \==, <=, >= 등의 조건 사용 가능
	- `df[df['Job'] == 'tutor']  # 직업이 tutor 인 행만 선택`
- 여러 조건을 사용한 필터링
	- AND(&), OR( | ) 연산자 사용
	- `df[(df['Job'] == 'tutor') & (df['Age'] <= 25)]  # 직업이 tutor & 나이가 25세 이하인 행만 선택`
- `isin()`을 사용한 필터링
	- 특정 값들이 포함된 행을 필터링할 때 유용
	- `df[df['Job'].isin('student', 'pilot')]  # 직업이 학생 또는 파일럿인 행 선택`
- `~` 연산자를 사용한 반대 조건 필터링
	- `df[~(df['Age'] >= 50)]`



## 데이터 정렬과 병합
### 1. 데이터 정렬
1) `sort_values()`
	- 특정 열의 값을 기준으로 데이터를 오름차순 또는 내림차순으로 정렬
```python
import pandas as pd

data = {
	'Name': ['A', 'B', 'C', 'D'],
	'Age': [14, 26, 22, 57],
	'Job': ['student', 'tutor', 'tutor', 'pilot']
}
df = pd.DataFrame(data)

sorted_df = df.sort_values(by='Age', ascending=True (default))  # 내림차순은 False
print(sorted_df)
------------------------------------
   Name  Age   Job
0    A   14  student
2    C   22   tutor
1    B   26   tutor
3    D   57   pilot 
```
2) `sort_index()`
	- 인덱스를 기준으로 데이터를 정렬
```python
sorted_df = df.sort_index()
print(sorted_df)
---------------------------
   Name  Age   Job
0    A   14  student
1    B   26   tutor
2    C   22   tutor
3    D   57   pilot 
```


### 2. 데이터 병합
- `merge()`
	- SQL의 join과 같음
	- how='inner' (default) 외에도 outer, left, right 으로 다양하게 병합 가능
	- `pd.merge(df1, df2, on='column1', how='inner')`
- `concat()`
	- 행 또는 열 단위로 데이터 프레임을 연결 가능
	- `pd.concat([df1, df2], axis=0)  # 열 단위 연결은 axis=1`
- `join()`
	- 인덱스를 기준으로 데이터프레임을 병합할 때 사용


