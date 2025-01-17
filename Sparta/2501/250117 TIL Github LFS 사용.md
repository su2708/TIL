#TIL #스파르타코딩클럽 [[2501]]

## 1. 한 일
### Github 대용량 파일 업로드(LFS) 사용
- LFS:
	- Git 리포지토리에 업로드할 수 있는 파일 용량은 최대 500 MB로 제한되지만, Git LFS(Large File System)를 활용하면 Git 리포지토리에서 대용량 파일(파일 당 최대 5 GB)의 버전 관리를 수행 가능
	- Git LFS는 대용량 파일에 대한 메타데이터(metadata)만 리포지토리에 커밋하고 실제 파일 내용은 별도의 원격 저장소에 저장하는 방식으로 작동

### Django View 및 모델 설계와 디버깅
- 뷰 설계:
    - `APIView`를 활용하여 Django REST Framework로 다양한 API 엔드포인트를 작성.
    - `get`, `post`, `delete` 메서드를 통해 채팅방, 메시지, 시험 계획에 대한 CRUD 기능 구현.
- 모델 설계:
    - `ChatRoom`과 `ChatMessage` 모델 간의 관계를 정의하고, `ForeignKey` 및 `unique_together` 제약 조건 사용.
    - `TestPlan` 모델에서 `ChatRoom`과의 연관성을 설정하고 데이터의 유효성을 보장.
- 디버깅:
    - `post` 메서드에서 발생한 `UNIQUE constraint failed` 에러를 분석하고, 중복 데이터 확인 로직 추가.
    - `NoneType` 반환 에러를 해결하기 위해 모든 분기에서 `Response` 객체를 반환하도록 수정.


---
## 2. TroubleShooting
### `index.faiss, index.pkl`파일 github upload 문제
- **문제**:
    - `index.faiss`, `index.pkl` 두 파일의 크기가 커서 github에 올라가지 않음
- **해결**:
    - `Git LFS`사용으로 해결
    - [대용량 파일 업로드(LFS) 사용](https://guide.ncloud-docs.com/docs/sourcecommit-use-lfs)

### `UNIQUE constraint failed` 에러
- **문제**:
    - `TestPlan` 생성 시 동일한 `chatroom_id`에 중복 데이터가 삽입되면서 데이터베이스의 `UNIQUE` 제약 조건이 위반됨.
- **해결**:
    - `TestPlan` 생성 전에 해당 `chatroom_id`로 이미 존재하는 `TestPlan`이 있는지 확인하는 검증 로직 추가:
```python
existing_plan = TestPlan.objects.filter(chatroom=chatroom).first()
if existing_plan:
    return Response({"message": "이미 이 채팅방에 시험 계획이 존재합니다."}, status=status.HTTP_400_BAD_REQUEST)
```

### `NoneType` 반환 에러
- **문제**:
    - 뷰 메서드에서 예외가 발생할 경우 `Response` 객체를 반환하지 않아 `NoneType` 에러 발생.
- **해결**:
	- 예외 상황에서도 항상 `Response` 객체를 반환하도록 수정:
```python
except Exception as e:
    return Response({"message": f"에러 발생: {str(e)}"}, status=status.HTTP_500_INTERNAL_SERVER_ERROR)
```


---
## 3. 느낀 점

### Django REST Framework의 강력함과 세부적인 설계의 중요성
- DRF를 활용하면 직관적인 API 설계가 가능하지만, 데이터 유효성 검증 및 예외 처리 로직을 철저히 작성해야 안정적인 서비스를 구현할 수 있음을 깨달음.

### 데이터베이스 제약 조건 관리의 중요성
- `UNIQUE constraint`와 같은 제약 조건은 데이터 무결성을 보장하지만, 이를 사전에 검증하지 않으면 예상치 못한 에러가 발생할 수 있음.
- 중복 데이터 검증 로직을 설계하면서 데이터베이스와 애플리케이션 간의 협력이 중요함을 실감.

### 디버깅 경험에서 얻은 자신감
- 에러 로그를 통해 문제를 분석하고, 재현 가능한 해결책을 설계하면서 디버깅 실력이 향상됨.
- 단순히 문제를 해결하는 데 그치지 않고, 향후 발생할 수 있는 유사한 문제를 예방하는 데 초점을 맞추게 됨.