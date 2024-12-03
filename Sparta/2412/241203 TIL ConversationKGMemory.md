#TIL #스파르타코딩클럽 [[2412]]

## 1. ConversationKGMemory
### ConversationKGMemory
- Conversation Knowledge Graph Memory의 약자
- 대화 중에 얻은 정보를 지식 그래프 형태로 저장하고 불러오는 역할
- 대화의 맥락을 구조화된 방식으로 유지하고, 축적된 지식을 기반으로 대화를 진행해야 할 때 적합

- 주요 기능
	- 지식 그래프 생성 및 관리: 대화에서 추출한 엔티티와 그들 간의 관계를 지식 그래프로 구성하여 저장 
	- 엔티티 및 관계 추적: 대화 중 등장하는 주요 엔티티와 그들의 속성, 상호 관계를 지속적으로 추적하고 업데이트
	- 구조화된 정보 제공: 저장된 지식 그래프를 활용하여 대화의 맥락을 구조화된 방식으로 이해하고, 이를 기반으로 정확하고 일관된 정보를 제공

- 장점:
	- 복잡한 관계 파악: 대화에서 발생하는 복잡한 엔티티 간의 관계를 시각적으로 표현
	- 지식 기반 대화 유지: 대화 중 축적된 지식을 활용하여 일관성 있고 풍부한 정보를 제공 가능


### Entity
- Entity: 데이터 또는 정보를 표현하는 개별적인 개체
	- ConversationKGMemory에서는 **구체적인 정보 단위**를 말함

- ConversationKGMemory에서의 Entity 사용 방식
	1. 정보 추출 및 저장
	    - "김셜리 씨는 판교에 거주 중입니다."라는 문장에서 엔티티는 `김셜리(사람)`와 `판교(장소)`
	    - 이 정보를 지식 그래프에 저장하면 `김셜리 → 거주지 → 판교`와 같은 관계를 표현 가능
	2. 맥락 유지    
	    - 사용자가 이후 대화에서 "그는 어디에서 일하나요?"라고 질문했을 때, "그"가 `김셜리`임을 이해하고 관련 정보를 제공
	3. 질문 답변    
	    - "김셜리 씨의 직업은 무엇인가요?"라는 질문에서, 시스템은 지식 그래프를 조회해 `신입 디자이너`라는 답을 찾을 수 있음
	4. 엔티티 간 관계 표현    
	    - "김셜리 씨는 삼성전자에서 일하며 서울에 거주합니다."라는 문장은 두 개의 엔티티(`김셜리`와 `삼성전자`)와 두 개의 관계(`일함`, `거주`)를 나타냄


### 사용 예시
```python
from langchain.memory import ConversationKGMemory
from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI(temperature=0.1)

memory = ConversationKGMemory(
    llm=llm,
    return_messages=True,
)

def add_message(input, output):
    memory.save_context({"input": input}, {"output": output})
    
add_message("Hi I'm Yun, I live in South Korea", "This is so coooool!")

memory.load_memory_variables({"input": "Who is Yun?"})

---
"""
# output
{'history': [SystemMessage(content='On Yun: Yun lives in South Korea.', additional_kwargs={}, response_metadata={})]}
"""
```