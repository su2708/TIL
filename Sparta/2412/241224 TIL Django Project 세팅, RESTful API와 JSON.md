#TIL #스파르타코딩클럽 [[2412]]

## 1. Django Project 세팅
### **Django Project 기본 세팅**
1. 가상환경 생성 및 활성화
2. Django 설치: `pip install django==4.2 (LTS version)`
3. `requirements.txt` 생성: `pip freeze > requirements.txt`
4. 프로젝트 생성: `django-admin startproject <프로젝트 이름> <생성 디렉토리>`
5. 앱 생성: `python manage.py startapp <앱 이름>`
	- `settings.py`의 `INSTALLED_APPS`에 생성한 앱 이름 추가
	- `<프로젝트 이름>/urls.py`에서 앱으로 향하는 url들을 분리해서 path 지정
	- `<앱 이름>/urls.py`에서 app_name을 주고 url마다 path 지정
6. `Custom UserModel` 생성
	- `accounts` 앱 생성
	- `settings.py`의 `INSTALLED_APPS`에 `accounts` 추가하고, `AUTH_USER_MODEL = "accounts.User"` 도 추가
	- `<프로젝트 이름>/urls.py`에서 앱으로 향하는 url들을 분리해서 path 지정
	- `accounts/urls.py`에서 app_name을 주고 url마다 path 지정
	- `accounts/models.py`에서 빈 User class 생성
	- `accounts/forms.py`을 생성해 `CustomUserCreationForm` 클래스 생성
7. 최초 `migration` 생성
	- `python manage.py makemigrations`
	- `python manage.py migrate`


---
## 2. RESTful API와 JSON
### RESTful API
-  API란?
	- CLI (Command Line Interface) - 명령줄로 소통하는 방법
	- GUI (Graphic User Interface) - 그래픽으로 유저와 소통하는 방법
	- API (Application Programming Interface) - 프로그래밍으로 어플리케이션과 소통하는 방법

-  RESTful API란?
	- REST: 웹에 대한 소프트웨서 설계 방법론
	- 어플리케이션간 소통하는 방법에 REST적인 표현을 더한 것 → REST 원리를 따라 설계한 API