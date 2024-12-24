#TIL #스파르타코딩클럽 [[2412]]

## 1. Model Relationship (M:N)
### ManyToMany
- M:N 관계 설정시 사용하는 모델 필드
    - 예시) 좋아요
        → 한 명의 유저는 여러개의 게시글을 좋아할 수 있어요!
        → 하나의 게시글도 여러명의 유저에게 좋아요를 받을 수 있어요!
        
- 중계 테이블을 이용해서 관계를 표현
- **`models.ManyToManyField(<classname>)`** 을 이용해서 설정
- M2M 관계가 설정되면 역참조시 사용가능한  `_set`이름의 RelatedManager를 생성
	- `related_name` 으로 변경 가능
- `add()`, `remove()`를 이용해서 관련 객체를 추가, 삭제
```python
# articles/models.py

# 작성하는 글에 대한 모델 
class Article(models.Model):
    ...    
    like_users = models.ManyToManyField(
        settings.AUTH_USER_MODEL, related_name="like_articles"
    )
    ...
```

- 중계 테이블을 내가 직접 정의하고, 그것을 지정하는 것도 가능
	- `through`를 사용해 중계 테이블 지정도 가능 
```python
# articles/models.py

# 작성하는 글에 대한 모델 
class Article(models.Model):
    ...    
    like_users = models.ManyToManyField(
        settings.AUTH_USER_MODEL, through="ArticleLike", related_name="like_articles"
    )
    ...

# 좋아요 중계 테이블
class ArticleLike(models.Model):
	user = models.ForeignKey(
		settings.AUTH_USER_MODEL, on_delete=models.CASCADE, related_name="like_articles"
	)

	article = models.ForeignKey(
		Article, on_delete=models.CASCADE, related_name="like_users"
	)
	
	rating = models.IntegerField(default=1)
	created_at = models.DateTimeField(auto_now_add=True)
	updated_at = models.DateTimeField(auto_now=True)
```


### follow
- 한 명의 유저는 여러명의 follower를 가질 수도 있고, 여러 명을 follow 할 수도 있음
	→ **M:N 관계**
- User - User 관계를 맺는 모델을 자기 자신으로 명시
```python
# accounts/models.py
from django.db import models
from django.contrib.auth.models import AbstractUser

# Create your models here.
class User(AbstractUser):
    following = models.ManyToManyField(
        "self", symmetrical=False, related_name="followers"
    )  # symmetrical=True이면 누군가를 팔로우 하자마자 맞팔이 됨. default는 True 
    
```
```python
# users/urls.py
app_name = "users"  # namespace 추가

urlpatterns = [
    # users
    ...
    path("<int:user_id>/follow/", views.follow, name="follow"),
]
```
```python
# users/views.py
from django.shortcuts import render, redirect
from django.views.decorators.http import require_POST
from django.shortcuts import get_object_or_404
from django.contrib.auth import get_user_model

...
def profile(request, username):
    member = get_object_or_404(get_user_model(), username=username)
    context = {
        "member": member,
    }
    return render(request, "users/profile.html", context)

@require_POST
def follow(request, user_pk):
	# request.user == "현재 로그인한 사람"
	# member == "누군가의 프로필"
    if request.user_is_authenticated:
        member = get_object_or_404(get_user_model(), pk=user_pk)
        if request.user != member:  # 자기 자신을 팔로우하는 경우 제외 
            if request.user in member.followers.all():
                member.followers.remove(request.user)
            else:
                member.followers.add(request.user)
        return redirect("users:profile", member.username)
    return redirect("accounts:login")
        
```

