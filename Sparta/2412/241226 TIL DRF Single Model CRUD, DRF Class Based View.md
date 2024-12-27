#TIL #스파르타코딩클럽 [[2412]]

## 1. DRF Single Model CRUD
### DRF Single Model CRUD
- ModelSerializer
	- Model의 여러가지 필드들을 어떻게 직렬화해서 데이터의 포맷을 잡을지가 핵심
	- Django의 Model → ModelForm 사용과 굉장히 유사한 형태

- API 설계

| Name          | Method | Endpoint                   |
| :------------ | :----- | :------------------------- |
| Article 목록 조회 | GET    | /articles/                 |
| Article 상세 조회 | GET    | /articles/<int:article_id> |
| Article 생성    | POST   | /articles/                 |
| Article 수정    | PUT    | /articles/<int:article_id> |
| Article 삭제    | DELETE | /articles/<int:article_id> |
```python
# api_pjt/urls.py
urlpatterns = [
    path("admin/", admin.site.urls),
    path("api/v1/articles/", include("articles.urls")),
]
```


### Article 목록 조회 (List)
- `articles/urls.py`
```python
app_name = "articles"
urlpatterns = [
    path("", views.article_list, name="article_list"),
]
```

- `articles/views.py`
```python
from rest_framework import status
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .serializers import ArticleSerializer
from django.shortcuts import render, get_object_or_404
from .models import Article

@api_view(["GET", "POST"])  # DRF의 method는 api_view라는 decorator가 필요 
def article_list(request):
    if request.method == "GET":
        # 1. article들을 다 가져오기 
        articles = Article.objects.all()
        # 2. 가져온 articles를 json으로 직렬화하는 serializer 선언 
        serializer = ArticleSerializer(articles, many=True)
        # 3. data를 직렬화해서 Response로 반환 
        return Response(serializer.data)
    
    elif request.method == "POST":
        serializer = ArticleSerializer(data=request.data)
        # raise_exception=True: serializer가 유효하지 않으면 에러 발생 
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
```

- `@api_view()`
	- 아무것도 적지 않으면 `GET` 만 허용, 이외의 요청일경우 `405 Method Not Allowed`
	- DRF의 함수형 뷰의 경우 필수적으로 작성이 필요


### Article 상세 조회 (Detail)
- `articles/urls.py`
```python
app_name = "articles"
urlpatterns = [
    path("", views.article_list, name="article_list"),
    path("<int:pk>/", views.article_detail, name="article_detail"),
]
```

- `articles/views.py`
```python
from rest_framework import status
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .serializers import ArticleSerializer
from django.shortcuts import render, get_object_or_404
from .models import Article

@api_view(["GET", "PUT", "DELETE"])
def article_detail(request, pk):
    article = get_object_or_404(Article, pk=pk)
    if request.method == "GET":
        serializer = ArticleSerializer(article)
        return Response(serializer.data)
    
    elif request.method == "PUT":
        # partial=True: 모든 field를 수정하는 것이 아니라 몇몇의 필드만 수정 
        serializer = ArticleSerializer(article, data=request.data, partial=True)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data)
    
    elif request.method == "DELETE":
        article.delete()
        data = {"pk": f"{article.pk} is deleted."}
        return Response(data, status=status.HTTP_204_NO_CONTENT)
```

- from rest_framework import status
	- 조금 더 읽기 쉬운 status code 구성을 제공
	- 상태코드를 정수로 명시해도 되지만 아래와 같은 형식을 권장


---
## 2. DRF Class Based View
### Class Based View (CBV)
- views의 함수들을 class 안의 함수들로 정의해서 사용하는 것
- 클래스형 뷰에서는 특정 Http Method에 대한 처리를 함수로 분리 가능
    → GET요청에 대한 처리는 `get()`에서, POST 요청에 대한 처리는 `post()`에서 정의
- 클래스를 사용하기 때문에 코드의 재사용성과 유지보수성이 향상
- 기본 `APIView`외에도 여러 편의를 제공하는 다양한 내장 CBV가 존재


### **Class Based View 종류**
- `APIView`
	- DRF CBV의 베이스 클래스
- `GenericAPIView`
    - 일반적인 API 작성을 위한 기능이 포함된 클래스
    - 보통 CRUD 기능이 대부분인 상황을 위해 여러가지 기능이 미리 내장되어 있음
- `Mixin`
    - 재사용 가능한 여러가지 기능을 담고있느 클래스
    - 말그대로 여러 클래스를 섞어서 사용하기 위한 클래스
        - `ListModelMixin` - 리스트 반환 API를 만들기 위해 상속 받는 클래스
        - `CreateModelMixin` - 새로운 객체를 생성하는 API를 만들기위해 상속 받는 클래스
- `ViewSets`
    - 여러 엔드포인트(endpoint)를 한 번에 관리할 수 있는 클래스
    - RESTful API에서 반복되는 구조를 더 편리하게 작성할 수 있는 방법을 제공


### CBV 사용해보기
- ArticleListAPIView
	- `as_view()` 메서드를 사용해서 URL 패턴에 연결
```python
# articles/urls.py
app_name = "articles"
urlpatterns = [    
    path("", views.ArticleListAPIView.as_view(), name="article_list_create"),
]
```
```python
# articles/views.py
from rest_framework.views import APIView

class ArticleListAPIView(APIView):
    def get(self, request):
        # 1. article들을 다 가져오기 
        articles = Article.objects.all()
        # 2. 가져온 articles를 json으로 직렬화하는 serializer 선언 
        serializer = ArticleSerializer(articles, many=True)
        # 3. data를 직렬화해서 Response로 반환 
        return Response(serializer.data)
    
    def post(self, request):
        serializer = ArticleSerializer(data=request.data)
        # raise_exception=True: serializer가 유효하지 않으면 에러 발생 
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
```

- ArticleDetailAPIView
```python
# articles/urls.py
app_name = "articles"
urlpatterns = [    
    path("", views.ArticleListAPIView.as_view(), name="article_list_create"),
    path("<int:pk>/", views.ArticleDetailAPIView.as_view(), name="article_detail"),
]
```
```python
# articles/views.py
from rest_framework.views import APIView

class ArticleDetailAPIView(APIView):
    def get_object(self, pk):
        return get_object_or_404(Article, pk=pk)

    def get(self, request, pk):
        article = self.get_object(pk)
        serializer = ArticleSerializer(article)
        return Response(serializer.data)

    def put(self, request, pk):
        article = self.get_object(pk)
        serializer = ArticleSerializer(article, data=request.data, partial=True)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data)

    def delete(self, request, pk):
        article = self.get_object(pk)
        article.delete()
        data = {"pk": f"{pk} is deleted."}
        return Response(data, status=status.HTTP_200_OK)
```

