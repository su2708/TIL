#TIL #스파르타코딩클럽 [[2411]]

## 1. Conversation Summary Memory
### Conversation Summary Memory
- 대화의 긴 맥락을 요약하여 유지하는 메모리 방식

### 작동 방식
1. 대화가 진행될수록 메모리가 쌓이는데, 이전 메시지들의 주요 내용을 요약(summary)하여 저장
2. 요약된 정보를 바탕으로 대화의 흐름을 이해하거나 관련성을 유지
3. 메모리 크기를 줄이고 모델의 처리 부담을 줄이는 데 유용

### 특징
- 효율성: 이전 대화 전체가 아닌 요약본만 남기기 때문에 대규모 대화에도 메모리 사용량이 적음
- 대화 맥락 유지: 주요 정보를 축약하여 계속 참조하기 때문에 맥락을 유지 가능
- 장기 대화 적합성: 대화를 길게 이어갈수록 요약을 반복 갱신하면서도 핵심 맥락을 유지

### 예시 코드
```python
from langchain.memory import ConversationSummaryMemory
from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI(temperature=0.1)

memory = ConversationSummaryMemory(llm=llm)

def add_message(input, output):
    memory.save_context({"input": input}, {"output": output})

def get_history():
    return memory.load_memory_variables({})

add_message("Hi I'm Suyoung, I live in South Korea.", "This is cool!")
add_message("South Korea is so pretty!", "I wish I could go!")

get_history()

---
# output
{
	'history': 'Suyoung from South Korea introduces themselves to the AI, who responds positively. The human comments on the beauty of South Korea, and the AI expresses a desire to visit.'
}
```

---
## 2. Conversation Summary Buffer Memory
### Conversation Summary Buffer Memory
- 요약과 버퍼를 결합하여 대화 내용을 저장하고 참조하는 방식
- 요약된 정보 뿐 아니라 최신 대화 내용을 일정 기간(버퍼 크기) 동안 함께 보존

### 작동 방식
1. 대화의 주요 내용을 요약하여 저장
2. 동시에, 최근 몇 개의 메시지를 원본 그대로(버퍼 형태) 저장하여 세부 맥락을 유지
3. 버퍼가 가득 차면 가장 오래된 메시지를 삭제하고 새로운 메시지를 추가

### 특징
- 세부 정보 보존: 요약과 함께 최근 대화의 세부 내용을 유지하므로 세부적인 응답이 가능
- 조화로운 메모리 관리: 오래된 메시지는 요약으로 대체되고, 최근 메시지는 버퍼에 남아 세부 정보를 제공
- 효율성과 맥락 유지의 균형: 요약의 장점과 버퍼의 구체성을 동시에 활용

### 예제 코드
```python
from langchain.memory import ConversationSummaryBufferMemory
from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI(temperature=0.1)

memory = ConversationSummaryBufferMemory(
    llm=llm,
    max_token_limit=50,  # 이 토큰 수 이상의 메시지는 요약해서 저장 
    return_messages=True,
    buffer_size=3  # 원본 그대로 저장하는 대화의 수
)

def add_message(input, output):
    memory.save_context({"input": input}, {"output": output})

def get_history():
    return memory.load_memory_variables({})

add_message("Hi I'm Nicolas, I live in South Korea", "Wow that is so cool!")
add_message("South Korea is so pretty", "I wish I could go!!")
add_message("How far is Korea from Argentina?", "I don't know! Super far!")
add_message("How far is Brazil from Argentina?", "I don't know! Super far!")
add_message("How far is France from Korea?", "I don't know! Super far!")
add_message("How far is USA from Korea?", "I don't know! Super far!")

get_history()

---
# output
{
	'history': [
		SystemMessage(content="Nicolas from South Korea introduces himself to the AI, who responds with enthusiasm. The conversation shifts to the beauty of South Korea, with the AI expressing a desire to visit. Nicolas asks how far Korea is from Argentina. The AI admits it doesn't know, saying it's super far. The human then asks how far Brazil is from Argentina. The AI responds that it doesn't know, it's super far. When asked how far France is from Korea, the AI is unable to provide an answer.", additional_kwargs={}, response_metadata={}),
		AIMessage(content="I don't know! Super far!", additional_kwargs={}, response_metadata={}),
		HumanMessage(content='How far is USA from Korea?', additional_kwargs={}, response_metadata={}),
		AIMessage(content="I don't know! Super far!", additional_kwargs={}, response_metadata={})
	]
}
```

---
## 3. 주요 차이점

| 특징       | Conversation Summary Memory | Conversation Summary Buffer Memory |
| -------- | --------------------------- | ---------------------------------- |
| 요약 여부    | 요약만 저장                      | 요약 + 최신 메시지 버퍼                     |
| 세부 정보 보존 | 세부 정보가 축약됨                  | 최근 대화의 세부 정보 유지                    |
| 긴 대화 적합성 | 높음                          | 중간 (버퍼 크기에 따라 다름)                  |
| 복잡성      | 낮음                          | 높음                                 |
| 주요 사용 사례 | 지속적인 대화 서비스                 | 기술 지원 및 복잡한 대화                     |
