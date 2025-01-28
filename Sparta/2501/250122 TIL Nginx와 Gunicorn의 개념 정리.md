#TIL #스파르타코딩클럽 [[2501]]

## 1. Nginx와 Gunicorn의 개념 정리
### Nginx
- 정의
	- Nginx는 **고성능 웹 서버이자 리버스 프록시 서버**로, HTTP 및 다른 프로토콜을 처리하는 데 사용됨
	- 정적 파일 서빙과 로드 밸런싱, 리버스 프록싱에 강점을 가짐

- 주요 특징
	- 리버스 프록시
		- 클라이언트의 요청을 받아 내부 서버(Gunicorn 등)로 전달하고, 서버의 응답을 클라이언트에게 반환하는 역할을 수행함
	- 로드 밸런싱
		- 여러 어플리케이선 서버(Gunicorn 등) 간 트래픽을 분산해 부하를 줄여줌
	- 정적 파일 서빙
		- HTML, CSS, 이미지와 같은 정적 파일을 빠르게 클라이언트에게 제공함
	- 고성능
		- 이벤트 기반 아키텍처로 높은 동시성을 지원하여 많은 요청을 효율적으로 처리함

- Nginx의 역할
	- 클라이언트 → Nginx → Gunicorn → Django (어플리케이션)
	- 외부 요청을 필터링하고 Gunicorn으로 전달하여 Django 어플리케이션을 실행함


### Gunicorn
- 정의
	- Gunicorn(Green Unicorn)은 Python 어플리케이션을 WSGI(Web Server Gateway Interface) 표준에 따라 실행하는 **WSGI 서버**

- 주요 특징
	- 멀티 프로세스 구조
		- 각 요청을 처리하기 위해 여러 워커 프로세스를 생성하여 동시성을 지원함
	- Python 친화적
		- Django, Flask 등 대부분의 Python 웹 프레임워크와 호환됨
	- 간편한 설정
		- 비교적 설정이 단순하며 빠르게 배포 가능

- Gunicorn의 역할
	- 클라이언트의 요청을 처리하지 않으며(직접 외부와 연결되지 않음), WSGI를 통해 Django와 같은 어플리케이션의 요청을 처리하고 응답을 생성
	- Nginx와 함께 사용하여 요청 처리의 안정성과 효율성을 높임


### Nginx와 Gunicorn의 관계
- Nginx
	- 클라이언트로부터 요청을 받고 리버스 프록시로 Gunicorn에 전달
	- 정적 파일은 자체적으로 처리해 Gunicorn의 부하를 줄임

- Gunicorn
	- Nginx로부터 받은 요청을 Django 어플리케이션에 전달하고 처리 결과를 반환
	- 어플리케이션의 동적 콘텐츠를 생성하는 역할을 수행


### 흐름
1. 클라이언트 → Nginx
    - 요청을 수신하고 리버스 프록시 역할을 수행.
2. Nginx → Gunicorn
    - 동적 요청은 Gunicorn으로 전달.
3. Gunicorn → Django
    - WSGI 서버로서 Django 애플리케이션의 요청을 처리.
4. Django → Gunicorn → Nginx
    - 처리된 결과를 Gunicorn이 받아 Nginx에 전달.
5. Nginx → 클라이언트
    - 최종 응답을 클라이언트에게 반환.