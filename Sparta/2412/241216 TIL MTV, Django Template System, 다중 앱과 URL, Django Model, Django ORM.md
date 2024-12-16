#TIL #스파르타코딩클럽 [[2412]]

## 1. Django의 설계 철학 - MTV Pattern
### 소프트웨어의 디자인 패턴
- 디자인 패턴: **자주 사용되는 소프트웨어의 구조를 일반화 해둔 것**
- 디자인 패턴의 필요성
	- 공통적으로 발생하는 문제에 대해 재사용 가능한 해결 방법을 제시 가능
	- 특정 구조에 대한 빠른 설계가 가능
	- 즉, 프로그래머가 시스템을 디자인할 때 발생하는 공통된 문제를 해결하면서 진행할 수 있는 형식화된 관행


### MVC vs MTV
|  **MVC**   | **MTV**  |                **역할**                 |
| :--------: | :------: | :-----------------------------------: |
|   Model    |  Model   | 데이터 구조 정의, DB 기록 관리 등 데이터와 관련된 로직을 처리 |
|    View    | Template |        UI, 레이아웃 등 화면상의 로직을 처리         |
| Controller |   View   |       클라이언트 요청에 대해 처리를 분기하는 역할        |
- 역할에 따라 분리하는 이유
	- 관심사 분리
	- 각 부분을 독립적으로 개발할 수 있어서 생산성이 증가하고 유지 보수가 쉬워짐
	- 다수의 멤버가 동시에 개발하기도 용이함

- 순서도
![[MTV.webp]]
1) HTTP 요청을 urls.py에서 수신
2) urls.py에서 HTTP 요청에 맞는 View로 이동
3) 해당 View에서 필요한 데이터를 Model과 송수신
4) 송수신한 데이터를 화면에 보여주기 위해 Template과 통신
5) 완성된 작업물을 HTTP 응답으로 내보냄


---
## 2. Django Template System
### DTL (Django Template Language)
- Django Template에서 사용하는 문법
- Django 개발자를 위해 Python과 비슷한 사용 방법을 가짐
- Python과 비슷하다고 해서 Python이 동작하는 것이 아님에 유의


### 변수 (Variable)
- **변수의 기본 형태: `{{ variable }}`**
- view의 context로 넘긴 데이터를 접근할 수 있음
- **`.`** 을 사용하여 변수의 속성 값에 접근 가능
- `render()`의 세번째 인자인 context에 **`dict()`** 형태로 데이터가 넘겨짐
- 넘겨진 데이터에서 **`key`** 값이 template에서 사용 가능한 변수가 됨


### 필터 (Filter)
- **필터의 기본 형태: `{{ variable|filter }}`**
- 변수에 어떠한 작업을 추가적으로 더해 수정하고 싶을 때 사용
- 약 60개의 built-in filter가 제공되며 일부 필터는 인자를 받기도 함


### 태그 (Tag)
- **태그의 기본 형태: `{% tag %}`**
- 반복문 또는 논리, 조건문을 수행하여 제어 흐름을 만들거나 특수한 기능을 수행
- 일부는 시작 태그와 종료 태그가 있음
```django
{% if ~ %}
{% endif %}
```


### 주석 (Comment)
```django
{# 한 줄 주석 #}

{% comment %}
	여러줄
	주석
{% endcomment %}
```


### 템플릿 상속 (Template Inheritance)
- 코드의 재사용성을 높여 코드 중복 문제를 해결
- 상위 템플릿에 공통이 될 부분을 정의하고 하위 템플릿에서 달라질 부분을 block으로 만드는 skeleton 형태

- 상위 템플릿
	- **`{% block block_name %}{% endblock block_name %}`**
	- 상위 템플릿에서 하위 템플릿마다 달라질 부분을 정의

- 하위 템플릿
	- **`{% extends 'template_name' %}`** 
	- 하위 템플릿에서 상위 템플릿을 상속한다는 의미
	- 하위 템플릿의 최상단에 위 명령어가 위치해야함
	- 다중 상속을 지원하지 않음


