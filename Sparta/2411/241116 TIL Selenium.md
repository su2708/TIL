#TIL #스파르타코딩클럽 [[2411]]

## 1. Selenium
### Selenium
- 웹 브라우저 자동화를 위한 오픈 소스 프레임워크
- 주로 웹 애플리케이션을 테스트하거나 특정 작업을 자동화하기 위해 사용
- 크롬, 파이어폭스, 엣지 등 여러 웹 브라우저에서 작동 가능



## 2. Why Selenium?
### 다양한 브라우저 지원
- 이전에는 특정 브라우저에서만 동작하는 테스트 도구들이 많아서, 여러 브라우저에서 작동을 시험해보려면 여러 테스트 도구들을 사용했어야 했음. 
- 그러나 셀레니움은 크로스 브라우저 테스트가 가능해 개발자가 여러 브라우저 환경에서 동일한 결과를 확인할 수 있도록 해줌


### 언어 제약 해소
- 과거와 달리 셀레니움은 Python, Java, C# 등 여러 언어를 지원하여 개발자가 익숙한 언어로 프로그램을 작성할 수 있도록 함


### 웹 요소의 세밀한 제어
- 셀레니움은 HTML, CSS, JavaScript로 구성된 웹 애플리케이션의 모든 요소를 세밀하게 제어 가능
- 동적인 요소가 많거나 Ajax 기반의 웹 애플리케이션에서도 강력한 성능을 발휘


### 셀레니움을 활용한 주요 사례
-  웹 브라우저 자동화를 위한 프레임워크 답게 여러 자동화를 셀레니움을 통해 구현 가
	1. UI/UX 테스트 자동화
		- 사람이 직접 확인할 필요 없이 자동으로 버튼 클릭, 페이지 이동, 폼 작성 등을 테스트
	2. 데이터 크롤링
		- 정적 HTML을 넘어 동적 JavaScript 렌더링 데이터를 크롤링할 때 유용
	3. 반복 작업 자동화
		- 반복적인 폼 제출, 계정 생성 등의 작업을 자동으로 수행 가능



## 3. 셀레니움 사용 예시
- indeed라는 구직 사이트에서 구직 목록을 불러오는 프로그램

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from bs4 import BeautifulSoup

# Selenium 브라우저 옵션 설정
options = Options()
options.add_argument("--no-sandbox")  # 샌드박스 모드 비활성화 (리소스 충돌 방지)
options.add_argument("--disable-dev-shm-usage")  # /dev/shm 공유 메모리 사용 비활성화

# Selenium 웹드라이버 초기화
browser = webdriver.Chrome(options=options)

# Indeed 웹사이트에서 특정 키워드 검색 결과의 페이지 수를 가져오는 함수
def get_page_count(keyword):
    base_url = "https://kr.indeed.com/jobs?q="  # Indeed 검색 URL
    browser.get(f"{base_url}{keyword}")  # 브라우저에서 검색 페이지 열기

    # BeautifulSoup으로 HTML 파싱
    soup = BeautifulSoup(browser.page_source, "html.parser")
    pagination = soup.find("nav", attrs={"aria-label": "pagination"})  # 페이지네이션 영역 찾기
    pages = pagination.select("div a")  # 페이지 링크 요소 찾기
    count = len(pages)  # 페이지 수 계산

    # 최대 5페이지까지만 반환, 페이지가 없으면 1 반환
    if count >= 5:
        return 5
    elif count == 0:
        return 1
    else:
        return count

# Indeed에서 특정 키워드로 채용 공고를 추출하는 함수
def extract_indeed_jobs(keyword):
    pages = get_page_count(keyword)  # 검색 결과 페이지 수 가져오기
    print("Found", pages, "pages in indeed.com")

    results = []  # 결과를 저장할 리스트
    for page in range(pages):  # 각 페이지를 순회하며 데이터 추출
        base_url = "https://kr.indeed.com/jobs"  # 기본 URL
        final_url = f"{base_url}?q={keyword}&start={page*10}"  # 각 페이지 URL 생성
        print(f"Requesting {final_url}")
        browser.get(final_url)  # 페이지 요청

        # BeautifulSoup으로 HTML 파싱
        soup = BeautifulSoup(browser.page_source, "html.parser")
        job_list = soup.find("ul", class_="jobsearch-ResultsList")  # 채용 공고 리스트 찾기
        jobs = job_list.find_all("li", recursive=False)  # 각 채용 공고 항목 찾기

        for job in jobs:  # 각 채용 공고를 순회하며 데이터 추출
            zone = job.find("div", class_="mosaic-zone")
            if zone == None:  # 채용 공고 데이터가 있는 경우
                anchor = job.select_one("h2 a")  # 채용 공고 제목 링크
                title = anchor['aria-label']  # 채용 공고 제목
                link = anchor['href']  # 채용 공고 링크
                company = job.find("span", class_="companyName")  # 회사 이름
                location = job.find("div", class_="companyLocation")  # 회사 위치

                # 추출한 데이터를 딕셔너리로 저장
                job_data = {
                    'link': f"https://kr.indeed.com{link}",
                    'company': company.string.replace(",", " "),
                    'location': location.string.replace(",", " "),
                    'position': title.replace(",", " ")
                }
                results.append(job_data)  # 결과 리스트에 추가

    return results  # 모든 결과 반환

```