#TIL #스파르타코딩클럽 [[2412]]

## 1. Pydantic
### Pydantic
- 파이썬 데이터 검증 및 설정 관리를 위한 라이브러리
- 데이터 모델을 정의하고 유효성을 검사하는 데 주로 사용
- **Python type hints**를 적용하고, 데이터가 invalid 할 때 친숙한 오류를 제공


### Why use Pydantic?
1. **Automatic Data Validation**  
    입력 데이터가 모델에 정의된 타입과 조건을 자동으로 검증
2. **Readable and Maintainable Code**  
    간결하고 명확한 데이터 모델을 정의 가능
3. **Serialization and Deserialization**  
    JSON과 같은 외부 데이터를 쉽게 변환하거나 저장 가능
4. **Error Handling**  
    데이터 유효성 검사가 실패하면 유용한 에러 메시지를 제공


### Key Features
- **BaseModel**: 데이터 모델을 정의하는 기본 클래스
- **Type Hinting**: 데이터 타입을 명확히 지정 가능
- **Validators**: 커스텀 검증 로직 추가
- **Nested Models**: 모델 안에 다른 모델을 포함 가능
- **Immutable Models**: 불변 데이터 모델 정의 가능


### Pydantic 데이터 처리 흐름 다이어그램
```scss
┌──────────────────┐
│ Raw Input Data   │
│ (JSON, Dict, etc)│
└────────▲─────────┘
         │
         │
         ▼
┌──────────────────┐
│ Pydantic Model   │
│ (ex. BaseModel)  │
└────────▲─────────┘
         │
         │ Validation
         │ (Type Hints, Constraints)
         ▼
┌──────────────────┐
│ Validated Data   │
│ (Python Object)  │
└──────────────────┘
```

#### 요소 별 설명
1. Raw Input Data
    - 외부에서 전달 받은 데이터 (예: JSON, Dictionary 등)
    - 사용자는 이 데이터를 Pydantic 모델에 전달
2. Pydantic Model
    - 데이터 구조를 정의하는 Python Class
    - 타입 힌트와 추가적인 조건 (`Field`, `Validators`)으로 구성
    - `BaseModel`을 상속 받아 구현됩니다
3. Validation
    - Pydantic이 데이터의 타입과 조건을 검증하는 과정
    - 데이터가 정의된 타입 및 제약 조건을 만족하지 않을 경우 `ValidationError`를 발생 시킴
4. Validated Data
    - 검증에 성공한 데이터는 Python 객체로 변환
    - 이후 애플리케이션 로직에서 활용 가능

#### 예제 코드
```python
from pydantic import BaseModel, Field, ValidationError

# Define a Pydantic model
class User(BaseModel):
    id: int
    name: str
    email: str = Field(..., regex=r'^\S+@\S+\.\S+$')  # Validate email format
    age: int = Field(..., ge=18, le=120)  # Age must be between 18 and 120

# Create an instance
try:
    user = User(id=1, name="Alice", email="alice@example.com", age=25)
    print(user)
except ValidationError as e:
    print(e.json())

# Invalid data
try:
    invalid_user = User(id="abc", name="Bob", email="invalid-email", age=150)
except ValidationError as e:
    print(e.json())

```
---
```json
// output
// invalid_user의 에러 메시지
[
  {
    "loc": ["id"],
    "msg": "value is not a valid integer",
    "type": "type_error.integer"
  },
  {
    "loc": ["email"],
    "msg": "string does not match regex",
    "type": "value_error.str.regex"
  },
  {
    "loc": ["age"],
    "msg": "ensure this value is less than or equal to 120",
    "type": "value_error.number.not_le"
  }
]

```

