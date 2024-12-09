#TIL #스파르타코딩클럽 [[2412]]

## 1. Gradle
### Gradle
- **빌드 자동화 도구**로, 주로 Java, Kotlin, Groovy 같은 JVM 기반 언어 프로젝트에서 사용
- 현재는 다양한 언어 프로젝트에서도 사용 가능할 정도로 범용적으로 이용

1. 빌드 도구
	- 소스 코드를 컴파일, 테스트, 패키징, 배포 등 프로젝트를 빌드하는 작업을 자동화하는 도구

2. Gradle의 주요 특징
	- DSL (Domain Specific Language)
		- Groovy 또는 Kotlin 스크립트 언어를 사용하여 빌드 스크립트를 작성
		- 빌드 스크립트: 프로젝트의 빌드 프로세스를 설정하고 정의하는 역할

	- 유연성
		- 커스텀 태스크를 정의하거나 플러그인을 사용하여 다양한 빌드 요구사항에 맞출 수 있음

	- 의존성 관리
		- Gradle은 중앙 저장소 또는 로컬 라이브러리에서 필요한 의존성을 자동으로 가져옴

	- 병렬 빌드
		- 멀티 코어 CPU를 활용하여 빌드 속도를 최적화

	- Incremetal Build
		- 변경된 부분만 빌드하여 시간을 절약


### Gradle의 주요 구성 요소
1. 빌드 스크립트 (build.gradle 또는 build.gradle.kts):
    - Gradle 프로젝트의 설정과 작업을 정의하는 스크립트 파일
    - 기본 형식
```groovy
plugins {
	id 'java'
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework:spring-core:5.3.8'
}

```


2. 의존성 (Dependencies):
    - 프로젝트가 외부 라이브러리나 다른 프로젝트 모듈에 의존할 경우 이를 관리
    - `implementation`, `testImplementation`, `compileOnly` 같은 키워드로 의존성을 정의

3. 태스크 (Tasks):
    - Gradle에서 실행할 작업 단위
    - `clean`, `build`, `test` 같은 기본 태스크 외에도 커스텀 태스크도 제작 가능
```groovy
task hello {
    doLast {
        println 'Hello, Gradle!'
    }
}
```

4. 플러그인 (Plugins):
    - 특정 언어나 프레임워크를 지원하기 위해 사용
    - 대표적인 플러그인:
        - `java`: Java 프로젝트 지원.
        - `application`: 애플리케이션 빌드 및 실행 지원.
        - `kotlin`: Kotlin 프로젝트 지원.


### Gradle의 장점
1. 속도와 성능:
    - Gradle은 병렬 처리와 캐싱 메커니즘을 통해 빌드 속도를 빠르게 함

2. 유연한 구성:
    - 필요에 따라 빌드 프로세스를 상세히 커스터마이징 가능

3. 다양한 언어와 플랫폼 지원:
    - Java뿐만 아니라 Kotlin, C++, Python 등 다양한 언어와 플랫폼을 지원

4. 플러그인 에코시스템:
    - Spring Boot, Android, Kotlin 등과 같은 다양한 플러그인을 사용 가능


### Gradle의 동작 방식
- Gradle은 태스크 그래프(Task Graph)라는 개념을 사용

1. 초기화 단계:
    - Gradle은 프로젝트 구조를 분석하고 어떤 태스크를 실행할지 결정

2. 구성 단계:
    - 빌드 스크립트를 평가하여 태스크 그래프를 생성

3. 실행 단계:
    - 태스크 그래프에 정의된 순서대로 태스크를 실행



---
## Docker 환경에서 Gradle의 역할
### Docker 환경에서 Gradle의 역할
1. 애플리케이션 빌드:
    - Gradle은 소스 코드를 컴파일하고, 의존성을 관리하며, JAR, WAR 파일 같은 애플리케이션의 실행 가능한 아티팩트(결과물)를 생성
    - Dockerfile이 이 아티팩트를 이미지에 포함시켜 배포 가능한 컨테이너를 제작

2. 테스트 자동화:
    - Gradle은 유닛 테스트, 통합 테스트를 실행할 수 있는 환경을 제공
    - Docker 이미지를 빌드하기 전에 애플리케이션이 제대로 작동하는지 검증 가능

3. 의존성 관리:
    - Gradle은 프로젝트에서 필요한 모든 라이브러리와 플러그인을 다운로드하고 관리
    - Docker 이미지를 빌드할 때 Gradle이 관리한 의존성이 포함


### Gradle과 Docker의 협력 과정
1. Gradle로 애플리케이션 빌드:
    - Gradle 스크립트를 사용하여 애플리케이션을 빌드하고 JAR/WAR 파일을 생성
    - 생성된 결과물은 `build/libs/` 디렉토리에 저장됨: `./gradlew build`

2. Dockerfile 작성:
    - Dockerfile에서 Gradle로 빌드된 결과물을 사용해 컨테이너 이미지를 생성
```dockerfile
FROM openjdk:11
COPY build/libs/my-app.jar /app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

3. Docker 이미지 빌드:
    - Docker 명령어를 실행하여 Gradle 빌드 결과를 기반으로 이미지를 생성
```bash
docker build -t my-app .
```

4. 컨테이너 실행:
    - 생성된 이미지를 사용하여 컨테이너를 실행
```bash
docker run -d -p 8080:8080 my-app
```       


### Gradle과 Docker의 통합 자동화
1. Gradle Docker 플러그인 사용:
    - `com.bmuschko.docker-remote-api` 같은 플러그인을 사용하면 Gradle로 Docker 이미지를 빌드하고 실행하는 작업까지 자동화 가능
    - Gradle로 Docker 이미지 빌드: `./gradlew buildDockerImage`
    - `build.gradle` 예시 코드
```groovy
plugins {
    id 'com.bmuschko.docker-remote-api' version '7.4.0'
}

docker {
    url = 'unix:///var/run/docker.sock'
}

task buildDockerImage(type: DockerBuildImage) {
    inputDir = file('.')
    tags = ['my-app:latest']
}
```

2. CI/CD에서 사용:
    - Jenkins, GitHub Actions 등에서 Gradle 빌드 후 Docker 이미지를 자동으로 생성하고 배포하도록 파이프라인을 구성 가능


### Gradle과 Docker 협업의 주요 이점
1. 일관된 빌드 환경:
    - Gradle이 관리하는 의존성과 빌드 결과물을 Docker 이미지로 일관되게 배포 가능
2. 자동화:
    - Gradle 스크립트로 Docker 이미지 생성 및 배포 과정을 자동화할 수 있어 CI/CD에 적합

3. 다중 언어 지원:
    - Gradle은 Java/Kotlin뿐 아니라 다양한 언어를 지원하므로, 다양한 프로젝트에서 Docker와 함께 사용 가능