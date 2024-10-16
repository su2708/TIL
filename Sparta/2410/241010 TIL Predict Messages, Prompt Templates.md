#TIL #스파르타코딩클럽 [[2410]]

## Predict Messages
### 1. ChatOpenAI
- OpanAI의 chat model(GPT-3.5 또는 GPT-4 등)을 활용하여 대화형 AI 애플리케이션을 구축할 때 사용되는 인터페이스
- LangChain 프레임워크 내에서 OpenAI의 chat model을 쉽고 유연하게 활용할 수 있도록 도와주는 중요한 컴포넌트
- 대화형 AI 애플리케이션을 빠르고 효율적으로 개발 가능

### 2. ChatOpenAI의 기능과 특징
1) 대화형 AI 인터페이스
	- `ChatOpenAI`는 OpenAI의 chat model을 사용하여 대화형 인터페이스를 제공
	- API 호출을 통해 챗봇이나 대화형 애플리케이션을 쉽게 구축 가능
2) 속성 및 설정
	- model_name: `ChatOpenAI`는 GPT-3.5 또는 GPT-4 같은 다양한 모델을 선택 가능
	- 온도(temperature): 생성되는 응답의 창의성 정도를 설정하는 파라미터
		- 0 ~ 2 까지의 범위를 가짐.
		- 결정적이고 예측 가능 (0) <-> 창의적이고 예측 하기 어려움 (2)
		- 일반적으로 0.7 정도가 균형 잡힌 값을 제공하나 때에 따라 조절 가능
	- max_tokens: 한 번의 응답에서 생성할 최대 토큰 수를 설정하여 모델의 출력 길이를 제어
3) 사용 용이성
	- 간편한 API 호출 방식을 제공하여, 사용자가 복잡한 설정 없이 쉽게 대화형 모델 사용 가능
	- 대화의 문맥을 유지하며 연속적인 메시지 처리가 가능하도록 설계되어 있어, 대화형 애플리케이션 개발에 적합
4) 추상화와 확장성
	- LangChain은 `ChatOpenAI`를 통해 대화형 모델과의 상호작용을 추상화하여, 사용자가 직접 OpenAI API를 호출하는 것보다 더 간단하고 직관적인 코드를 작성 가능
	- API 버전이나 모델 변경 시에도 코드 수정이 최소화되도록 설계됨

### 3. HumanMessage, AIMessage, SystemMessage
- LangChain에서 대화형 애플리케이션을 구축할 때 사용하는 메시지 타입들 중 일부
- 각각의 메시지가 어떤 역할을 하는지 명확하게 정의해주며, 챗봇의 대화 흐름 관리에 사용

1) HumanMessage
	- 사용자가 챗봇에게 보내는 메시지
	- 사용자의 입력, 질문, 명령 등을 표현하며, AI가 이 메시지를 바탕으로 응답을 생성
2) AIMessage
	- AI가 사용자에게 보내는 응답 메시지
	- `HumanMessage`에 대한 AI의 답변으로, 챗봇이 생성하는 텍스트가 포함됨
3) SystemMessage
	- 대화의 맥락이나 AI의 행동 방침을 설정하기 위해 사용되는 메시지
	- AI가 어떻게 응답해야 하는지 지시하거나, 대화의 초기 설정을 제공하는 데 사용

```python
from langchain.chat_models import ChatOpenAI
from langchain.schema import HumanMessage, AIMessage, SystemMessage

chat = ChatOpenAI(temperature=0.1)

# 가상의 대화 설정
messages = [
    SystemMessage(
        content="You are a geography expert. And you only reply in Korean."
    ),
    AIMessage(content="안녕! 나는 챗봇이야."),
    HumanMessage(content="What is the distance between Korea and Japan? Also, what is your name?"),
]

chat.predict_messages(messages)

# 출력
AIMessage(content='한국과 일본 사이의 거리는 약 900km입니다. 제 이름은 챗봇이에요.')
```



## Prompt Templates
### 1. 왜 prompt가 중요한가?
1) AI의 행동과 역할 설정
2) 대화의 맥락과 목적 제공
3) 출력의 창의성과 일관성 조절
4) 사용자 경험(UX) 향상
5) 문제 해결과 성능 최적화


### 2. PromptTemplate, ChatPromptTemplate
1) PromptTemplate
	- 정적 프롬프트 템플릿을 정의할 때 사용
	- 변수와 placeholder를 포함한 텍스트 기반 prompt를 생성할 수 있으며, 실행 시점에 구체적인 값을 대입하여 완성된 prompt 제작
	- 주로 단순한 질문이나 명령 생성에 유용

2) ChatPromptTemplate
	- 대화형 프롬프트 템플릿으로, `HumanMessage`, `AIMessage`, `SystemMessage` 등 여러 메시지 타입을 조합하여 대화의 구조를 설계할 때 사용
	- 대화의 각 단계마다 다른 메시지 타입을 지정하고, 이를 통해 AI와 사용자의 대화 흐름을 관리하며 여러 맥락에 맞는 응답을 유도 가능
	- 주로 복잡한 대화나 AI와의 다단계 상호 작용이 필요한 경우에 활용

```python
# PromptTemplate example
from langchain.chat_models import ChatOpenAI
from langchain.prompts import PromptTemplate, ChatPromptTemplate

chat = ChatOpenAI(temperature=0.1)

template = PromptTemplate.from_template(
    "What is the distance between {country_a} and {country_b}?",
)

prompt = template.format(country_a="Korea", country_b="Vietnam")

chat.predict(prompt)

# 출력
'The distance between Korea and Vietnam is approximately 2,500 kilometers (1,550 miles) when measured in a straight line.'
```
```python
# ChatPromptTemplate example
from langchain.chat_models import ChatOpenAI
from langchain.prompts import PromptTemplate, ChatPromptTemplate

chat = ChatOpenAI(temperature=0.1)

template = ChatPromptTemplate.from_messages(
    [
        ("system", "You are a geography expert. And you only reply in {language}."),
        ("ai", "안녕! 나는 {name}이야."),
        ("human", "What is the distance between {country_a} and {country_b}? also, what is your name?"),
    ]
)

prompt = template.format_messages(
    language="Korean",
    name="chatbot",
    country_a="Korea",
    country_b="France",
)

chat.predict_messages(prompt)

# 출력
AIMessage(content='한국과 프랑스 사이의 거리는 대략 9000km입니다. 제 이름은 AI 어시스턴트입니다. 어떻게 도와드릴까요?')
```
