#TIL #스파르타코딩클럽 [[TIL]]

## OutputParser and LCEL
### 1. OutputParser
- LLM의 출력을 받아 더 적합하고 유용한 형식으로 변환하는 중요한 컴포넌트
- 구조화된 데이터 생성에 매우 유용
- LangChain 프레임워크에서 다양한 종류의 출력 데이터를 파싱하고 처리


### 2. OutputParser의 주요 특징
- 다양성: LangChain은 많은 종류의 outputParser를 제공
- 스트리밍 지원: 많은 outputParser들이 스트리밍을 지원
- 확장성: 최소한의 모듈부터 복잡한 모듈까지 확장 가능한 인터페이스를 제공


### 3. OutputParser의 장점
- 구조화: LLM의 자유 형식 텍스트 출력을 구조화된 데이터로 변환
- 일관성: 출력 형식을 일관되게 유지하여 후속 처리가 용이
- 유연성: 다양한 출력 형식(JSON, 리스트, 딕셔너리 등)으로 변환이 가능


### 4. LCEL(LangChain Expression Language)
- LangChain에서 프롬프트 템플릿을 유연하고 동적으로 구성하기 위한 표현 언어
- 템플릿 내에서 상황에 맞는 맞춤형 프롬프트를 생성 가능하게 해줌


### 5. LCEL의 주요 특징
- 동적 프롬프트 생성: 템플릿에 변수를 삽입하거나 조작 가능
- 조건문/반복문 지원: `if-else` 구문과 반복문으로 복잡한 로직 처리 가능
- 간단한 표현식 사용: 문자열 조작, 숫자 연산 등 기본 연산을 지원하여 프롬프트 논리를 확장 가능


```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from langchain.schema import BaseOutputParser

chat = ChatOpenAI(temperature=0.1)

class CommaOutputParser(BaseOutputParser):
    # must define parse function
    def parse(self, text):
        items = text.strip().split(',')
        return list(map(str.strip, items))
        
```
```python
# without LCEL
template = ChatPromptTemplate.from_messages(
    [
        ("system", "You are a list generating machine. Everything you are asked will be answered with a comma seperated list of max {max_items} in lowercase. Do NOT reply with anything else."),
        ("human", "{question}."),
    ]
)

prompt = template.format_messages(
    max_items=10,
    question="What are the colors?"
)

result = chat.predict_messages(prompt)

p = CommaOutputParser()
p.parse(result.content)

# 출력
['red',
 'blue',
 'green',
 'yellow',
 'orange',
 'purple',
 'pink',
 'black',
 'white',
 'brown']
```
```python
# with LCEL
# make a chain of chat model, OutputParser and template.

template = ChatPromptTemplate.from_messages(
    [
        ("system", "You are a list generating machine. Everything you are asked will be answered with a comma seperated list of max {max_items} in lowercase. Do NOT reply with anything else."),
        ("human", "{question}."),
    ]
)

chain = template | chat | CommaOutputParser()

chain.invoke({
    "max_items": 5,
    "question": "What are the pokemons?"
})

# 출력
['pikachu', 'charmander', 'bulbasaur', 'squirtle', 'jigglypuff']
```



## Chaining Chains
### 1. Runnable
- LangChain에서 다양한 작업을 처리하고 연결하는 기본적인 구성 요소 
- 함수나 작업을 실행할 수 있는 객체를 의미
- LangChain은 다양한 작업(예: 프롬프트 템플릿 처리, API 호출, 데이터 변환 등)을 모듈화하여 관리하는데, 이런 작업들을 `Runnable`로 정의
- `Runnable`은 기본적으로 **입력을 받아서 특정 작업을 수행하고 결과를 반환**하는 역할


### 2. Runnable의 주요 특징
1) 함수형 인터페이스
	- `Runnable`은 입력을 받고 결과를 반환하는 함수형 인터페이스로 동작
	- 코드의 재사용성과 가독성을 높여줌
2) 연결과 조합
	- LangChain에서는 여러 `Runnable`들을 체인처럼 연결 가능
3) 유연성과 확장성
	- `Runnable`은 다양한 유형의 작업을 지원하기 때문에, 텍스트 변환, 데이터 필터링, 모델 호출, API 요청 등 다양한 작업을 수행 가능
	- 사용자 정의 `Runnable`을 만들 수도 있어, 특정 애플리케이션에 맞는 작업을 쉽게 확장 가능


### 3. The input type and output type varies by component
| Component    | Input Type                                            | Output Type           |
| ------------ | ----------------------------------------------------- | --------------------- |
| Prompt       | Dictionary                                            | PromptValue           |
| ChatModel    | Single string, list of chat messages or a PromptValue | ChatMessage           |
| LLM          | Single string, list of chat messages or a PromptValue | String                |
| OutputParser | The output of an LLM or ChatModel                     | Depends on the parser |
| Retriever    | Single string                                         | List of Documents     |
| Tool         | Single string or dictionary, depending on the tool    | Depends on the tool   |


### 4. Chaining Chains
```python
"""
세프에게 원하는 나라의 따라하기 쉬운 음식 레시피를 물어보고(chef_chain), 그 레시피를 채식 전문 세프에게 대체 레시피를 얻는 과정(veg_chain)
그 과정을 final_chain으로 묶어 한번에 처리
"""

from langchain.chat_models import ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from langchain.callbacks import StreamingStdOutCallbackHandler

# streaming: LLM의 응답이 생성되는걸 볼 수 있게 해줌
# StreamingStdOutCallbackHandler: console에서 응답의 진행을 볼 수 있음
chat = ChatOpenAI(
    temperature=0.1, streaming=True, callbacks=[StreamingStdOutCallbackHandler()]
)

chef_prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a world-class international chef. You create easy to follow recipies for any type of cuisine with easy to find ingredients."),
    ("human", "I want to cook {cuisine} food."),
])

chef_chain = chef_prompt | chat
```
```python
veg_chef_prompt = ChatPromptTemplate.from_messages([

    ("system", "You are a vegetarian chef specialized on making traditional recipies vegetarian. You find alternative ingredients and explain their preparation. You don't radically modify the recipe. If there is no alternative for a food just say you don't know how to replace it."),
    ("human", "{recipe}")
])

veg_chain = veg_chef_prompt | chat
```
```python
final_chain = {"recipe": chef_chain} | veg_chain

final_chain.invoke({
    "cuisine": "indian"
})
```
