#TIL #스파르타코딩클럽 [[2411]]

## 1. HuggingFacePipeline 
### HuggigFace Pipeline
- Hugging Face 모델을 로컬에서 실행할 수 있도록 해주는 클래스
- Hugging Face의 `transformers` 라이브러리를 통해 로컬 컴퓨터에서 모델을 실행


### 특징
- 모델을 로컬 머신에 다운로드하여 실행
- python 코드로 간단히 사용 가능
- 네트워크 요청 없이 작동하여 클라우드 비용 없이 무료로 사용 가능
- 데이터 보안 측면에서 유리
- 로컬 컴퓨터의 성능 혹은 GPU의 여부로 인해 모델 실행의 속도가 달라짐


### Hugging Face Endpoint vs Hugging Face Pipeline
| 특징    | Hugging Face Endpoint          | Hugging Face Pipeline          |
| ----- | ------------------------------ | ------------------------------ |
| 역할    | 모델을 API로 호출하여 추론 수행            | 로컬 환경에서 모델을 로드하여 직접 추론 수행      |
| 호스팅   | Hugging Face의 클라우드 인프라에서 바로 실행 | 사용자의 로컬 컴퓨터에서 실행               |
| 추론 방식 | REST API를 통해 클라우드에서 추론 결과 제공   | 로컬 메모리에 모델을 로드하여 python 코드로 실행 |
| 사용 목적 | 빠르고 간편한 모델 추론 및 API 통합         | 소규모 애플리케이션, 실험적 사용, 오프라인 추론    |
| 확장성   | 클라우드 기반으로 자동 확장 지원             | 로컬 리소스에 의존, 확장이 어려움            |
| 요금제   | API 호출량에 따라 사용 요금 부과           | 로컬에서 실행 시 무료, 추가 리소스 비용 없음     |


### 사용 순서
#### Model Download
```python
%pip install --upgrade --quiet  transformers --quiet
```

```python
# 허깅페이스 모델/토크나이저를 다운로드 받을 경로
# (예시)
import os

# ./cache/ 경로에 다운로드 받도록 설정
os.environ["TRANSFORMERS_CACHE"] = "./cache/"
os.environ["HF_HOME"] = "./cache/"
```

#### Model Loading
```python
from langchain.llms.huggingface_pipeline import HuggingFacePipeline
from langchain.prompts import PromptTemplate

# HuggingFace 모델을 다운로드
llm = HuggingFacePipeline.from_model_id(
    model_id="openai-community/gpt2",  # 사용할 모델의 ID
    task="text-generation",  # 수행할 작업 지정
    device=0,  # 0: GPU, -1: CPU (default)
    
    # 파이프라인에 전달할 추가 인자 설정 
    pipeline_kwargs={
        "max_new_tokens":50
    }
)

prompt = PromptTemplate.from_template("A {word} is a")

chain = prompt | llm
result = chain.invoke({"word": "banana"})
print(result)
```

- HuggingFace에서 원하는 모델 선택
	> [HuggingFace Models](https://huggingface.co/models)

- `from_model_id()`메서드를 사용하여 모델 매개변수를 지정함으로써 모델을 로드
	> [HuggingFace Pipeline](https://python.langchain.com/api_reference/huggingface/llms/langchain_huggingface.llms.huggingface_pipeline.HuggingFacePipeline.html#langchain_huggingface.llms.huggingface_pipeline.HuggingFacePipeline)

---
## 2. GPT4All
### GPT4All
- LLM을 로컬에서 실행할 수 있는 또 다른 방법
- HuggingFace Pipeline에 비해 모델이 다양하지 않지만 API나 GPU 등이 필요하지 않음
- GPT4ALL 페이지의 모델을 로컬에 다운로드 받아 LLM을 실행
	> [GPT4All](https://www.nomic.ai/gpt4all)
- ChatGPT와 유사한 인터페이스
	![[gpt4all.png]]


### 사용 예시
```python
%pip install gpt4all
```

```python
from gpt4all import GPT4All

# GPT4All의 모델 다운로드
model = GPT4All(
    model_name="Meta-Llama-3-8B-Instruct.Q4_0.gguf",  # 다운로드 받을 모델 명 
    model_path="./files/gpt4all",  # 모델의 다운로드 경로 
) # downloads / loads a 4.66GB LLM

with model.chat_session():
    print(model.generate("How can I run LLMs efficiently on my laptop?", max_tokens=1024))  # generate() 메서드로 답변 생성
```
