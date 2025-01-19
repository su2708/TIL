#TIL #스파르타코딩클럽 [[2501]]

## 1. 한 일

### Docker와 Docker-Compose를 활용한 Django 배포
- Docker 환경 구성:
    - `Dockerfile`을 작성하여 Django 백엔드와 MySQL 데이터베이스를 컨테이너화.
    - `docker-compose.yml`을 활용하여 서비스 간의 의존성을 정의하고 데이터베이스와의 연결 설정.
- 서비스 분리:
    - Django 프로젝트를 `backend` 서비스로, MySQL을 `db` 서비스로 분리하여 관리.
    - `.env` 파일을 사용하여 환경 변수를 안전하게 관리.
- 실행 및 디버깅:
    - `docker-compose up --build -d` 명령어로 컨테이너 실행.
    - Django 슈퍼유저 생성 및 데이터베이스 마이그레이션 수행.


---
## 2. TroubleShooting

### MySQL 컨테이너 연결 문제
- **문제**:
    - Django가 MySQL 컨테이너(`db`)에 연결하지 못하며 `OperationalError: (2002, "Can't connect to server on 'db' (115)")` 오류 발생.
- **해결**:
	- `docker-compose.yml`에서 `depends_on`을 `service_healthy` 조건으로 설정하여 MySQL이 준비된 후 Django가 실행되도록 수정.
```yaml
depends_on:
  db:
    condition: service_healthy
```
	 - `healthcheck` 대신 최신 방식 적용을 고려하여 `wait-for-it.sh` 또는 `wait4x`와 같은 도구 사용을 검토.

### 포트 충돌 문제
- **문제**:
    - `docker-compose` 실행 시 MySQL 포트 `3306`이 이미 사용 중이라는 오류 발생.
- **해결**:
    - `docker-compose.yml`에서 MySQL 포트를 `3307:3306`으로 변경하여 해결.
```yaml
ports:
	- "3307:3306"
```
	
	- Django의 `settings.py`에서 포트 설정 수정
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': os.getenv('MYSQL_DATABASE', 'mydb'),
        'USER': os.getenv('MYSQL_USER', 'user'),
        'PASSWORD': os.getenv('MYSQL_PASSWORD', 'password'),
        'HOST': 'db',
        'PORT': '3307',
    }
}
```



---
## 3. 느낀 점

### Docker와 Docker-Compose의 강력함
- 컨테이너를 활용하면 손쉽게 개발 환경을 배포할 수 있으며, 환경 간 일관성을 유지할 수 있음.
- `docker-compose`를 사용하여 여러 서비스(예: 백엔드, 데이터베이스)를 효율적으로 관리할 수 있음.

### 서비스 의존성 관리의 중요성
- 서비스 간의 실행 순서와 준비 상태를 신중하게 관리해야 하며, 단순 `depends_on` 대신 보다 견고한 방식(예: `wait-for-it`)을 적용해야 함.

### 문제 해결 능력의 향상
- Docker 로그와 Django의 디버깅 도구를 적극적으로 활용하여 문제를 분석하고 해결할 수 있었음.
- 에러 메시지를 통해 문제의 원인을 정확히 파악하고, 해결 방안을 적용하면서 경험이 축적됨.