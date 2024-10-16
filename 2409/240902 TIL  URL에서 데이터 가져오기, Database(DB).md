#TIL #알고리즘 #스파르타코딩클럽 [[TIL]]

## 1. URL 주소에서 데이터 가져오기

### 1) 주소에 따라 메인화면 인사말 바꾸기
```python
@app.route("/iloveyou/<name>/")
def iloveyou(name):

    motto = f"{name}야 난 너뿐이야..."
    context = {
        "name": name,
        "motto": motto
    }

    return render_template("motto.html", data=context)
```

- `route()` 부분의 `<name>` 와 같이 `<>` 를 사용하면, URL 주소에 따라서 name을 변수처럼 사용 가능

## 2. Database(DB)

### 1) DB의 두 가지 종류
- RDBMS(SQL)
	- 엑셀에 데이터를 저장하는 것과 유사. 행/열의 생김새에 따라 정형화된 데이터를 가짐
	- 장점: 데이터의 일관성, 용이한 분석
	- 단점: 자유로운 형태의 데이터 적재 불가
	- ex) SQLite, MS-SQL, My-SQL 등
- No-SQL
	- Dictionary 형태로 데이터를 저장. 모든 데이터가 같은 값을 가질 필요가 없음
	- 장점: 자유로운 형태의 데이터
	- 단점: 데이터 간의 일관성이 부족
	- ex) MongoDB


