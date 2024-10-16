#TIL #스파르타코딩클럽 [[2410]]

## LengthBasedExampleSelector
### 1. LengthBasedExampleSelector
- 모든 사례들을 다 예제로 선택할 수는 없기 때문에 example selector가 필요
- LengthBasedExampleSelector는 기본적으로 예제를 형식화하고, 예제의 길이에 기반해서 적절한 예제를 선택하는 역할을 수행. 이를 통해 예제의 양을 조절하고 관리함
- 예제 선택 시 길이에 따라 선택된 예제들이 적절한지, 요구하는 조건을 충족하는지를 확인할 수 있는 과정을 거침. 이를 통해 예제가 사용자에게 최적화된 형태로 제공될 수 있도록 보장함
```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts.few_shot import FewShotPromptTemplate
from langchain.callbacks import StreamingStdOutCallbackHandler
from langchain.prompts import PromptTemplate
from langchain.prompts.example_selector import LengthBasedExampleSelector
from langchain.prompts.example_selector.base import BaseExampleSelector

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

example_prompt = PromptTemplate.from_template("Human: {question}\nAI:{answer}")

example_selector = LengthBasedExampleSelector(
    examples=examples,
    example_prompt=example_prompt,
    max_length=180  # 허용하는 예제의 양
)

prompt = FewShotPromptTemplate(
    example_prompt=example_prompt,
    example_selector=example_selector,
    suffix="Human: What do you konw about {country}?",
    input_variables=["country"]
)

prompt.format(country="Brazil")
```


### 2. 자신만의 example selector 만들기
1) `BaseExampleSelector`
	- 다양한 형태의 예제 선택기를 만들기 위한 기본 클래스
2) `add_example`
	- 예제를 동적으로 추가하거나 수정하기 위한 메서드
	- 필수는 아니지만 대부분의 경우 사용
3) `select_examples`
	- 예제를 선택하는 로직이 정의된 필수 메서드
```python
# 자신만의 example selector 만들기
class RandomExampleSelector(BaseExampleSelector):
    def __init__(self, examples):
        self.examples = examples
    
    # 필수는 아니지만 동적인 예제 선택을 위해 사용하는 메서드
    def add_example(self, example):
        self.examples.append(example)
        
    # 예제를 선택하는 로직이 적힌 필수 메서드
    def select_examples(self, input_variables):
        from random import choice
        return [choice(self.examples)]
        
example_prompt = PromptTemplate.from_template("Human: {question}\nAI:{answer}")

example_selector = RandomExampleSelector(
    examples=examples,
)

prompt = FewShotPromptTemplate(
    example_prompt=example_prompt,
    example_selector=example_selector,
    suffix="Human: What do you konw about {country}?",
    input_variables=["country"]
)

prompt.format(country="Brazil")
```



## Serialization and Composition
### 1. prompt를 별도의 파일로 저장 가능 (.json or .yaml)
- 프롬프트 파일을 사용하는 장점
	- 유지보수와 관리
	- 재사용성
	- 가독성

- 사용 방법
```json
// prompt.json
{
    "_type": "prompt",
    "template": "What is the capital of {country}",
    "input_variables": [
        "country"
    ]
}
```
```yaml
// prompt.yaml
_type: "prompt"
template: "What is the capital of {country}"
input_variables: ["country"]
```
```python
from langchain.prompts import load_prompt

#prompt = load_prompt("./prompt.json")
prompt = load_prompt("./prompt.yaml")
```


