#TIL #스파르타코딩클럽 [[2411]]

## 1. 한번에 여러 CSV 파일을 올려 질문에 대한 분석하기
### 코드 작성 목적
- 파일 한번에 여러 개 올리기
- 단순히 csv 파일만 올려도 분석이 가능한 지 확인
- 향후 프로젝트 진행 방향을 잡기 위한 프로토타입 제작


### 동작 순서
1. 사용할 라이브러리 및 API key 불러오기
```python
import streamlit as st
import pandas as pd
from langchain_experimental.agents import create_csv_agent
from langchain_community.llms import OpenAI
from langchain.tools import Tool
from langchain_community.chat_models import ChatOpenAI
from dotenv import load_dotenv
import os

load_dotenv()

# OpenAI API Key 설정
OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")
```

2. streamlit에서 여러 csv파일 업로드
```python
# Streamlit에서 파일 업로드
st.title("Multiple CSV Agent")
uploaded_files = st.file_uploader("CSV 파일들을 업로드하세요", type=["csv"], accept_multiple_files=True)
```

3. 모든 csv 파일을 데이터프레임으로 로드
```python
# 모든 CSV 파일을 데이터프레임으로 로드
dataframes = {}
files_path = []  # csv 파일 경로
for uploaded_file in uploaded_files:
	df = pd.read_csv(uploaded_file, on_bad_lines="skip")  # 열 크기가 맞지 않는 행은 무시
	dataframes[uploaded_file.name] = df  # 
	files_path.append(f"./files/jeju/{uploaded_file.name}")
	st.write(f"{uploaded_file.name} 데이터 미리보기:")
	st.dataframe(df)
```

4. 사용자 질문이 들어오면 Agent 생성 및 질문 실행
```python
# 사용자 질문 입력
question = st.text_input("질문을 입력하세요:")

if question:
	# LangChain Agent 생성
	tools = []
	for name, df in dataframes.items():
		# 각각의 CSV 파일을 하나의 Tool로 등록
		tool = Tool(
			name=name,
			func=lambda query, df=df: df.query(query),
			description=f"Analyze the {name} dataset."
		)
		tools.append(tool)

	# OpenAI LLM 초기화
	llm = ChatOpenAI(
		model="gpt-4o",
		temperature=0.1,
		streaming=True,
		openai_api_key = OPENAI_API_KEY,
	)

	# Agent 생성 및 질문 실행
	agent = create_csv_agent(
		llm=llm,  # 모델 지정
		path=files_path,  # csv 파일 경로들
		pandas_kwargs={"on_bad_lines":"skip"},  # 열 크기가 맞지 않는 행은 무시
		tools=tools,
		verbose=True,
		allow_dangerous_code=True,  # 보안 무시 (*로컬 컴퓨터에서만 실행!*)
	)
	response = agent.run(question)

	# 응답 출력
	st.write("AI 응답:")
	st.text(response)

```

- 참고 코드
> [CSV Agent](https://github.com/su2708/Group_10/blob/develop/model.py)

### trouble shooting
1. 질문에 대한 응답의 정확도가 낮음
	- 잘못 선택한 모델
		- ChatGPT-4o는 csv 파일과 같은 표에 대한 응답의 정확도가 낮음
	- 임베딩 하지 않은 csv 파일
		- csv 파일 역시 임베딩을 통해 벡터 스토어 확보 필요
		- csv 파일이 txt나 markdown에 비해 임베딩하기 어려움

2. 시스템 메시지의 부재
	- csv 데이터들로 부터 시장 규모를 산출하는 방법을 몰랐던 것이 원인
	- 아이디어를 낸 조원으로부터 자세한 가이드라인을 받을 예정

3. 예시 프롬프트의 부재
	- 응답의 질을 올리기 위해 few shot prompt template 필요
