#TIL #스파르타코딩클럽 [[2501]]

## 1. 한 일
### Django 모델 간 순환 참조 문제 해결
- Django의 `OneToOneField`를 사용하여 `ChatRoom`과 `TestPlan` 간 1대1 관계를 설정했다.
- 순환 참조 문제를 방지하기 위해 모델 참조를 문자열(`"app_name.ModelName"`)로 변경하여 초기화 순서를 유연하게 조정했다.
- `unique_together`를 사용하여 각 `chat_id`별로 `plan_id`가 고유하도록 데이터 무결성을 설정했다.

### TestPlan 생성 로직 구현
- 사용자의 메시지(예: `"이제 계획을 세워줘"`)를 기반으로 자동으로 `TestPlan`을 생성하고 `ChatRoom`과 연결하는 로직을 구현했다.
- `TestPlan` 생성 시, `plan_id`를 `pre_save` 신호를 통해 자동 증가하도록 설정했다.
- `transaction.atomic()`과 `select_for_update()`를 사용하여 동시성 문제를 방지하고, 데이터의 무결성을 유지했다.

### API 설계 및 예외 처리
- `TestPlanCreateView`를 작성하여 `POST /chatroom/<chat_id>/testplan/` 요청으로 `TestPlan`을 생성하고 `ChatRoom`과 연결하는 기능을 구현했다.
- 예외 처리:
    - `ChatRoom.DoesNotExist` 예외 발생 시 404 응답 반환.
    - 이미 `TestPlan`이 존재할 경우 400 응답으로 처리.



---
## 2. TroubleShooting

### **순환 참조 문제**
- **오류 메시지**: `ImportError: cannot import name 'TestPlan' from partially initialized module...`
    - 원인: `ChatRoom`과 `TestPlan` 모델이 서로를 참조하면서 초기화 순서 문제가 발생.
    - 해결:
        - 모델 참조를 문자열로 변경하여 순환 참조 문제를 해결.
        - `TestPlan`의 `chat_id`와 `ChatRoom`의 `testplan` 필드 모두 `"app_name.ModelName"` 형식으로 참조하도록 수정.

### **plan_id 설정 문제**
- **문제**: 새로운 `chat_id`에 대한 첫 번째 `TestPlan` 생성 시, `select_for_update()`가 작동하지 않음.
    - 원인: 데이터베이스에 기존 데이터가 없으면 잠금을 설정할 대상이 없음.
    - 해결:
        - `pre_save` 신호에서 `TestPlan.objects.filter(chat_id=...)`를 사용해 데이터가 없을 경우 `plan_id=1`로 설정.
        - `transaction.atomic()`으로 동시성 문제를 방지.

### 테스트 데이터 저장
- 오류 메시지: `TypeError: Object of type ChatRoom is not JSON serializable`
    - 원인: `ChatRoom` 객체를 JSON 응답으로 직접 반환하려 했기 때문.
    - 해결: `ChatRoomSerializer`를 사용하여 데이터를 JSON으로 변환 후 반환.

---

## 3. 느낀 점

### Django 모델 관리
- 모델 간 참조는 문자열로 작성하면 초기화 순서 문제를 방지할 수 있다는 점을 배웠다.
- `OneToOneField`를 사용해 1대1 관계를 명확히 정의하고, 데이터 무결성을 보장하는 설계가 중요함을 깨달았다.

### 데이터 무결성과 트랜잭션
- `transaction.atomic()`과 `select_for_update()`를 통해 동시성 문제를 방지하고, 데이터의 무결성을 유지하는 방법을 익혔다.
- `unique_together`를 사용하면 간단히 필드 간 고유성을 보장할 수 있다는 점도 유용했다.

### API 설계
- `POST` 요청으로 특정 `ChatRoom`에 연결된 `TestPlan`을 생성하고 관리하는 RESTful 설계의 중요성을 경험했다.
- 적절한 예외 처리가 API의 안정성과 사용자 경험을 크게 향상시킨다는 점을 배웠다.