### 2. PipelinePromptTemplate
1) `PipelinePromptTemplate`는 여러 prompt들을 단계별로 연결하여, 하나의 흐름을 만들고 결과를 다음 단계로 전달하는 역할
2) prompt와 variables을 tuple이나 dictionary 형태로 정의하고, 이를 list로 `pipeline_prompts` 인자에 전달
3) 각 prompt는 템플릿 형식으로 작성되며, 필요한 variable은 `{variable_name}` 형식으로 prompt 내부에 정의
```python
from langchain.chat_models import ChatOpenAI
from langchain.callbacks import StreamingStdOutCallbackHandler
from langchain.prompts.pipeline import PipelinePromptTemplate

chat = ChatOpenAI(
    temperature=0.1,
    streaming=True,
    callbacks=[
        StreamingStdOutCallbackHandler(),
    ],
)

intro = PromptTemplate.from_template(
    """
    You are a role playing assistant.
    And you are impersonating a {character}.
    """
)

example = PromptTemplate.from_template(
    """
    This is an example of how you talk:
    Human: {example_question}
    You: {example_answer}
    """
)

start = PromptTemplate.from_template(
    """
    Start now!
    Human: {question}
    You:
    """
)

final = PromptTemplate.from_template(
    """
    {intro}
    {example}
    {start}
    """
)

# 많은 prompt들을 하나로 합치는 방법
prompts = [
    ("intro", intro),
    ("example", example),
    ("start", start),
]

full_prompt = PipelinePromptTemplate(
    final_prompt=final,
    pipeline_prompts=prompts
)

full_prompt.format(
    character="Pirate",
    example_question="What is your location?",
    example_answer="Arrrg! That is a secret!! Arg arg!!",
    question="What is your fav food?",
)
```



## Caching
### 1. Caching
- `Caching`은 언어 모델(LM)의 답변을 저장하고, 같은 질문이 다시 들어왔을 때 메모리 또는 다른 저장 매체에서 같은 답변을 내놓는 것을 말함.
- `set_llm_cache`와 `InMemoryCache`를 import해서 메모리 기반 캐시를 설정 가능함. 다른 캐시 구현테도 사용 가능
- 이전에 답변했던 질문이 다시 LM에 들어왔을 때, 다시 LM을 사용하지 않으므로 비용과 시간 절약 효과를 가짐
- `set_debug` 함수를 통해 전체적인 작업 상황과 디버깅 로그를 볼 수 있음. 체인 작업 시 유용
```python
from langchain.chat_models import ChatOpenAI
from langchain.callbacks import StreamingStdOutCallbackHandler
from langchain.globals import set_llm_cache, set_debug
from langchain.cache import InMemoryCache, SQLiteCache

set_llm_cache(InMemoryCache())
# set_llm_cache(SQLiteCache())  # 답변이 메모리가 아닌 db에 저장되길 원할 경우에 사용. ()안에 저장할 경로 입력, 기본값은 langchain.db에 저장됨
set_debug(True)

chat = ChatOpenAI(
    temperature=0.1,
    # streaming=True,
    
    # callbacks=[
    #     StreamingStdOutCallbackHandler(),
    # ],
    
)

# Before caching: response loading time - 3.7s
chat.predict("How do you make italian pasta")

# after caching: response loading time - 0.0s
chat.predict("How do you make italian pasta")
```



## Serialization
### 1. OpanAI 모델의 비용 알기
```python
from langchain.chat_models import ChatOpenAI
from langchain.callbacks import get_openai_callback

chat = ChatOpenAI(
    temperature=0.1,
)

# OpenAI의 모델을 사용할 때 지출하는 비용을 아는 방법
with get_openai_callback() as usage:
    a = chat.predict("What is the recipe for soju")
    b = chat.predict("What is the recipe for bread")
    print(a, b, "\n")
    print(usage)
```


### 2. 모델 저장과 로딩
```python
# 모델 선택 후 저장
from langchain.chat_models import ChatOpenAI
from langchain.llms.openai import OpenAI

chat = OpenAI(
    temperature=0.1,
    max_tokens=450,
    model="gpt-3.5-turbo-16k"
)

chat.save("model.json")
```
```python
# 모델 불러오기
from langchain.llms.loading import load_llm

chat = load_llm("model.json")
chat
```
