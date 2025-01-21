#TIL #스파르타코딩클럽 [[2501]]

## 1. Docker & Docker-Compose Troubleshooting

### 1. Access denied for user 'root'@'localhost' (using password: NO)
#### **문제**:
- root에 로그인이 되지 않던 문제

**해결**:
- db의 healthcheck에 아래의 문구로 수정
```yml
db:
image: mariadb:10.5 # MariaDB 공식 이미지
container_name: mariadb
ports: # 포트 매핑 (호스트:컨테이너)
  - "3307:3306"
healthcheck:
  test:
	[
	  "CMD",
	  "mysqladmin",
	  "ping",
	  "-h",
	  "localhost",
	  "-u",
	  "root",
	  "-p$MYSQL_ROOT_PASSWORD",
	]
```


---
### 2. migrations 자동화 문제
**문제:**
- Django 컨테이너가 반복적으로 재시작되며 `health check` 실패 (`curl` 명령어 누락).
- 데이터베이스 마이그레이션 및 정적 파일 수집 작업 자동화를 위한 실행 실패.

**해결책:**
1. 자동 마이그레이션을 실행하기 위해 아래의 command를 docker-compose.yml에 추가
```yml
backend: # Django Backend
    build: # Dockerfile 경로
      context: ./backend
      dockerfile: Dockerfile
    container_name: django_backend
    command: >
      sh -c "python manage.py makemigrations && python manage.py migrate &&
        python manage.py collectstatic --noinput &&
        python manage.py runserver 0.0.0.0:8000"
```


---
### 3. Django의 정적 파일 관련 오류
**문제:**
- Django의 `collectstatic` 실행 시 `STATIC_ROOT` 설정 누락으로 `ImproperlyConfigured` 오류 발생.
**해결책:**
1. `backend/settings.py`에 다음 설정 추가:
```python
import os
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]
```

2. Docker Compose의 볼륨 추가:
```yml
volumes:
	- ./backend/staticfiles:/app/staticfiles
```    

