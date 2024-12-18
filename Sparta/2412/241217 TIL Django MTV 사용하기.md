#TIL #스파르타코딩클럽 [[2412]]

## 1. Django MTV 사용하기 (CR)
### GET & POST
- GET
	- 원하는 리소스를 가져올 때 사용
	- 생성할 때도 사용 가능하지만, 묵시적으로 리소스 조회용으로 사용하기로 약속
	- DB에 변화를 주지 않는 요청 (Read에 해당)

- POST
	- 서버로 데이터를 전송할 때 사용
	- 특정 리소스를 생성 혹은 수정하기 위해 사용
	- DB에 변화를 주는 요청 (Create, Update, Delete에 해당)


### 사이트 간 요청 위조 CSRF(Cross Site Request Forgery)
- CSRF
	- 유저가 실수로 해커가 작성한 로직을 따라 특정 웹페이지에 수정/삭제 등의 요청을 보내게 되는 것
	- 즉, 유저가 정말로 **의도한 요청인지 검증이 필요**

- CSRF Token
	- 유저가 서버에 요청을 보낼 때 함께 제공되는 특별한 토큰 값
	- 사용자의 세션과 연결되어 요청이 전송될 때 함께 제출됨
	- 서버는 요청을 받을 때 이 토큰을 검증하여 요청이 유효한지 확인하는 방식으로 CSRF 방지
	- 일반적으로 GET을 제외한 데이터를 변경하는 Method에 적용
	- Django에서는 쉽게 CSRF Token 방식을 구현할 수 있게 template tag로 제공
```django
{% block content %}
<h1>New Article</h1>

<form action="{% url 'create' %}" method="POST">
	<!-- csrf token 구현 -->
	{% csrf_token %}
	
	<label for="title">제목</label>
	<input type="text" name="title" id="title"><br><br>

	<label for="content">내용</label>
	<textarea name="content" id="content" cols="30" rows="10"></textarea>

	<button type="submit">저장</button>
</form>

{% endblock content  %}
```


### POST 방식 vs GET 방식
- HEADER
	- HTTP 전송에 필요한 모든 부가정보를 담고있는 부분
	- 메시지 크기, 압축, 인증, 요청 클라이언트(웹 브라우저) 정보, 서버 애플리케이션 정보, 캐시 관리 정보 등등 을 포함

- BODY
	- 실제 전송할 데이터가 담겨있는 부분
	- 데이터가 없다면 비어있게 됨

- GET은 데이터를 URL에 담아보내고, POST는 BODY에 담아 보냄
- 따라서 GET은 데이터 전송에 한계가 있으나, POST는 그렇지 않음
- POST 방식으로 데이터를 전송할 때는 CSRF Token이 필요


---
## 2. Django MTV 사용하기 (RUD)
### PRG 패턴
- POST - Redirect - GET 패턴
	1) POST요청을 서버에서 처리
	2) 서버에서는 다른 주소로 Redirect하도록 응답
	3) 브라우저는 GET방식으로 서버를 호출하여 사용자의 요청이 반영된 것을 보여줌

- PRG패턴을 사용하면 반복적인 POST호출을 막을 수 있고 사용자의 입장에서도 처리가 끝나고 처음 단계로 돌아간다는 느낌을 줌
