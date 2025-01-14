#TIL #스파르타코딩클럽 [[2501]]

## 1. 한 일
### Django REST Framework에서의 요청 처리
- Django REST Framework의 `APIView`를 사용하여 POST 요청을 처리하는 방법을 학습했다.
- `request.data.get()`으로 클라이언트의 데이터를 안전하게 추출하고, 필수 필드의 유효성을 확인하는 방법을 연습했다.
- 클라이언트가 필요한 데이터를 제공하지 않은 경우 적절한 오류 메시지와 HTTP 상태 코드(`400 Bad Request`)를 반환하는 방식을 구현했다.


### 모델 데이터 저장

- Django 모델의 `objects.create()`를 사용하여 데이터를 저장하는 과정을 익혔다.
- ForeignKey 필드에는 객체 자체(`chat_id=chatroom_instance`)나 기본 키(`chat_id_id=chat_id`)를 할당할 수 있음을 확인했다.
- JSONField에 데이터를 저장할 때는 Python 딕셔너리 형태로 값을 전달해야 한다는 점을 확인했다.


### API 응답 구조

- POST 요청 처리 후, 사용자 메시지와 AI 응답을 JSON 응답으로 반환하는 방식을 설계했다.
- 응답 예시:
```json
{
	"message": "메시지가 성공적으로 생성되었습니다.",
	"user_msg": "Hello, AI!",
	"ai_response": {"content": "Hi! How can I assist you?"}
}
```


### `ModelSerializer`와 `read_only_fields`

- `ModelSerializer`를 사용하여 직렬화와 유효성 검증을 자동화할 수 있음을 학습했다.
- `read_only_fields`는 클라이언트가 수정하지 못하도록 설정하는 데 유용하며, 자동 생성되는 필드(`id`, `created_at`)에 주로 사용된다.


---
## 2. TroubleShooting
### 오류 디버깅
- **오류 메시지: "string indices must be integers, not 'str'."**
    - 원인은 문자열 데이터를 딕셔너리처럼 접근(`user_msg["user_msg"]`)하려 한 것이었다.
    - `request.data.get("user_msg")`가 문자열을 반환한다는 점을 인지하고, 이를 문자열로 그대로 사용하여 문제를 해결했다.


### 코드 개선
- 중복된 객체 선언을 제거하고, 이미 가져온 ForeignKey 객체(`chatroom_instance`)를 재사용하는 방식으로 코드 간결성을 높였다.
- 예외 처리 시, 특정 예외(`ChatRoom.DoesNotExist`)를 별도로 처리하여 더 명확한 오류 메시지를 반환했다.