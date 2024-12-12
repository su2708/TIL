#TIL #스파르타코딩클럽 [[2412]]

## 1. Output Parser
### Output Parser
- LangChain 프레임워크에서 다양한 종류의 출력 데이터를 파싱하고 처리하기 위한 중요한 컴포넌트
- 대형 언어 모델을 활용하여 텍스트 기반 애플리케이션을 만들 때 사용됨
- 모델의 출력 결과를 적절한 형식으로 변환하고, 이를 애플리케이션의 다음 단계로 전달하는 역할
- 출력 데이터를 string, json, List 등 원하는 형태대로 결과를 받아서 이후에 데이터 조작하기 쉽게 만들어줌
- 모든 OutputParser는 `get_format_instructions()`를 가지고 있고, Runnable이며 Chain에 주입이 가능. 또한 PromptTemplate를 통해 직접 instructions를 주입 하는 것도 가능


### Pydantic Output Parser
- 스키마에 대응하는 출력 형식으로, LLM에 쿼리하도록 명시하는 방식
```python
from pydantic import BaseModel
from langchain_core.prompts import PromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.pydantic_v1 import Field

class Person(BaseModel):
	name: str = Field(description="person's name")
	hometown: str = Field(description="person's hometown")
	birthday: str = Field(description="person's birthday")

# prompt 생성
chat_prompt = ChatPromptTemplate.from_messages(
	[
		(
			"system",
			"""
			You are an AI that provides information about historical figures.
			{format_instructions}
			""",
		),
		("human", "Tell me about {name}"),
	]
)

# chat model 생성
chat = ChatOpenAI()

# output parser 생성
output_parser = PydanticOutputParser(pydantic_object=Person)

# chain 형성
runnable = chat_prompt | chat | output_parser

# chain 실행
res = runnable.invoke(
	{
		"name": "소녀시대 윤아",
		"format_instructions": output_parser.get_format_instructions(),
	}
)

print(res)

---

"""
# output
name='윤아' hometown='서울, 대한민국' birthday='May 30, 1990'
"""

```


---
## 2. ERD 특강
### ERD
- 개체(Entity), 개체 간의 관계(Relationship), 개체의 속성(Attribute)을 시각적으로 표현한 다이어그램
![[ERD.webp]]


### ERD의 필요성
1. 비즈니스 요구 사항의 체계적 이해
	- 비즈니스 요구사항을 분석하고, 관리 대상이 되는 개체와 그들 사이의 관계를 명확히 파악 가능
	- 애매모호한 부분을 줄여 비개발자와 개발자 사이의 원활한 커뮤니케이션
2. 데이터 구조의 시각화
	- 복잡한 데이터 구조를 직관적으로 이해 가능
3. **정규화와 무결성 확보**
	- ERD를 기반으로 데이터 정규화를 진행 - **데이터 중복**과 **이상 현상**을 방지
	- 각 개체 간 **참조 무결성**을 확보
4. 개발 및 유지 보수 편의성 향상
	- 실제 구현 시 테이블 구조를 확정하고, 외래 키(Foreign Key)와 인덱스(Index)를 설정할 때 참조할 근거
	- DB 구조를 체계적으로 관리하고 수정할 수 있어 유지 보수와 확장성을 크게 개선


### ERD의 기본 구성 요소
1. 개체(Entity)
	- 데이터베이스에서 독립적으로 관리할 가치가 있는 "대상"을 의미 or 업무에서 관리하고자 하는 실체
	- 데이터베이스에서 테이블 단위로 표현

2. 속성(Attribute)
	- 해당 개체가 갖는 특성이나 정보
	- 데이터베이스에서 Column으로 표현
	- 개체의 속성 중에는 특정 행을 유일하게 식별하거나, 개체 간의 관계를 형성하는 데 사용되는 Key 개념이 중요 (대표적으로 Primary Key와 Foreign Key)

3. 관계(Relationship)
	- 두 개체 간의 연관성을 나타내며, 한 개체가 다른 개체에 어떻게 연결되는지 표현


### ERD 표현 방법
1. 개체(Entity)
	- 직사각형 상자로 표현하며, ERD 상에서는 직사각형 제일 위쪽에 표시
	- 개체명을 명확하고 단수형으로 작성하는 것이 일반적

2. 속성(Attribute)
	- 개체의 특성을 표현하는데, 다양한 방식으로 표현
	    - 동그라미로 표현
	    - 개체 상자 안에 기입
	    - ERD 상에서는 개체명 아래에 나열

3. 관계(Relationship)
	- 개체와 개체를 연결하는 선으로 표현
	    - 관계명이나 관계 기호
	    - 마름모형
	    - (글)동사나 동사구로 표현


### 관계 표현 방법
![[관계표현방법.webp]]


> [ERD 특강](https://teamsparta.notion.site/ERD-15a2dc3ef51480de98adc191add79038)
