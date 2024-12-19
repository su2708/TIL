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
