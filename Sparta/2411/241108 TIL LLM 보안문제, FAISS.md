#TIL #스파르타코딩클럽 [[2411]]

## 1. LLM의 사용 준비하기
### 보안 문제
#### LLM을 사용할 때의 데이터 보안 문제
- 개인정보보호
	- LLM은 사용자의 민감한 정보(이름, 전화번호, 신용카드 정보 등)을 접할 수 있기 때문에 이런 데이터가 저장되거나 유출될 경우 큰 문제로 이어짐
- 데이터 저장 및 전송
	- LLM이 처리한 데이터가 어디에 저장되는지, 어떻게 전송되는지 반드시 확인
	- 암호화된 전송 방식(ex. HTTPS)을 사용해 반드시 데이터를 보호
- 모델 학습 데이터
	- 만약 학습 데이터에 민감한 정보가 포함된다면, 해당 정보가 예기치 않게 모델의 출력으로 등장할 가능성이 존재

#### 보안을 강화하는 방법
- 민감 정보 필터링
	- 입력된 데이터를 처리하기 전에 민감한 정보를 자동으로 필터링하는 시스템 구축
- 암호화
	- 데이터는 저장 및 전송 중에 암호화되어야 함
	- SSL/TLS와 같은 안전한 전송 프로토콜을 사용
- 데이터 저장 최소화
	- 필요 이상으로 데이터를 저장하지 않기
	- 데이터 보존 주기를 설정해 자동 삭제 
- 접근 통제
	- LLM을 사용할 수 있는 사람의 권한을 제한
	- 모델이 민감한 데이터에 접근하지 않도록 제한



## 2. Vector DB 개념 및 RAG 개념
### Vector DB 개념
#### Vector DB란?
- 데이터를 벡터 형식으로 저장하고, 그 벡터들을 효율적으로 검색할 수 있는 데이터베이스
- 유사한 벡터 간의 검색을 지원

#### 벡터(임베딩)의 역할
- 텍스트나 이미지 등의 비정형 데이터를 벡터화(임베딩)해서 저장
- 벡터는 데이터의 의미나 특징을 수치로 표현한 것
- 벡터의 수치를 바탕으로 유사도를 계산해 관련성이 높은 항목을 찾음

#### FAISS란?
- Facebook AI Research에서 개발한 벡터 검색 엔진
- 대규모 벡터를 효율적으로 검색하고, 유사도를 계산하는 데 탁월한 성능을 발휘



## 3. Python LangChain과 FAISS
### FAISS를 활용한 벡터 데이터베이스 구성 및 쿼리
#### Step 1: OpenAI 임베딩 모델로 벡터 임베딩 생성
```python
from langchain_openai import OpenAIEmbeddings

# OpenAI 임베딩 모델 초기화
embeddings = OpenAIEmbeddings(model="text-embedding-ada-002")
```

#### Step 2: FAISS 인덱스 초기화
```python
import faiss
from langchain_community.vectorstores import FAISS
from langchain_community.docstore.in_memory import InMemoryDocstore

# FAISS 인덱스 생성
index = faiss.IndexFlatL2(len(embeddings.embed_query("hello world")))
vector_store = FAISS(
    embedding_function=embeddings,
    index=index,
    docstore=InMemoryDocstore(),
    index_to_docstore_id={}
)
```

#### Step 3: 벡터 데이터베이스에 문서 추가
```python
from langchain_core.documents import Document
from uuid import uuid4  # 고유 ID를 생성해주는 라이브러리

# 문서 생성
documents = [
    Document(page_content="LangChain을 사용해 프로젝트를 구축하고 있습니다!", metadata={"source":"tweet"}),
    Document(page_content="내일 날씨는 맑고 따뜻할 예정입니다.", metadata={"source":"news"}),
    Document(page_content="오늘 아침에는 팬케이크와 계란을 먹었어요.", metadata={"source":"personal"}),
    Document(page_content="주식 시장이 경기 침체 우려로 하락 중입니다.", metadata={"source":"news"}),
]

# 고유 ID 생성 및 문서 추가
uuids = [str(uuid4()) for _ in range(len(documents))]
vector_store.add_documents(documents=documents, ids=uuids)
```

#### Step 4: 벡터 데이터베이스 쿼리
```python
# 기본 유사성 검색
results = vector_store.similarity_search("내일 날씨는 어떨까요?", k=2, filter={"source": "news"})
for res in results:
    print(f"* {res.page_content} [{res.metadata}]")
    
# 점수와 함께 유사성 검색
results_with_scores = vector_store.similarity_search_with_score("LangChain에 대해 이야기해주세요.", k=2, filter={"source": "tweet"})
for res, score in results_with_scores:
    print(f"* [SIM={score:.3f}] {res.page_content} [{res.metadata}]")
```