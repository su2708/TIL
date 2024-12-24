#TIL #스파르타코딩클럽 [[2412]]

## 1. Response와 Serializer
### django seed
- 개인의 Django models에 맞게 test data를 생성해주는 라이브러리
- 적용 방법
	1) `pip install django-seed`
	2) `settings.py`에 `django_seed` 추가
	3) `python manage.py seed <앱 이름> --number=<개수>`
```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    # Third-party
    "django_seed",
    # Local
    "articles",
    "accounts",
]
```


### Response와 Serialization
1. Response
	- Response는 서버가 클라이언트 요청에 대해 반환하는 HTTP 응답
	
	- Django REST Framework에서 `Response` 객체는 JSON 형식의 데이터를 반환할 때 많이 사용
	- 주요 특징
		- Django의 기본 `HttpResponse`를 확장한 객체로, RESTful API에 적합한 JSON 응답을 줌
		- 데이터 직렬화와 콘텐츠 협상을 자동으로 처리
		- 상태 코드와 헤더를 쉽게 설정 가능
	
	- `JsonResponse`
		- JSON으로 인코딩된 response를 만드는 `HttpResponse`의 서브 클래스
		- dict 타입이 아닌 객체를 직렬화(Serialization)하기 위해서는 `safe=False`로 설정

2. Serialization
	- Python 객체를 JSON, XML 등의 포맷으로 변환하거나, 그 반대로 역직렬화(Deserialization)하는 과정
	- 데이터를 클라이언트와 서버 간에 주고받기 위한 중요한 과정
	
	- 주요 기능
		- **직렬화(Serialization)**: Python 데이터 구조(Python 객체, QuerySet 등)를 JSON 형식으로 변환
		- **역직렬화(Deserialization)**: 클라이언트로부터 받은 JSON 데이터를 Python 데이터로 변환

	- Django는 내부적으로 serializer를 제공하지만 유연하지 않고 모델 구조에 한정된 구조
	- 모델에 종속적이지 않고 유연하지만 사용하기 편한 Serializer가 필요