#TIL #스파르타코딩클럽 [[2412]]

## 1. URL Namespace
### URL Namespace
- Django는 서로 다른 앱에서 동일한 URL Name을 사용하는 경우, 고유하게 구분할 수 있도록 namespace를 지원
- url에 이름 공간을 만들어주고 나면, `namespace:url_name`형태로 사용
- 처음부터 `namespace`를 지정하고 프로젝트를 진행하는 것을 권장
- `namespace`없이 경로를 명시했던 경우라면 앞에 `namespace`를 추가
	- `{% url 'url_name' %} -> {% url 'namespace:url_name' %}`
	- `redirect('url_name') -> redirect('namespace:url_name')`

```python
# articles/urls.py
from django.urls import path
from . import views

app_name = "articles"  # add namespace

urlpatterns = [
		...
    path("hello/", views.hello, name="hello"),
    ...
]
```


### Templates 구조
- 서로 다른 앱에서 같은 이름의 html 파일이 있는 경우, Django는 `settings.py`의 `INSTALLED_APPS`의 순서에 따라 가장 먼저 검색된 html 파일을 보여줌
- 매번 그 순서를 신경쓰며 작업할 수 없으므로 Templates Namespace를 사용
- 처음부터 `<app_name>/templates/<app_name>`으로 Templates 구조를 만들고, Templates을 사용할 때는 `<app_name>/<template.html>`으로 사용
	- `render(request, 'articles/index.html')`


---
## 2. Django Auth
### Auth
- 인증(Authentication)과 권한(Authorization)을 합쳐서 Auth라고 대개 인증시스템이라고 명명
	- 인증(Authentication) : 내가 누구인지를 입증하는 것
	- 권한(Authorization) : 수행할 수 있는 자격 여부


### 쿠키(Cookie)와 세션(Session)
#### 만약 쿠키와 세션이 없다면?
- 이전의 요청을 기억하지 못하기 때문에, 요청을 보낼 때마다 매번 로그인이 필요
- client와 server가 서로를 기억하기 위해 필요함

#### 쿠키
- 서버 → 웹 브라우저에 전달하는 작은 데이터 조각
    - 유저가 웹을 방문하게 되면 서버로부터 쿠키를 전달받음
- Key-Value 형태로 쿠키 데이터가 유저의 로컬에 저장
- 이후 동일한 서버에 보내는 모든 요청에 쿠키가 함께 전달
- 그냥 문자열이기 때문에 수정하는 것이 간편함

#### 세션
- 쿠키는 수정이 간편해서 보안에 취약 → 특정 쿠키가 이전에 해당 서버에서 발행한건지 확인하는데 사용하는 데이터
- 서버와 클라이언트 간의 **상태(State)** 를 기억하기 위한 것

- 세션과 쿠키가 쓰이는 방법
	1) 클라이언트가 서버에 접속
	2) 서버가 특정 session id를 발급하고 기억
	3) session id 전달받아 쿠키에 저장
	4) 이후 클라이언트는 해당 쿠키를 이용해서 요청
	5) 서버에서는 쿠키에서 session id를 꺼내서 검증
	6) 검증에 성공했다면 알맞은 로직을 처리

→ 쿠키에 민감한 정보를 저장할 필요 없이 session id만 저장하고 서버에서 검증하는 방식으로 사용
→ 로그인은 이러한 절차로 구현됨

- 쿠키의 수명
    - 세션쿠키 (Session Cookie)
        - 현재의 세션이 종료되면(브라우저가 닫히면) 삭제됨
    - 지속쿠키 (Persistent Cookie)
        - 디스크에 저장되며 브라우저를 닫거나 컴퓨터를 재시작해도 남아있음
        - Max-Age를 지정하여 해당 기간이 지나면 삭제가 가능

- Django의 Session과 Auth
	- `settings.py`의 MIDDLEWARE에서 알아서 처리해줌


### Django의 Authentication System
#### 로그인 구현하기
- 로그인 == Session을 Create하는 로직
- Django는 이 과정을 전부 내부적으로 처리할 수 있는 기능을 제공하기 때문에 직접 session에 대한 로직을 작성할 필요는 없음

- Authentication Form
	- Django의 Built-in Form
	- 로그인을 위한 기본적인 form을 제공

- login()
	- 개발자가 직접 구현하지 않아도 login()함수 하나만 사용하면 로그인 구현 가능
	- 사용자 로그인 처리를 해주고 내부적으로 session을 사용해서 user 정보를 저장

#### 로그아웃 구현하기
- 로그아웃 == 서버의 세션 데이터를 지우는 것

- logout()
	- login()과 마찬가지로 logout()을 사용해 간단하게 기능 구현이 가능
	- 현재 request에서 가져온 session을 사용하여 DB에서 삭제
	- 클라이언트 쿠키에서도 삭제


#### HTTP 요청을 처리하는 다양한 방법
- View Decorators
	- `require_http_methods()`: 특정한 method 요청에 대해서만 허용
	- `require_POST()`: POST 요청만 허용 (`require_http_methods()`의 짧은 버전)

- 접근 제한하기
	- `is_authenticated`: 주로 `request.user`가 Auth가 있는 지 확인
	- `@login_required`
		- 로그인이 안된 상태로 접근하면 `settings.LOGIN_URL`에 설정된 경로로 이동
		- 기본 값: `/accounts/login`
		- 로그인이 되어 있다면 로직을 실행
	- 로그인 성공 시 이전 페이지로 자동으로 이동
		- 쿼리스크링에 `next`로 저장

```python
# accounts/views.py
from django.shortcuts import render, redirect
from django.contrib.auth import login as auth_login
from django.contrib.auth import logout as auth_logout
from django.contrib.auth.forms import AuthenticationForm
from django.views.decorators.http import require_POST, require_http_methods

@require_http_methods(["GET", "POST"])  # GET과 POST가 들어올 때만 login 실행 
def login(request):
    if request.method == "POST":
        form = AuthenticationForm(data=request.POST)
        if form.is_valid():
            auth_login(request, form.get_user())  # 실제 로그인 처리를 진행하는 부분 
            
            # 로그인 성공하면 가려던 곳(next)으로 가고, 아니면 index로 이동 
            next_path = request.GET.get("next") or "index"
            return redirect(next_path)
    else:
        form = AuthenticationForm()
    context = {"form": form}
    return render(request, "accounts/login.html", context)

@require_POST  # POST 요청이 들어올 때만 logout 함수 실행 
def logout(request):
    if request.user.is_authenticated:
        auth_logout(request)  # 실제 로그아웃 처리를 진행하는 부분 
    return redirect("articles:index")
```