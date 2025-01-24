#TIL #스파르타코딩클럽 [[2501]]

## 1. MobaXterm을 사용하여 Docker 컨테이너 내부의 MariaDB(MySQL) 확인 방법
- MobaXterm을 사용하여 AWS EC2에서 실행 중인 **Docker 컨테이너 내부의 MariaDB(MySQL) 데이터베이스를 확인하는 방법**을 정리합니다.

---

### 1. MobaXterm을 통한 EC2 인스턴스 접속
1. MobaXterm을 실행합니다.
2. 상단 메뉴에서 "Session" → "New Session"을 클릭합니다.
3. "SSH"를 선택하고 다음 정보를 입력합니다:
    - Remote host: `<EC2-퍼블릭-IP>` (예: `3.39.181.117`)
    - Port: `22` (기본 SSH 포트)
    - Username: `ubuntu` (Amazon Linux는 `ec2-user`)
    - Use private key: EC2 연결 시 사용한 `.pem`나 `.ppk` 키파일 선택 (예: `your-key.pem`)
4. "OK"를 클릭하여 EC2에 연결합니다.

---

### 2. 실행 중인 Docker 컨테이너 확인

EC2에 접속한 후, 현재 실행 중인 Docker 컨테이너를 확인합니다.
```bash
docker ps
```

출력 예시:
```bash
CONTAINER ID   IMAGE          COMMAND                  CREATED        STATUS        PORTS                     NAMES
a1b2c3d4e5f6   mariadb:10.6    "docker-entrypoint.s…"   5 hours ago    Up 5 hours    0.0.0.0:3306->3306/tcp      mariadb
```

컨테이너가 실행 중이라면 컨테이너 이름(예: `mariadb`) 또는 ID를 확인합니다.

---

### 3. Docker 컨테이너 내부로 접근하여 MySQL CLI 실행

Docker 컨테이너 내부에서 MySQL을 실행하려면 다음 명령어를 사용합니다.
```bash
docker exec -it mariadb mysql -u root -p
```

- `mariadb`: 컨테이너 이름 또는 컨테이너 ID
- `-u root`: MySQL의 루트 사용자
- `-p`: 비밀번호 입력을 위한 옵션

비밀번호를 입력하면 MySQL CLI가 실행됩니다.

---

### 4. 데이터베이스 확인 및 작업 수행

MariaDB에 로그인한 후 다음 명령을 실행하여 데이터베이스를 확인합니다.

#### 1) 데이터베이스 목록 조회
```sql
SHOW DATABASES;
```

출력 예시:
```diff
+--------------------+
| Database           |
+--------------------+
| information_schema |
| userdatabase       |
+--------------------+
```

#### 2) 특정 데이터베이스 사용
```sql
USE userdatabase:
```

#### 3) 테이블 목록 조회
```sql
SHOW TABLES;
```

출력 예시:
```diff
+-------------------+
| Tables_in_userdatabase |
+-------------------+
| users             |
| orders            |
+-------------------+
```

#### 4) 테이블 데이터 조회
```sql
SELECT * FROM users LIMIT 5;
```

---

### 5. Docker 컨테이너에서 직접 MySQL 쿼리 실행

컨테이너 내부로 들어가지 않고 직접 호스트에서 명령을 실행할 수도 있습니다.
```bash
docker exec -it mariadb mysql -u myuser -p mypassword -e "SHOW DATABASES;"
```

출력 예시:
```
Database
information_schema
userdatabase
```

---

### 6. Docker 컨테이너 로그 확인

만약 데이터베이스 연결에 문제가 있을 경우, 컨테이너 로그를 확인하여 문제를 진단할 수 있습니다.
```bash
docker logs mariadb --tail 50
```

---

### 7. 데이터베이스 백업 및 복원

#### 1) 데이터베이스 백업
로컬 서버에서 다음 명령을 실행하여 MySQL 데이터를 백업합니다.
```bash
docker exec mariadb mysqldump -u root -p userdatabase > backup.sql
```

#### 2) 데이터베이스 복원
백업한 SQL 파일을 데이터베이스에 복원하려면:
```bash
docker exec -i mariadb mysql -u root -p userdatabase < backup.sql
```

---

### 8. 컨테이너 네트워크 확인 및 연결 문제 해결

컨테이너 간 네트워크 연결을 확인하려면 다음 명령을 사용합니다.
```bash
docker network ls
```

특정 네트워크에 컨테이너가 연결되어 있는지 확인하려면:
```bash
docker inspect mariadb
```

---

### 9. MariaDB 설정 확인 및 수정

만약 MariaDB의 설정을 변경하려면 컨테이너 내부에서 MariaDB 설정 파일(`my.cnf`)을 확인해야 합니다.
```bash
docker exec -it mariadb sh
cat /etc/mysql/my.cnf
```

예를 들어, 원격 접속을 허용하려면 다음 설정을 확인합니다.
```ini
[mysqld]
bind-address = 0.0.0.0
```

설정을 변경한 후 컨테이너를 재시작합니다.
```bash
docker restart mariadb
```
