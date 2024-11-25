#TIL #스파르타코딩클럽 [[2411]]

## 1. 제주 데이터 허브에서 카드 이용 데이터 받기
### 1. 크롤링 목적
1. 제주도의 소비 현황을 관찰 및 시장 분석
2. 추후 전국 단위의 소비 현황 분석을 위한 연습 데이터 확보

### 2. 데이터 설명
1. 성별 카드 이용금액 비율.csv
	- 2018년 1분기 ~ 2024년 3분기
	- 연도 / 분기 / 업종 / 성별 / 연령대 / 사용량

2. 시간대별 카드 이용금액 현황.csv
	- 2018 1월 ~ 2024 8월
	- 연도_월 / 시간 대 / 이용자 타입 / 연령대 / 사용량

3. 요일별 카드 이용 건수 비교.csv
	- 2018 1월 ~ 2024 8월
	- 연도_월 / 요일 / 연령대 /성별 / 사용 횟수
  

### 3. 동작 순서
1. URL 및 다운로드 경로 설정
```python
from selenium import webdriver  # 웹드라이버 기본 모듈
from selenium.webdriver.chrome.service import Service  # 크롬 서비스 관리 모듈
from selenium.webdriver.chrome.options import Options  # 크롬 브라우저 옵션 설정 모듈
from webdriver_manager.chrome import ChromeDriverManager  # 크롬드라이버 자동 관리 모듈
from selenium.webdriver.common.by import By  # 요소 찾기 방법 지정 모듈
from selenium.webdriver.common.keys import Keys  # 키보드 입력 모듈
import time
import os 

# URL 및 다운로드 경로 설정
URL = "https://www.jejudatahub.net"  # 제주데이터허브 url
DOWNLOAD_PATH = os.path.abspath("./files")  # 다운로드 경로
os.makedirs(DOWNLOAD_PATH, exist_ok=True)  # 파일 저장 디렉토리 생성
```

2. 크롬 드라이버 설정
```python
# 드라이버 설정
service = Service(ChromeDriverManager().install())  # 크롬 드라이버 설정
chrome_options = Options()
chrome_options.add_argument('--start-maximized')  # 브라우저를 최대화해서 시작
chrome_options.add_experimental_option("prefs", {
    "download.default_directory": rf"{DOWNLOAD_PATH}",  # 다운로드 경로 설정
    "download.prompt_for_download": False,  # 다운로드 팝업 비활성화
    "safebrowsing.enabled": True,  # 안전 다운로드 허용
    "profile.default_content_setting_values.automatic_downloads": 1,  # 여러 파일 다운로드 허용
})
```

3. 제주 데이터 허브에 접속하여 '카드 이용' 검색
```python
# 1. 제주데이터허브 접속
driver.get(f"{URL}")
time.sleep(2)  # 페이지 로딩 대기

# 2. '카드 이용' 검색어 입력
search_box = driver.find_element(By.CSS_SELECTOR, "input[placeholder='키워드를 입력하세요.']")  # 검색 창 찾기

search_box.send_keys("카드 이용")  # 검색어 입력
time.sleep(1)  # 잠시 대기

search_box.send_keys(Keys.RETURN)  # Enter 키로 검색 실행
time.sleep(1)  # 검색 결과 로딩 대기
```

4. 검색 결과에서 로그인 없이 받을 수 있는 데이터 다운로드
```python
# 3. 검색 결과에서 1, 2, 3번 항목의 자료 다운로드
data_list = [1, 2, 3]
for elem in data_list:
	# 3-1. 항목 링크 찾기
	hyperlink = driver.find_element(By.XPATH, f'//td[@class="cell-number" and text()="{elem}"]/following-sibling::td[@class="cell-subject"]/a[@class="hyperlink"]')  # 항목 링크 위치
	driver.execute_script("arguments[0].click();", hyperlink)  # 항목 링크 클릭
	time.sleep(1)
	
	# 3-2. 첨부 파일 찾기
	file = driver.find_element(By.XPATH, '//span[@class="pointer-span"]')  # 파일 위치
	driver.execute_script("arguments[0].click();", file)  # 파일 다운로드
	time.sleep(3)
	print(f"파일 다운로드: {file.text}")
	
	# 3-3. 목록으로 나가기
	list = driver.find_element(By.XPATH, '//div[@class="contents-footer"]/a[@class="btn btn-outline fl"]') # 목록 아이콘 위치
	driver.execute_script("arguments[0].click();", list)  # 목록 아이콘 클릭
	time.sleep(1)
```

- 전체 코드 참조
> [crawling_jeju.ipynb](https://github.com/su2708/Group_10/blob/feature/crawling/ysy/crawling_jeju.ipynb)