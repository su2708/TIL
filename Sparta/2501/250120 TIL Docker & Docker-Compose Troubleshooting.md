#TIL #스파르타코딩클럽 [[2501]]

## 1. Docker & Docker-Compose Troubleshooting
### 1. `docker-compose` vs `docker compose`
- `docker-compose`는 기존의 Python 패키지 기반 명령어이며, 현재는 `docker compose`가 기본으로 권장됨.
- `docker compose`는 Docker CLI에 기본적으로 포함되어 있어 별도 설치가 필요하지 않음.


---
### 2. 컨테이너 health check 문제
**문제**:
- Django 백엔드 컨테이너(`django_backend`)가 `unhealthy` 상태로 표시됨.
- 원인 파악을 위해 `docker compose logs` 및 `docker compose ps -a`를 통해 상태 점검.
- Django 컨테이너 내에 `curl`이 없는 문제 발견

**해결책:**
1. `Dockerfile`에 `RUN apt-get install -y curl` 추가
2. backend의 health check 명령을 `test: ["CMD-SHELL", "python manage.py check || exit 1"]`로 설정.


---
### 3. Docker compose 명령어 및 옵션 학습
- `docker compose stop` → 실행 중인 컨테이너 중지 (볼륨 및 네트워크 유지)
- `docker compose start` → 중지된 컨테이너 다시 시작
- `docker compose down --volumes --remove-orphans` → 컨테이너, 볼륨 및 불필요한 컨테이너 정리 후 재시작
- `docker compose up -d --build` → 빌드를 포함하여 모든 서비스를 백그라운드에서 시작

**상황별 사용법 정리:**
- 환경변수 적용 후 → `docker compose down --volumes` 후 재시작.
- 빠른 서비스 재시작 → `docker compose restart <서비스명>`.
- 서비스의 상태 확인 → `docker compose ps -a` 및 `docker compose logs`.

