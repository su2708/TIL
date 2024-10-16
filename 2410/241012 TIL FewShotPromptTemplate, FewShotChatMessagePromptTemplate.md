#TIL #스파르타코딩클럽 [[2410]]

## FewShotPromptTemplate
### 1. FewShot은 언어 모델에게 **더 나은 답변을 얻기 위해 예제를 제공**하는 방식. 
- 이를 통해 모델이 어떤 형태와 스타일로 답변해야 할지 학습하게 도와줌

### 2. 답변 예제 보여주기 >>> prompt로 원하는 것 설명하기
- Prompt로 내가 원하는 것을 설명하는 것보다 **몇 개의 답변 예제를 보여주는 방식이 더 효과적**인 경우가 많음
- 예제를 제공하면 모델이 해당 예제들을 기반으로 응답 패턴을 학습하여, 일관성 있고 정확한 답변을 생성할 확률이 높아짐

### 3. FewShotPromptTemplate을 통해 예제를 형식화
- `FewShotPromptTemplate`을 사용하면 **예제를 특정 형식으로 제공**하여, 일관성 있게 구조화된 방식으로 모델에게 전달 가능
- 이를 통해 모델이 예제에서 학습한 패턴을 더 잘 적용하도록 도와줌

```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import PromptTemplate
from langchain.prompts.few_shot import FewShotPromptTemplate
from langchain.callbacks import StreamingStdOutCallbackHandler

chat = ChatOpenAI(
    temperature=0.1,
    streaming=True,
    callbacks=[
        StreamingStdOutCallbackHandler(),
    ],
)

# AI에게 줄 답변 예제들
examples = [
    {
        "question": "What do you know about France?",
        "answer": """
        Here is what I know:
        Capital: Paris
        Language: French
        Food: Wine and Cheese
        Currency: Euro
        """,
    },
    {
        "question": "What do you know about Italy?",
        "answer": """
        I know this:
        Capital: Rome
        Language: Italian
        Food: Pizza and Pasta
        Currency: Euro
        """,
    },
    {
        "question": "What do you know about Greece?",
        "answer": """
        I know this:
        Capital: Athens
        Language: Greek
        Food: Souvlaki and Feta Cheese
        Currency: Euro
        """,
    },
]

# Before I give examples to Language Model(LM)
chat.predict("What do you know about France?")

# 출력
'France is a country located in Western Europe. It is known for its rich history, culture, and cuisine...'
```
```python
# {}안의 변수 이름은 예제와 같게 설정
example_template = """
    Human: {question}
    AI: {answer}
"""

example_prompt = PromptTemplate.from_template(example_template)

prompt = FewShotPromptTemplate(
    example_prompt=example_prompt,
    examples=examples,
    suffix="Human: What do you konw about {country}?", # 앞의 예제를 보고나서 AI가 답변해야 할 질문
    input_variables=["country"]
)

chain = prompt | chat

chain.invoke({
    "country": "Germany"
})


'''
# 출력
AI: 
        Here is what I know:
        Capital: Berlin
        Language: German
        Food: Bratwurst and Sauerkraut
        Currency: Euro
'''
```


## FewShotChatMessagePromptTemplate
### 1. FewShotChatMessagePromptTemplate VS FewShotPromptTemplate
- `FewShotPromptTemplate`은 일반 prompt 템플릿으로, 단순한 텍스트 기반 예제만 사용
- `FewShotChatMessagePromptTemplate`는 대화형 모델을 위한 템플릿이기 때문에, 다양한 메시지 타입(`HumanMessage`, `AIMessage`, `SystemMessage`)을 사용하여 예제를 구성
- 대화형 모델이 문맥을 더 잘 이해하고 유지하도록 도와줌

```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from langchain.prompts.few_shot import FewShotChatMessagePromptTemplate
from langchain.callbacks import StreamingStdOutCallbackHandler

chat = ChatOpenAI(
    temperature=0.1,
    streaming=True,
    callbacks=[
        StreamingStdOutCallbackHandler(),
    ],
)

# AI에게 줄 답변 예제들
examples = [
    {
        "country": "France",
        "answer": """
        Here is what I know:
        Capital: Paris
        Language: French
        Food: Wine and Cheese
        Currency: Euro
        """,
    },
    {
        "country": "Italy",
        "answer": """
        I know this:
        Capital: Rome
        Language: Italian
        Food: Pizza and Pasta
        Currency: Euro
        """,
    },
    {
        "country": "Greece",
        "answer": """
        I know this:
        Capital: Athens
        Language: Greek
        Food: Souvlaki and Feta Cheese
        Currency: Euro
        """,
    },
]

# 형식 지정
example_prompt = ChatPromptTemplate.from_messages([
    ("human", "What do you know about {country}?"),
    ("ai", "{answer}")
])

# 형식에 맞게 대화형으로 답변
example_prompt = FewShotChatMessagePromptTemplate(
    example_prompt=example_prompt,
    examples=examples,
)

# 초기 설정, 예제와 함께 새로운 질문
final_prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a geography expert, you give short answers."),
    example_prompt,
    ("human", "What do you know about {country}?")
])

chain = final_prompt | chat

chain.invoke({
    "country": "Thailand"
})

'''
# 출력
I know this:
Capital: Bangkok
Language: Thai
Food: Pad Thai and Tom Yum
Currency: Thai Baht
'''
