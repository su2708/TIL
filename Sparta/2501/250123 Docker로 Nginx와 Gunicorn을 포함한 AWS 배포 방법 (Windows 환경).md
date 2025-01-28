#TIL #스파르타코딩클럽 [[2501]]

## 1. 개요
### 목표
- Docker를 사용해 Django 애플리케이션을 AWS EC2에 배포하며, Nginx와 Gunicorn을 포함한 배포 환경을 구축한다.

### 작업 환경
- Windows 에서 MobaXterm을 사용해 AWS EC2 인스턴스 관리

---
## 2. 배포 순서
### AWS EC2 인스턴스 생성 및 설정
1) AWS EC2 인스턴스 생성
	- Ubuntu 24.04 LTS 선택
	- 인스턴스 유형: t2.medium
	- `.ppk` 확장자의 새 키 페어 생성
	- 내 IP로 보안 그룹 생성
	- 보안 인바운드 규칙
		- HTTP (80): 0.0.0.0/0
		- SSH (22): 내 IP
		- TCP (3000): 0.0.0.0/0
		- TCP (8000): 0.0.0.0/0
2) SSH 연결
	- Windows에서 MobaXterm을 사용해 SSH 연결
	- `.ppk` 키 파일로 EC2 인스턴스에 접속


### Docker 및 Docker-Compose 설치
1) Docker 설치 (EC2 내부)
```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

2) Docker-Compose 설치
```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```


### 프로젝트 구조 준비
1) Windows에서 프로젝트 디렉토리 준비
```bash
project/
├── backend/           # Django 프로젝트 및 Gunicorn 설정
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── your_django_project/
│   │   ├── wsgi.py
│   │   ├── ...
│   ├── manage.py
│   ├── ...
├── nginx/             # Nginx 설정
│   ├── nginx.conf
├── docker-compose.yml # Docker Compose 파일
├── .env               # 환경 변수 파일
```


### 주요 설정 파일 작성
1) `backend/Dockerfile`
```Dockerfile
FROM python:3.12-slim

# 작업 디렉토리 설정
WORKDIR /app

# 필수 파일 복사
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 프로젝트 복사
COPY . .

# Gunicorn 실행 명령
CMD ["gunicorn", "your_django_project.wsgi:application", "--bind", "0.0.0.0:8000"]
```

2) `nginx/nginx.conf`
```nginx
server {
    listen 80;
    server_name localhost;

    location /static/ {
        alias /static/;
    }

    location / {
        proxy_pass http://backend:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

3) `docker-compose.yml`
```yaml
version: '3.9'

services:
  backend:
    build:
      context: ./backend
    container_name: django_backend
    command: gunicorn your_django_project.wsgi:application --bind 0.0.0.0:8000
    volumes:
      - ./backend:/app
      - static_volume:/static
    env_file:
      - .env
    ports:
      - "8000:8000"
    depends_on:
      - db

  nginx:
    image: nginx:latest
    container_name: nginx_server
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - static_volume:/static
    ports:
      - "80:80"
    depends_on:
      - backend

volumes:
  static_volume:
```

4) `.env`
```makefile
DJANGO_SECRET_KEY=your_secret_key
DATABASE_NAME=your_db_name
DATABASE_USER=your_db_user
DATABASE_PASSWORD=your_db_password
```


### AWS로 프로젝트 업로드
1) 로컬에서 작성한 주요 설정 파일들을 새 Repository에 업로드
2) MobaXterm에서 해당 Repository를 `git clone`


### Docker로 배포 실행
1) Docker Compose 실행
```bash
docker compose up --build -d
```

2) Docker Compose 종료
```bash
docker compose down --volumes
```

3) 서비스 상태 확인
```bash
docker ps
```

4) Nginx로 접속 테스트
	- 브라우저에서 `http://domain_or_IP`로 접속해 정상 동작 확인