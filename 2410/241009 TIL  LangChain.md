#TIL #스파르타코딩클럽 [[2410]]

## LangChain
### 1. LangChain
- 다양한 언어 모델(LLM)을 통합하고 활용할 수 있도록 해주는 오픈소스 프레임워크
- 여러 AI 언어 모델 API와의 통합을 쉽게 해주고, 체인을 구성하도록 도와줌


### 2. LangChain의 주요 기능
- 모델 연결 및 관리: 다양한 언어 모델(GPT-3, GPT-4 등)을 쉽게 연결하고 사용 가능하게 함
- 체인 구성: 여러 단계를 거쳐 실행할 수 있는 체인을 구성
	- 예: '질문 분석 -> 관련 정보 검색 -> 요약 -> 답변 생성'과 같은 일련의 작업을 한 번에 처리
- 프롬프트 관리: 프롬프트를 쉽게 생성하고, 템플릿으로 관리할 수 있게 도와줌
	- 프롬프트(prompt): 언어 모델에 입력되는 텍스트
- 도구 통합: 데이터베이스 검색, API 호출, 웹 스크래핑 등 외부 도구와 언어 모델을 결합하여 더욱 복잡한 애플리케이션을 제작하는데 도움을 줌


### 3. LangChain의 구성 요소
- LLM(Large Language Model): OpenAI와 같은 언어 모델을 사용할 수 있게 연결하는 인터페이스
- 체인(Chains): 여러 단계를 거쳐 작업을 수행하는 프로세스
- 에이전트(Agents): 체인과 도구들을 결합하여, 다양한 정보를 수집하고 처리하는 일련의 작업을 수행하는 기능
- 프롬프트 템플릿(Prompt Templates): 언어 모델에 전달할 프롬프트를 쉽게 구성하고 관리할 수 있는 방법


### 4. LangChain을 사용하는 이유
- 유연성: 다양한 언어 모델과 통합할 수 있고, 필요에 따라 다른 도구나 API와 연결할 수 있어 매우 유연하게 AI 애플리케이션을 개발할 수 있음
- 효율성: LangChain은 반복적이고 유사한 작업을 자동화하여, 일관성 있고 효율적인 응답을 제공


### 5. LangChain 사용 예시
```python
from langchain.chat_models import ChatOpenAI

chat = ChatOpenAI()

b = chat.predict('How many planets are there?')

print(b)
# 'There are eight planets in our solar system: Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, and Neptune.'
```

