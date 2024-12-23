#TIL #스파르타코딩클럽 [[2412]]

## 1. Model Relationship (1:N)
### 1:N 관계
-  `Article`에 `Author`라는 개념을 둔다면,
    - 하나의 `Article`은 한 명의 `Author`를 가질 수 있음
    - **한 명의 `Author`는 여러개의 `Article`을 가질 수 있음 (1:N)**
- 만약 `Article`에 `Comment`라는 개념을 둔다면,
    - **하나의 `Article`은 여러개의 `Comment`를 가질 수 있음 (1:N)**
    - 하나의 `Comment`는 하나의 `Article`을 가질 수 있음


### Foreign Key
- **외래 키**를 의미
- 관계형 DB에서 한 테이블(A)의 필드 중 다른 테이블(B)의 행을 **유일하게 식별이 가능한 키**
- 테이블(A)에 설정되는 Foreign Key가 반드시 다른 테이블(B)의 Primary Key일 필요는 없으나 유일하게 식별이 가능해야함


### 정참조 & 역참조
- 정참조
	- Comment(N) → Article(1) 참조 == 정참조
	- Comment는 하나의 참조하는 Article이 존재하므로 `comment.article`로 쉽게 접근 가능 

- 역참조
	- Article(1) → Comment(N) 참조 == 역참조
	- Django는 역참조의 경우 참조하는 클래스명에 `_set`을 붙인 manager를 생성
	- manager 이름을 변경하려면 아래와 같이 변경 가능
```python
# 작성하는 댓글에 대한 모델
class Comment(models.Model):
    article = models.ForeignKey(
        Article, on_delete=models.CASCADE, related_name="comments" # manager 이름 변경
    )
    ...
```


---
## 2. Custom UserModel
### Custom UserModel
- Django는 **`AUTH_USER_MODEL` setting을 변경하여 기본 User Model을 대체 가능**
- 기본 User Model을 사용하더라도 **Custom User Model을 사용하는 것이 권장**됨
- `AUTH_USER_MODEL` 설정은 **반드시 프로젝트 최초 마이그레이션에서 함께 진행하는 것을 권장**

```python
# settings.py
AUTH_USER_MODEL = "accounts.User"
```
```python
# accounts/models.py
from django.db import models
from django.contrib.auth.models import AbstractUser

# Create your models here.
class User(AbstractUser):
    pass  # 기본 UserModel을 사용하더라도 Custom User Model을 정의하고 사용하는 걸 권장 
```
```python
# accounts/forms.py
class CustomUserCreationForm(UserCreationForm):
    class Meta:
        model = get_user_model()  # 현재 활성화된 user model인 User를 model로 사용 
        fields = UserCreationForm.Meta.fields + ()
```

