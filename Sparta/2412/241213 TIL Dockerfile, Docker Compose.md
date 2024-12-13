#TIL #스파르타코딩클럽 [[2412]]

## 1. Dockerfile
### Dockerfile
- Docker 이미지를 생성하기 위한 스크립트
- 사용자가 정의한 명령어를 통해 애플리케이션 환경을 설정하고 필요한 소프트웨어를 설치


### 주요 개념
1. **Dockerfile**: 이미지를 생성하기 위한 구성 파일
2. **이미지**: 컨테이너를 실행하기 위한 템플릿
3. **컨테이너**: 이미지로부터 생성된 실행 가능한 인스턴스


### Dockerfile의 기본 구조
```dockerfile
# 1. 베이스 이미지 선택
FROM python:3.8-slim

# 2. 작성자 정보 (선택)
LABEL maintainer="your_email@example.com"

# 3. 작업 디렉토리 설정
WORKDIR /app

# 4. 필요한 파일 복사
COPY requirements.txt .

# 5. 의존성 설치
RUN pip install --no-cache-dir -r requirements.txt

# 6. 애플리케이션 소스 복사
COPY . .

# 7. 컨테이너 실행 명령 설정
CMD ["python", "app.py"]
```


### 주요 명령어
- **FROM**: 기본으로 사용할 이미지를 지정 (필수)
- **LABEL**: 이미지에 메타데이터 추가
- **WORKDIR**: 작업 디렉토리를 설정
- **COPY/ADD**: 파일을 컨테이너에 복사
- **RUN**: 명령어를 실행 (ex. 설치 작업)
- **CMD**: 컨테이너 시작 시 기본으로 실행할 명령어
- **EXPOSE**: 컨테이너에서 사용할 포트를 지정



---
## 2. Docker Compose
### Docker Compose
- 여러 컨테이너를 정의하고 함께 실행할 수 있도록 도와주는 도구
- 특히, 서비스 간의 네트워크 설정, 볼륨 연결 등을 효율적으로 설정


### 주요 개념
1. **서비스**: 각 컨테이너를 의미
2. **네트워크**: 서비스 간 통신을 위한 가상 네트워크
3. **볼륨**: 컨테이너와 호스트 간의 데이터 공유


### Docker Compose 파일 구조 (docker-compose.yml)
```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "5000:5000"
    volumes:
      - .:/app
    environment:
      - FLASK_ENV=development
  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: example_db
    ports:
      - "5432:5432"
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:

```


### 구성 요소
- **version**: Compose 파일의 버전
- **services**: 실행할 컨테이너를 정의
    - **build**: 이미지를 생성하기 위한 경로 및 Dockerfile 정보
    - **image**: 사용할 Docker 이미지를 지정
    - **ports**: 호스트와 컨테이너 간 포트 매핑
    - **volumes**: 컨테이너와 호스트 간 데이터 공유
    - **environment**: 환경 변수를 설정
- **volumes**: 데이터가 유지되는 볼륨을 정의


### Dockerfile과 Docker Compose의 차이

| 특징     | **Dockerfile**               | **Docker Compose**                         |
| ------ | ---------------------------- | ------------------------------------------ |
| 역할     | 이미지 빌드를 위한 설정 파일             | 여러 컨테이너의 실행과 관리                            |
| 파일 형식  | Dockerfile                   | YAML (`docker-compose.yml`)                |
| 사용 목적  | 컨테이너 이미지 정의                  | 컨테이너 간의 관계와 환경 구성                          |
| 주요 명령어 | `docker build`, `docker run` | `docker-compose up`, `docker-compose down` |


---
## 3. 실행 과정
### Dockerfile
- 이미지를 빌드
	```bash
	docker build -t myapp .
	```

- 컨테이너 실행
	```bash
	docker run -p 5000:5000 myapp
	```


### Docker Compose
- 컨테이너 실행 및 네트워크 생성
	```bash
	docker-compose up
	```

- 백그라운드 실행
	```bash
	docker-compose up -d
	```

- 컨테이너 정리
	```bash
	docker-compose up -d
	```


---
## 4. 활용 예시

### 단독 Dockerfile
- 간단한 단일 애플리케이션 배포


### Docker Compose
- 복잡한 애플리케이션 (ex. 웹 서버 + 데이터베이스)
- 각 서비스의 독립 실행 및 테스트