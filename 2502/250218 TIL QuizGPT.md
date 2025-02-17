#TIL [[TIL]]
## 9.4 Formatter Prompt
### Format을 지정하는 이유
- 특정 형식을 강제하여 일관된 프롬프트 구조를 유지할 수 있음
- LLM이 일관된 형식으로 응답을 하도록 유도할 수 있음
```python
questions_prompt = ChatPromptTemplate.from_messages([
    (
        "system",
        """
        You are a helpful assistant that is role playing as a teacher.
        
        Based ONLY on the following context make 10 questions to test the user's knowledge about the text.
        
        Each question should have 4 answers, three of them must be incorrect and one should be correct.
        
        Use (o) to signal the correct answer.
        
        Question examples:
        
        Question: What is the color of the ocean?
        Answers: Red | Yellow | Green | Blue(o)
        
        Your turn!
        
        Context: {context}
        """
    )
])

questions_chain = {
    "context": format_docs
} | questions_prompt | llm

formatting_prompt = ChatPromptTemplate.from_messages([
    (
        "system",
        """
        You are a powerful formatting algorithm.

        You format exam questions into JSON format.
        Answers with (o) are the correct ones.

        Example Input:
        Question: What is the color of the ocean?
        Answers: Red|Yellow|Green|Blue(o)

        Example Output:

        ```json
		{{
			"question": "What is the color of the ocean?",
			"answers": [
				{{
					"answer": "Red",
					"correct": false
				}},
				{{
					"answer": "Yellow",
					"correct": false
				}},
				{{
					"answer": "Green",
					"correct": false
				}},
				{{
					"answer": "Blue",
					"correct": true
				}},
			]
		}}
        ```
        Your turn!
        Questions: {context}
        """,
    )
])

formatting_chain = formatting_prompt | llm
```

### {}와 {{}}의 차이
- {}: prompt에 어떤 값을 주입하고 싶을 때 사용
- {{}}: prompt에서 format 하지 않았으면 하는 부분들을 지정할 때 사용


---
## 9.5 Output Parser
### Output Parser의 역할
1. LLM 응답을 특정 포맷으로 변환
	- LLM은 기본적으로 String 형태로 출력을 제공
	- String이 아닌 JSON, Python 객체, 리스트, 숫자 등 원하는 데이터 구조로 변환할 때 사용

2. 응답을 정제 및 후처리
	- LLM의 응답이 불완전하거나 불필요한 정보를 포함할 수 있음
	- Output Parser는 이를 정리하여 필요한 데이터만 추출하는 역할을 수행

3. 안정성 확보
	- API 연동, 데이터 저장 등에서 일관된 포맷을 데이터가 필요할 때 유용함
	- LLM이 예상치 못한 출력을 내는 경우를 방지


---
## 9.8 Function Calling
### Function Calling
- GPT 모델이 함수(예: API 호출, DB 조회, 계산 등)를 직접 실행할 수 있도록 지원하는 기능
- GPT가 단순한 텍스트 응답이 아니라, 외부 함수와 연동하여 더욱 강력한 기능을 수행할 수 있도록 함

### Function Calling을 사용하는 이유
4. 정확한 응답 제공
	- GPT는 정보를 생성하는 능력이 있지만, **정확한 데이터는 보장할 수 없음**
	- Function Calling을 이용하면, GPT가 실시간으로 DB 조회, API 요청을 해서 정확한 정보를 제공할 수 있음

5. LLM을 API 인터페이스로 사용
	- Function Calling을 사용하면 GPT가 다양한 API와 직접 상호작용할 수 있음
	- GPT가 사용자의 요청을 분석하고 적절한 API를 호출할 수 있음

6. 복잡한 작업 자동화
	- Function Calling을 이용하면 GPT가 코드를 실행하거나, 여러 기능을 조합하여 자동화된 업무를 수행할 수 있음

### Function Calling 사용법
7. OpenAI API에서 Function Calling 활성화
	- GPT 모델을 사용할 때, `functions` 파라미터를 설정하면 함수 호출을 활성화 할 수 있음

8. 함수 정의 & OpenAI에 등록
	- OpenAI에게 사용할 함수의 **이름(name), 설명(description), 매개변수(parameters)** 를 JSON 형식으로 전달

```python
import openai
import json

openai.api_key = "YOUR_OPENAI_API_KEY"

# 1️⃣ 함수 정의 (날씨 조회)
functions = [
    {
        "name": "get_weather",
        "description": "Fetches the current weather for a given city.",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "City name to get the weather for"
                }
            },
            "required": ["location"]
        }
    }
]

# 2️⃣ 실제 날씨 정보를 반환하는 함수
def get_weather(location):
    weather_data = {
        "Seoul": {"temperature": "12°C", "condition": "Clear"},
        "New York": {"temperature": "8°C", "condition": "Cloudy"},
    }
    return weather_data.get(location, {"temperature": "Unknown", "condition": "Unknown"})

# 3️⃣ GPT 모델 호출 (Function Calling 활성화)
response = openai.ChatCompletion.create(
    model="gpt-4-0613",  # Function Calling 지원 모델 사용
    messages=[{"role": "user", "content": "서울의 날씨 어때?"}],
    functions=functions,
    function_call="auto"  # 자동으로 적절한 함수 선택
)

# 4️⃣ GPT가 함수 호출을 요청했는지 확인
if response["choices"][0]["message"].get("function_call"):
    function_name = response["choices"][0]["message"]["function_call"]["name"]
    function_args = json.loads(response["choices"][0]["message"]["function_call"]["arguments"])
    
    if function_name == "get_weather":
        result = get_weather(**function_args)  # 실제 함수 실행
    
    print(f"🌦️ 서울의 현재 날씨: {result['temperature']} ({result['condition']})")

```

