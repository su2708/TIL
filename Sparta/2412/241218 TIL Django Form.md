#TIL #스파르타코딩클럽 [[2412]]

## 1. Django Form
### Django Form Class
- 유저가 입력하는 데이터는 반드시 **유효성 검사가 필요**하지만 이 과정에서 중복되는 코드가 발생
- Django에서는 반복 작업을 줄일 수 있는 Form을 제공하지만 꼭 그것을 사용할 필요는 없음
	- 직접 구현한 Form+View 로직을 사용해도 괜찮음


#### Form 선언하기
- Model 선언과 비슷하게 내가 이 Form에서 입력받고자 하는 **데이터에 대한 명세를 작성**
```python
# articles/models.py
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=50)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    def __str__(self):
        return self.title
```
```python 
# articles/forms.py
from django import forms

class ArticleForm(forms.Form):
    title = forms.CharField(max_length=50)
    content = forms.CharField(widget=forms.Textarea)
```

#### Form 적용하기
- 새 글을 작성할 때 적용
```python
# articles/views.py
from.forms import ArticleForm
⁝
def new(request):
	form = ArticleForm()
	context = {
		"form": form,
	}
	return render(request, "new.html", context)
⁝
```
```html
<!-- articles/templates/new.html -->
<h1>New Articles</h1>

    <form action="{% url 'create' %}" method="POST">
        {% csrf_token %}
        {{ form.as_p }}  <!-- form 적용 -->
        <button type="submit">저장</button>
    </form>
```

- Form rendering options
	- label과 input을 렌더링하는 것에 대한 옵션
		- as_div: \<div> 태그로 감싸져서 렌더링
		- as_p: \<p> 태그로 감싸져서 렌더링
	- 공식 문서: [Form Rendering Options](https://docs.djangoproject.com/en/4.2/topics/forms/#form-rendering-options)

- Form Widget
	- 웹 페이지에서 Form Input 요소가 어떻게 렌더링 되어서 보여질지 정의
	- Form Fields에 할당해서 사용
	- 공식 문서: [Form Widget]([https://docs.djangoproject.com/en/4.2/topics/forms/#widgets](https://docs.djangoproject.com/en/4.2/ref/forms/widgets/#module-django.forms.widgets))


### Django Model Form
- Django Form이 Model과 상당히 유사 -> 누가 자동으로 만들어줄 수 없나?
- ModelForm Class: Django가 알아서 Model을 참조해 Model Field를 보고 Form을 제작

#### Model을 통해서 Form Class를 만드는 방식
- ModelForm이 사용할 데이터를 Meta 클래스에 명시
- fields 항목에 내가 form으로 만들고 싶은 항목들을 지정 가능
```python
# articles/forms.py
from django import forms
from articles.models import Article

class ArticleForm(forms.ModelForm):
    # '어떤 모델을 가져와서 Form을 만들어야 하는가?'를 Meta class에 정의 
    class Meta:
        model = Article  # 가져올 model 
        fields = "__all__"  # model에서 가져올 속성들 
        # exclude = ["created_at", "updated_at"]  # fields 중에서 제외할 것들 

```
```python
# articles/views.py
⁝
def create(request):
  form = ArticleForm(request.POST) # form에 request.POST에 있는 데이터 채워
  if form.is_valid(): # form 형식에 맞으면
      article = form.save() # 저장하고 해당 객체 반환 
      return redirect("article_detail", article.id)
  return redirect("new")
⁝
```

#### 유사한 함수들을 병합하기
1. new - create 수정하기
	- `views.py`의 `new`함수와 `create`가 상당히 유사하게 생김
		- `new`함수: request.method == GET 이면 새로운 빈 Form을 만들어 보여줌
		- `create` 함수: request.method == POST이면 값이 채워진 Form을 새롭게 만들어 저장
		- request 요청에 따라 Form을 만드는 것이 유사함
	- `new`함수를 `create`에 병합
		- `views.py`에서 `new`함수 지우기
		- `urls.py`에서 `new`로 가는 path 지우기
		- `new.html`의 이름을 `create.html`로 바꾸기

```python
# new함수를 create함수에 병합 
def create(request):
    # 기존 create 함수 부분 
    if request.method == "POST":
        form = ArticleForm(request.POST)  # 데이터가 바인딩된(값이 채워진) Form 
        if form.is_valid():  # Form이 유효하다면 데이터를 저장하고 다른 곳으로 redirect 
            article = form.save()
            return redirect("article_detail", article.pk)
    # 기존 new 함수 부분 
    else:
        form = ArticleForm()

    context = {"form": form}
    return render(request, "create.html", context)
```
```html
<!-- articles.html에서도 new url을 create url로 변경 -->
<h1>Articles</h1>

<a href="{% url 'create' %}"><button>Create New Article</button></a>

{% for article in articles %}
	<!-- article_detail에 필요한 인자인 article.pk를 같이 넘겨줌 -->
	<a href="{% url 'article_detail' article.pk %}">
		<p>[ {{ article.pk }} ] {{ article.title }}</p>
	</a>
	<br/>
{% endfor %}
```
```html
<!-- new.html을 create.html로 변경 -->
<h1>New Articles</h1>

<form action="{% url 'create' %}" method="POST">
	{% csrf_token %}
	{{ form.as_p }}  <!-- form을 p 태그로 감싸겠다는 의미 -->
	<button type="submit">저장</button>
</form>

<br/>
<a href="{% url 'articles' %}">목록 보기</a>
```

2. edit - update 수정하기
	- `views.py`의 `edit`함수와 `update`가 상당히 유사하게 생김
		- `edit`함수:  GET 이면 article값으로 채워진 Form을 만들어 내용 수정
		- `update` 함수: POST이면 article값으로 채워진 Form을 만들어 수정된 내용 저장 
		- request 요청에 따라 article값으로 채워진 Form을 만들어 내용을 수정하는 것이 유사
	- `edit`함수를 `update`에 병합
		- `views.py`에서 `edit`함수 지우기
		- `urls.py`에서 `edit`로 가는 path 지우기
		- `edit.html`의 이름을 `update.html`로 바꾸기
```python
# edit함수를 update함수에 병합 
def update(request, pk):
    article = Article.objects.get(pk=pk)
    if request.method == "POST":
        form = ArticleForm(request.POST, instance=article)
        if form.is_valid():
            article = form.save()
            return redirect("article_detail", article.pk)
    else:
        form = ArticleForm(instance=article)  # article의 값을 가지는 객체로 채워진 Form 
    context = {
        "form": form,
        "article": article,
    }
    return render(request, "update.html", context)
```
```html
<!-- article_detail.html에서도 edit url을 update url로 변경 -->
<h1>Article Detail</h1>
⁝
<a href="{% url 'articles' %}">목록보기</a><br/>
<a href="{% url 'update' article.pk %}"><button>수정</button></a>
```
```html
<!-- edit.html을 update.html로 변경 -->
<h1>Update Article</h1>

<form action="{% url 'update' article.pk %}" method="POST">
	{% csrf_token %}
	{{ form.as_p }}
	<button type="submit">수정</button>
</form>

<br/>
<a href="{% url 'article_detail' article.pk %}">이전으로</a>
```
