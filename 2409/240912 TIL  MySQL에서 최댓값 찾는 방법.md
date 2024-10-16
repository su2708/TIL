#TIL #스파르타코딩클럽 [[TIL]]

## MySQL에서 최대값을 찾는 다양한 방법

### 1. `Max()` 함수 사용
- `Max()` 함수는 컬럼에서 가장 큰 값을 반환하는 집계 함수
- 그룹 내에서 최대값을 찾거나 테이블 전체에서 최대값을 찾을 때 사용
- 각 그룹에서 최대값을 찾을 때는 `Group by`와 함께 `Max()`를 사용

### 2. `Order by` 와  `LIMIT` 사용
- 최대값을 가진 행 전체를 반환할 때 사용
- 데이터를 정렬한 후 `LIMIT`만큼의 행을 선택한다.
```sql
-- 가장 높은 급여를 가진 직원의 이름과 급여를 반환
select Name, Salary
from Employees
order by Salary desc
limit 1;
```

### 3. `Having`을 사용하여 최대값 필터링
-  `Having`은 그룹화된 데이터에서 조건에 맞는 최대값을 필터링할 때 유용