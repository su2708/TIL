#TIL #스파르타코딩클럽 [[2411]]

## 1. Magic
### st.write()
- Streamlit의 다목적 함수로, **다양한 형식의 데이터를 화면에 출력하는 기능**을 제공
- 다양한 형식을 자동으로 인식하고 렌더링하기 때문에, `st.write()` 하나로 간편하게 구현 가능
```python
import streamlit as st
import pandas as pd

# 텍스트 출력
st.write("Hello, Streamlit!")

# 데이터프레임 출력
df = pd.DataFrame({
    'Column A': [1, 2, 3],
    'Column B': [4, 5, 6]
})
st.write(df)

# 그래프 출력
import matplotlib.pyplot as plt

fig, ax = plt.subplots()
ax.plot([1, 2, 3], [10, 20, 30])
st.write(fig)

# HTML 코드 렌더링
st.write("<h1>Streamlit</h1>", unsafe_allow_html=True)
```


### magic
- 별도의 `st.write()` 호출 없이도 파이썬 코드에 나타나는 데이터를 화면에 자동으로 출력하는 기능
- 간단하고 빠르게 시각화를 진행할 수 있다는 장점
```python
# Magic 기능 예시
"Hello, Streamlit!"  # 텍스트가 자동으로 화면에 출력됨

df  # 데이터프레임이 자동으로 화면에 출력됨
```

---
## 2. Data Flow
### **데이터가 변할 때마다 전체 코드를 다시 실행**하는 이유
1. 일관성 유지
	- 특정 부분만 따로 업데이트 하는 경우에 발생할 수 있는 복잡한 상태 관리 문제를 피할 수 있음
2. 간단한 개발자 경험
	- 데이터 변경에 따른 복잡한 상태를 고려하지 않고, 새롭게 상태가 반영된 코드를 보여주기 때문에 개발자 입장에서는 단순하고 예측 가능한 구조로 코드를 작성 가능


---
## 3. Multi Page
### with 구문
- python의 context manager
- 여러 Streamlit 요소를 그룹화하거나 특정 레이아웃을 손쉽게 정의할 수 있도록 해줌
	- `st.container`: 요소를 그룹화해 특정 영역에 묶을 때 사용
	- `st.expander`: 클릭하여 펼쳐볼 수 있는 내용을 만들 때 사용
	- `st.sidebar`: 사이드바 영역에 요소들을 추가할 때 사용
	- `st.form`: 입력 요소들을 폼으로 묶어 한 번에 제출할 수 있게 할 때 사용
```python
# ex) st.sidebar
with st.sidebar:
    st.write("Sidebar content")
    option = st.selectbox("Choose an option", ["Option 1", "Option 2", "Option 3"])
    st.write("You selected:", option)
```


### multi-page
- Streamlit으로 여러 페이지를 만들 때, 각 페이지를 `pages` 폴더에 저장하는 것이 기본 설정
- 이 구조대로 페이지들을 저장하면, Streamlit이 자동으로 사이드바에 페이지 별 메뉴 생성
```bash
my_app/
├── main_app.py       # 메인 앱 파일
└── pages/
    ├── page1.py      # 첫 번째 페이지
    ├── page2.py      # 두 번째 페이지
    └── page3.py      # 세 번째 페이지
```

---
## 4. Chat Messages
### Data Flow로 인한 문제
- 한 세션에서 데이터가 변할 때마다 전체 코드를 다시 실행하기 때문에 이전 데이터를 저장할 수 없음
- Chatbot 서비스를 위해서는 이전 대화 기록을 따로 저장하고 보여줘야 함
- 전체 코드가 재실행되더라도 이전 데이터를 보여줄 수 있는 저장소의 필요성이 발생


### session_state
- 한 세션이 진행되는 동안의 기록을 저장하고, 전체 세션이 종료되거나 재시작 하기 전까지는 그 기록을 잃지 않음
- Streamlit의 데이터 흐름 구조에서 세션 내 데이터의 지속성을 유지하는데 중요한 역할
```python
if "message" not in st.session_state:
    st.session_state["message"] = []  # initialize a session_state["message"] list

def send_message(message, role, save=True):
    with st.chat_message(role):
        st.write(message)
    if save:
	    # {"message": message, "role": role} 형태로 session_state["message"]에 저장
        st.session_state["message"].append({"message": message, "role": role})
        
for message in st.session_state["message"]:
    send_message(message["message"], message["role"], save=False)
    
message = st.chat_input("Send a message to the ai")
```