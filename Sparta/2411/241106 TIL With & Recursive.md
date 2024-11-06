#TIL #스파르타코딩클럽 [[2411]]

## 1. With & Recursive
### With 구문: Common Table Expressions (CTEs)
- CTE
	- 복잡한 쿼리에서 반복적으로 사용되는 서브쿼리 부분을 **별도의 임시 테이블처럼 만들어 재사용**할 수 있게 해주는 기능
	- 코드의 가독성, 재사용성, 유지 보수성을 높이기 위해 사용
```sql
WITH cte_name AS (
    SELECT column1, column2
    FROM some_table
    WHERE condition
)
SELECT column1, column2
FROM cte_name
```


### Recursive CTE: 재귀적 공통 테이블 표현식
- Recursive CTE
	- MySQL 8.0 이상에서 사용 가능
	- 계층 구조 데이터를 탐색할 때 매우 유용
	- 종료 조건에 맞춰 데이터를 누적하거나 반복 처리 가능
```sql
WITH RECURSIVE cte_name AS (
    -- Anchor Member: 초기 값 정의
    SELECT column1, column2
    FROM table
    WHERE base_condition

    UNION ALL

    -- Recursive Member: 재귀 호출
    SELECT column1, column2
    FROM table
    JOIN cte_name ON table.parent_id = cte_name.id
    WHERE recursive_condition
)
SELECT * FROM cte_name;

```


### 11.06 SQL CODEKATA
##### 문제
`ANIMAL_OUTS` 테이블은 동물 보호소에서 입양 보낸 동물의 정보를 담은 테이블입니다. `ANIMAL_OUTS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `NAME`, `SEX_UPON_OUTCOME`는 각각 동물의 아이디, 생물 종, 입양일, 이름, 성별 및 중성화 여부를 나타냅니다.

|NAME|TYPE|NULLABLE|
|---|---|---|
|ANIMAL_ID|VARCHAR(N)|FALSE|
|ANIMAL_TYPE|VARCHAR(N)|FALSE|
|DATETIME|DATETIME|FALSE|
|NAME|VARCHAR(N)|TRUE|
|SEX_UPON_OUTCOME|VARCHAR(N)|FALSE|

보호소에서는 몇 시에 입양이 가장 활발하게 일어나는지 알아보려 합니다. 0시부터 23시까지, 각 시간대별로 입양이 몇 건이나 발생했는지 조회하는 SQL문을 작성해주세요. 이때 결과는 시간대 순으로 정렬해야 합니다.

##### 풀이
1.  0시 ~ 23시까지 우선 만들어야 하기 때문에 with recursive 구문을 활용해 매 시간 설정
```sql
with recursive time as (
    select 0 as hour
    union all
    
    select hour + 1
    from time
    where hour < 23
)
```

- 결과

| HOUR |  0  |  1  |  2  |  3  |  ⋯  | 23  |
| ---- | :-: | :-: | :-: | :-: | :-: | :-: |

2. 위에서 만든 시간표에 맞게 ANIMAL_OUTS의 시간대 별로 입양 건 수 조회
```sql
select t.hour, count(o.animal_id) as count
from time t left join (
    select *, hour(datetime) as hour
    from animal_outs
) o on t.hour = o.hour
group by 1
order by 1
```

- 결과

| HOUR  |  0  |  ⋯  |  7  |  8  |  9  | 10  | 11  | 12  | 13  | 14  | 15  | 16  | 17  | 18  | 19  |  ⋯  | 23  |
| ----- | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| COUNT |  0  |  0  |  3  |  1  |  1  |  2  | 13  | 10  | 14  |  9  |  7  | 10  | 12  | 16  |  2  |  0  |  0  |