---
## 3. 다중 앱과 URL
### Variable Routing
- URL 일부를 변수로 지정하여, 해당 부분에 들어온 값을 view로 넘겨줄 수 있음
- view에서 변수를 받아서 그 부분에 맞게 처리
- 하나의 URL에 여러 페이지를 연결하는 것이 가능
```python
# users/urls.py
path("profile/<str:username>/", views.profile)

# users/views.py
def profile(request, username):
	context = {"username": username}
	return render(request, "profile.html", context)
```
```html
# users/templates/profile.html
<h1>{{ username }} Profile Page</h1>
<p> username : {{ username }} </p>
```


### Multiple Apps
- 하나의 프로젝트는 여러 개의 앱으로 구성됨
- 각각의 기능별로 나누어서 App으로 분리하는 것이 좋은 구조
```bash
.
|-- articles  // `articles/` 요청을 처리하는 app
|   ⁝
|   |-- templates
|   |   |-- data-catch.html
|   |   |-- data-throw.html
|   |   |-- hello.html
|   |   `-- index.html
|   |-- urls.py
|   |   `-- views.py
|-- db.sqlite3
|-- manage.py
|-- my_first_pjt
|   ⁝
|   |-- settings.py
|   `-- urls.py
|-- templates
|   `-- base.html
`-- users  // `users/` 요청을 처리하는 app
    ⁝
    |-- templates
    |   |-- profile.html
    |   `-- users.html
    |-- urls.py
    `-- views.py
```


### Naming URL Patterns
- 어떠한 URL을 작성할 때 직접 경로를 넣지 않고 각각의 URL에 '이름'을 붙여주는 것

```python
# /articles/urls.py
urlpatterns = [
    path("hello/", views.hello, name="hello"),
    # data
    path("data-throw/", views.data_throw, name="data-throw"),
    path("data-catch/", views.data_catch, name="data-catch"),
]
```
```html
<!-- /articles/templates/index.html -->
<!-- href에 경로가 아닌 path의 name을 대신 적어서 경로를 표현 -->
{% block content %}
    <h1>INDEX</h1>
    <nav>
        <ul>
            <li><a href="{% url 'hello' %}">Hello</a></li>
            <li><a href="{% url 'data-throw' %}">Data Throw</a></li>
            <li><a href="{% url 'users' %}">Users</a></li>
        </ul>
    </nav>

    <p>My first Django project is working!</p>
{% endblock content %}
```


---
## 4. Django Model
### 용어 정리
#### Model
- 저장할 데이터에 대한 필드와 동작들을 포함한 데이터베이스 구조
- Django는 Model을 이용해서 데이터를 조작
- 일반적으로 하나의 Model은 하나의 데이터베이스 테이블을 의미


#### 데이터베이스(Database)
- 잘 정리된 데이터가 모여있는 것


#### 쿼리(Query)
- 데이터베이스를 조작할 수 있는 언어


#### 스키마(Schema)
- 데이터베이스의 구조, 관계 등을 정의한 것


#### 테이블(Table)
- 열(Column) - 속성 / 필드(Field)
- 행(Row) - 데이터 / 레코드(Record) / 튜플(Tuple)
- 조직화된 데이터 요소들의 집합


#### 데이터베이스 기본 구조
- 테이블(Table)
- 기본키, PK(Primary Key)
- 열(Column)
- 행(Row)


### 모델 작성해보기
- `modles.Model`을 상속받아서 사용하고자 하는 데이터 스키마를 정의
- 각각의 필드는 테이블의 컬럼
- 필드의 타입에 따라 사용하며, 각 필드별로 필요한 옵션들이 존재
> 참고: [Model Field reference](https://docs.djangoproject.com/en/4.2/ref/models/fields/#field-types)
```python
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=50)  # Article table의 title column
    content = models.TextField()  # content column
    created_at = models.DateTimeField(auto_now_add=True)  # created_at column
    updated_at = models.DateTimeField(auto_now=True)  # updated_at column
