#TIL #스파르타코딩클럽 [[2412]]

## 1. Django Form
### Django Form Class
- 유저가 입력하는 데이터는 반드시 **유효성 검사가 필요**하지만 이 과정에서 중복되는 코드가 발생
- Django에서는 반복 작업을 줄일 수 있는 Form을 제공하지만 꼭 그것을 사용할 필요는 없음
	- 직접 구현한 Form+View 로직을 사용해도 괜찮음


#### Form 선언하기
- Model 선언과 비슷하게 내가 이 Form에서 입력받고자 하는 **데이터에 대한 명세를 작성**
```python 
# articles/forms.py
from django import forms

class ArticleForm(forms.Form):
    title = forms.CharField(max_length=10)
    content = forms.CharField()
```

#### Form 적용하기
- 새 글을 작성할 때 적용
```html
<!-- articles/templates/new.html -->
<h1>New Articles</h1>

    <form action="{% url 'create' %}" method="POST">
        {% csrf_token %}
        {{ form.as_p }}  <!-- form 적용 -->
        <label for="title">제목:</label>
        <input type="text", id="title", name="title", placeholder="Enter messagaes.">
        <br/>
        <br/>

        <label for="content">내용</label>
        <textarea name="content" id="content" cols="30" rows="10"></textarea>
        <br/>
        <br/>

        <button type="submit">저장</button>
    </form>
```
```python
# articles/views.py
from.forms import ArticleForm
...
def new(request):
	form = ArticleForm()
	context = {
		"form": form,
	}
	return render(request, "new.html", context)
...
```

- Form rendering options
	- label과 input을 렌더링하는 것에 대한 옵션
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
    class Meta:
        model = Article
        fields = "__all__"
        # exclude = ["title"]
```
