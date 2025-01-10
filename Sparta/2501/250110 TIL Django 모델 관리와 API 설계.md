#TIL #스파르타코딩클럽 [[Sparta Coding Club]]

## 1. 한 일

### Django 모델 간 참조 관리
- `OneToOneField`를 활용하여 `ChatRoom`과 `TestPlan` 간 1대1 관계를 설계.
- 모델 간 **순환 참조 문제**를 방지하기 위해 문자열(`"app_name.ModelName"`)로 모델 참조를 변경.
- 데이터 무결성을 보장하기 위해 `unique_together`를 설정, 각 `chat_id`에 대해 고유한 `plan_id`를 유지하도록 설계.

### TestPlan 생성 로직 구현
- 사용자 요청(예: `"이제 계획을 세워줘"`)에 따라 `TestPlan`을 생성하고 `ChatRoom`과 연결하는 로직을 작성.
- `TestPlan`의 `plan_id`는 **`pre_save` 신호**를 통해 자동 증가하도록 설정.
- **`transaction.atomic()`**과 **`select_for_update()`**를 사용해 동시성 문제를 방지하며 데이터베이스의 일관성을 유지.


---

## 2. TroubleShooting

### **순환 참조 문제**
- **오류**: `ImportError: cannot import name 'TestPlan' from partially initialized module...`
- **원인**: 모델 간 순환 참조로 인해 초기화 순서가 꼬임.
- **해결**:
    - 모델 참조를 문자열(`"app_name.ModelName"`)로 변경하여 초기화 순서를 유연하게 관리.
    - `TestPlan`과 `ChatRoom` 간 참조를 명확히 하고, 관련 필드를 수정.

### **plan_id 초기화 문제**
- **문제**: 새로운 `chat_id`에 대해 첫 번째 `TestPlan` 생성 시, `select_for_update()`로 잠금을 설정할 데이터가 없음.
- **해결**:
    - `pre_save` 신호에서 `TestPlan.objects.filter(chat_id=...)`로 기존 데이터 유무를 확인.
    - 데이터가 없을 경우, `plan_id`를 1로 초기화.
    - 트랜잭션(`transaction.atomic`)을 통해 동시성을 관리하고 무결성을 유지.

### **JSON 직렬화 오류**
- **오류**: `TypeError: Object of type ChatRoom is not JSON serializable`.
- **원인**: `ChatRoom` 객체를 JSON으로 반환하려 했기 때문.
- **해결**:
    - `ChatRoomSerializer`를 사용하여 데이터를 JSON으로 변환한 후 반환.

---

## 3. 느낀 점

### Django 모델 참조 설계
- **문자열로 모델 참조**: 모델 간 순환 참조 문제를 방지할 수 있는 간단하고 효과적인 방법.
- **`OneToOneField`의 장점**: 1대1 관계를 명확히 정의함으로써 데이터 모델을 간결하고 직관적으로 관리 가능.

### 트랜잭션 관리의 중요성
- `transaction.atomic()`과 `select_for_update()`는 동시성 문제를 해결하는 강력한 도구이며, 특히 여러 사용자가 동시에 데이터에 접근할 때 유용하다는 점을 경험.
- `unique_together`로 데이터 무결성을 쉽게 보장할 수 있어 실수 방지에 효과적.

### API 설계와 예외 처리
- RESTful 설계는 유지보수가 쉽고 직관적이며, 명확한 응답 코드를 통해 사용자 경험을 개선할 수 있음을 배움.
- 예외 처리가 API 안정성과 개발자 경험 모두에 중요한 역할을 한다는 점을 다시 한 번 실감.