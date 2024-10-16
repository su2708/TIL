#TIL #스파르타코딩클럽 [[TIL]]

## apply()
### 1. `apply()`의 기본 형식
```python
DataFrame.apply(function, axis=0)
```
- 각 행(row) 또는 열(column)에 적용할 수 있는 함수
- 데이터를 변환하거나 계산하는 데 매우 유용
- `function`: 각 행 또는 열에 적용할 함수
- `axis`: 함수를 적용할 축을 지정
	- `axis=0` (default): 열 단위로 함수를 적용
	- `axis=1`: 행 단위로 함수를 적용


### 2. 내가 겪은 상황
```
5. 각 범죄 별로 검거율을 계산하고, 각 검거율 데이터 column을 DataFrame에 추가해주세요.
- 추가해야할 column: [강간검거율, 강도검거율, 살인검거율, 절도검거율, 폭력검거율, 검거율]
```
```python
extra_columns = ['강간', '강도', '살인', '절도', '폭력', '검거율']

for col in extra_columns:
    if col == '검거율':
        df['검거율'] = df.apply(lambda row: (row['소계(검거)'] / row['소계(발생)']) * 100, axis=1)
    else:
        df[f'{col}검거율'] = df.apply(lambda row: (row[f'{col}(검거)'] / row[f'{col}(발생)']) * 100, axis=1)

df.head(3)
```
1) 추가해야 할 열 생성
	- `extra_columns = ['강간', '강도', '살인', '절도', '폭력', '검거율']`
2) for 문으로 df에 열 추가
	- `lambda row: (row[f'{col}(검거)'] / row[f'{col}(발생)']) * 100`
		- 검거 열의 값과 발생 열의 값의 비율을 각 행마다 계산
	- `axis=1`
		- 계산한 값을 행 마다 적용
	- `df[f'{col}검거율'] = df.apply(function, axis)`
		- 데이터프레임에 검거율 행을 만들고, 함수의 결과 값을 검거율 열의 각 행마다 적용


### 3. `apply()` vs `map()`
-  `apply()`: 데이터프레임의 행 또는 열 단위로 함수를 적용. 여러 열을 동시에 참조할 때 유용
- `map()`: 시리즈에만 적용 가능하며, 각 요소에 함수를 적용. 여러 열을 동시에 참조는 불가