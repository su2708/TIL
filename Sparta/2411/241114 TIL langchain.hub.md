#TIL #스파르타코딩클럽 [[2411]]

## langchain.hub
### langchain.hub
- LangChain 라이브러리에서 프롬프트와 모델, 체인, 에이전트 등을 중앙 저장소에서 불러와 쉽게 사용할 수 있게 해주는 기능
- 자주 사용되는 프롬프트 템플릿이나 에이전트를 미리 정의해두고, 이를 쉽게 호출하고 관리할 수 있도록 지원


### 주요 역할과 기능
1. 중앙화된 프롬프트 및 체인 관리
	- `langchain.hub`는 다양한 프롬프트 템플릿, 체인, 에이전트를 중앙화된 라이브러리처럼 관리
	- 사용자들은 자주 사용하는 프롬프트나 체인을 개별적으로 정의할 필요 없이 미리 정의된 설정을 바로 사용 가능
2. 코드 간결화
	- 프롬프트를 처음부터 작성할 필요 없이, `hub`에서 제공하는 템플릿을 바로 호출해 사용하면서 코드를 간결화
3. 호환성과 표준화
	- 프로젝트에서 동일한 프롬프트와 체인 구성을 사용해야 할 때 유용
	- `langchain.hub`에서 템플릿을 가져오면 동일한 방식으로 사용되기 때문에 코드 호환성과 표준화를 유지 가능
4. 버전 관리
	- `langchain.hub`를 통해 프롬프트 템플릿의 버전을 관리할 수 있어, 특정 버전의 템플릿이나 체인을 안정적으로 호출 가능


### 사용 예시
```python
# LangChain library에서 prompt, model, chain, agent 등을 불러오는 라이브러리
from langchain import hub

# Prompts 디렉토리가 없다면 Prompts 디렉토리 만들기
os.makedirs("Prompts", exist_ok=True)

# LangChain library에서 prompt 가져오기
prompt1 = hub.pull("rlm/rag-prompt")
prompt2 = hub.pull("rlm/rag-document-relevance")
prompt3 = hub.pull("rlm/rag-answer-hallucination")

# 가져온 prompt들을 prompts list에 저장
prompts = [prompt1, prompt2, prompt3]
```
- 프롬프트를 매번 새로 정의할 필요 없이 `hub.pull()`로 프롬프트를 가져와 사용