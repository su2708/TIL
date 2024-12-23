#TIL #스파르타코딩클럽 [[2412]]

## 1. Django Static & Media
### Static Files
- 정적 파일(멈춰있는 파일)을 의미
- 서비스 로직에서 별도의 처리 없이 보여주기만 하면 되는 파일을 의미
	- 로고, 광고 이미지 등 서비스 이미지 파일
	- javascript file, CSS file 등
- 무조건 주기만 하면 되는 파일들이기 때문에 모아서 따로 제공 가능
- 각 앱의 `static` 디렉토리: **`<app_name>/static/<app_name>`**

- **`STATIC_URL`**: static 파일을 참조할 때 사용할 url
	- 개발 단계에서는 `app/static` 경로 및 settings의 `STATICFILES_DIRS`에 정의된 경로 참조
	- 실제 디렉토리 경로가 아님에 주의 (URL로만 존재하는 경로)

- **`STATIC_ROOT`**: 배포를 위해 정적 파일을 수집하는 디렉토리의 절대경로
	- Django 프로젝트에서 사용하는 모든 정적 파일을 이곳으로 모아서 적용
		- 단, `DEBUG=True`인 경우엔 동작 X (개발 단계라고 인식)
	- 추후 배포시 모든 정적파일을 다른 웹 서버가 직접 제공하기 위함


### Media Files
- 유저가 웹에서 업로드한 모든 파일 (static file을 제외 모든 파일)

- **`MEDIA_URL`**: 미디어를 처리를 위한 URL

- **`MEDIA_ROOT`**
	- 업로드한 파일이 저장되는 디렉토리 경로를 지정
	- 업로드 파일은 데이터베이스에 저장되지 않으며 실제 저장되는 것을 파일의 경로


### runserver
- `runserver` : ‘개발용’ 서버를 실행하는 것
- 순수 python으로 작성되었으며 django에 포함되어있어 개발을 편하게 도와주는 역할
- **절대로 개발 서버를 운영 환경에서 사용해서는 안됨**
    - 만약 `python [manage.py](<http://manage.py>) runserver`를 이용해서 배포한다면 문제가 발생
- Django는 Web Framework이며 실제 운영시에는 웹 서버를 앞쪽에 달아줘야함
