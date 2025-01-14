#TIL #스파르타코딩클럽 [[2501]]

## 1. Django에서 발생한 주요 문제와 해결 방법
### `no such table: chatrooms_chatroom` 에러
- **원인**:
	- `makemigrations`와 `migrate`를 제대로 수행하지 않아 테이블이 생성되지 않음.
	- 데이터베이스와 모델 정의가 일치하지 않음.
	- `INSTALLED_APPS`에 앱이 등록되지 않음.
- **해결**:
	1. `makemigrations`와 `migrate` 명령을 재실행하여 테이블 생성.
	2. 데이터베이스 초기화(`db.sqlite3` 삭제) 후 마이그레이션 재적용.
	3. `INSTALLED_APPS`에서 앱 등록 여부 확인.

### `serializer.is_valid()`가 False를 반환    
- **원인**:
	- 요청 데이터에 필요한 필드가 누락됨.
	- `plan_id`에 해당하는 `TestPlan` 레코드가 존재하지 않음.
- **해결**:
	1. `serializer.errors`를 출력하여 어떤 필드가 유효성 검사를 통과하지 못했는지 확인.
	2. Django Shell에서 `TestPlan` 레코드 생성: `TestPlan.objects.create(name="Plan")`	
	3. 요청 데이터가 올바르게 전달되었는지 `request.data` 확인.

### `ForeignKey` 필드 관련 문제
- `plan_id`가 필수 입력 항목으로 설정되어, `null=True, blank=True`를 추가하여 선택적 입력으로 변경 가능.
- 관련 객체가 삭제되었을 때의 동작(`on_delete`)을 주의하여 설정 (`CASCADE`, `SET_NULL` 등).

### POST 요청 디버깅
- `request.data`를 출력하여 요청 데이터 확인.
- `serializer.save(user_id=request.user)` 호출로 `user_id`를 추가로 저장.


---
## 2. Django 프로젝트를 디버깅하는 과정에서 배운 점

- `serializer.errors`를 활용하여 유효성 검사 실패 원인을 파악하는 것이 중요하다.
- `makemigrations`와 `migrate`가 제대로 실행되지 않으면 Django가 테이블을 생성하지 않으므로, 반드시 명령 실행 후 로그를 확인해야 한다.
- 테이블이 존재하지 않는 에러가 발생하면, 데이터베이스와 모델 정의 상태를 점검하고 초기화 작업을 수행하는 것이 효과적이다.
- Django REST Framework에서 요청 데이터의 필수 항목을 명확히 정의하고, 요청 데이터가 정확히 전달되었는지 확인해야 한다.


### 앞으로 적용할 개선점
1. **마이그레이션 프로세스 점검**:
    - 모델 수정 후 항상 `makemigrations`와 `migrate`를 실행하고 상태를 확인.
    - `showmigrations`로 마이그레이션 상태를 정기적으로 점검.
2. **디버깅 효율성 향상**:
    - `serializer.errors`와 `request.data`를 적극 활용하여 문제 원인 파악.
    - SQLite 셸이나 Django Admin에서 데이터 상태 확인 습관화.
3. **TDD(Test-Driven Development) 적용**:
    - 모델과 API의 유효성 검사를 자동화하기 위해 단위 테스트 작성.
    - 요청과 응답에 대한 테스트 케이스 작성으로 API 안정성 확보.