```


### 마이그레이션(Migration)
- 현재는 Model 코드는 작성했지만 데이터베이스에 반영이 안된 상태
- **Django는 마이그레이션을 만들고 이 단위로 데이터베이스에 변경사항을 반영**


### 마이그레이션 관련 명령어
- **model에 생긴 변경사항을 마이그레이션으로 만드는 과정**
```shell
python manage.py makemigrations
```

- **마이그레이션을 실제로 데이터에 반영해서 동기화하는 과정**
```shell
python manage.py migrate
```

- 마이그레이션 목록과 적용여부를 보여주는 명령어
```shell
python manage.py showmigration
```

- 해당 마이그레이션이 어떤 sql문을 작성했는지 보여주는 명령어
```shell
python manage.py sqlmigrate <app_name> <migration_no>
```


---
## 5. Django ORM
### ORM (Object-Relational-Mapping)
- 파이썬으로 데이터베이스를 조작할 수 있게 해준다는 것
- SQL을 잘 알지 못해도 DB를 조작 할 수 있고, 잘 알더라도 기존의 복잡한 쿼리 문 없이 객체 지향적인 접근이 가능


### Database API
- Django ORM으로 Database API를 사용해서 DB를 조작
- **Manager**
	- 모델 클래스를 생성하면 Django는 자동적으로 CRUD 할 수 있는 Database API를 제공
	- API와 함께 모델 클래스를 이용하여 DB의 쿼리 작업을 도와주는 Manager도 제공
	- Manager의 기본 이름은 **`objects`** 
	- Manager를 이용해서 Django ORM의 Queryset API를 사용
		- Queryset: ORM을 사용해서 DB로부터 전달받은 객체
		- 기본 형태: **`<Model Class>.<Manager>.<Queryset API>`** 


### CRUD with Shell
- Django shell: Django가 제공하는 여러가지 기능을 명령어로 입력해서 실행할 수 있는 shell 환경
- **CRUD**: 대부분의 소프트웨어가 하는 동작인 Create, Read, Update, Delete의 앞 글자

- 생성(Create)
```python
# 하나의 Article 생성 방법 (1)
article = Article()
article.title = 'first_title'
article.content = 'first_content'

# 여기에서 전체 Article을 조회해보면
Article.objects.all() # 비어있다

# save()하기전에는 저장되지 않음
article.save()

# 다시 전체 Article을 조회해보면 하나의 아티클이 있음
Article.objects.all()


# 속성 하나씩 접근하기
# 제목 
article.title

# 내용
article.content

# 생성일시
article.create_at

# pk(id)
article.id

---
# 하나의 Article 생성 방법 (2)
article = Article(title='second_title', content='second_content')
article.save()

---
# 하나의 Article 생성 방법 (3)
# save()가 필요하지 않음
Article.objects.create(title='third title', content='마지막 방법')
```

- 조회(Read)
```python
# 하나의 Article만 조회하기 (get)
# 조회하는 대상이 하나도 없거나, 2개 이상이면 에러 발생
Article.objects.get(id=1)

---
# 전체 Article 조회
Article.objects.all()

---
# 조건으로 조회하기
# 조건에 사용되는 매개변수를 `lookup`이라고 함
# lookup과 일치되는 객체를 모두 반환
Article.objects.filter(title='second')

# 다양한 lookup을 제공
Article.objects.filter(id__gt=2) # 2보다 큰 id
Article.objects.filter(id__in=[1,2,3]) # 1,2,3에 속하는 id
Article.objects.filter(content__contains='my') # content에 'my'가 포함된
...
```

- 수정(Update)
```python
'''
1. 수정할 객체를 조회
2. 수정할 내용을 입력
3. 수정한 것을 DB에 반영
'''
article = Article.objects.get(id=1)
article.title = 'updated title'
article.save()
```

- 삭제(Delete)
```python
# 삭제할 객체를 조회한 후 삭제
article = Article.objects.get(id=2)
article.delete()
```

