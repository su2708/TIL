#TIL #스파르타코딩클럽 [[2411]]

## 1. Vector Store
### 1. CacheBackedEmbeddings
- 목적: 이미 임베딩된 텍스트가 다시 요청될 때, **다시 임베딩하는 것을 피하고 기존 임베딩 결과를 빠르게 반환**하기 위함
- 동작 방식: 캐시 저장소에 저장해두고, 중복 요청 시 캐시된 결과를 반환
- ==임베딩 단계에서 중복 요청을 최소화해주는 역할==


### 2. Vector store
- 목적: **대량의 임베딩된 벡터를 효율적으로 저장하고 검색**하는 데 중점
- 동작 방식: 임베딩된 벡터를 저장하고, 빠른 검색을 위해 인덱싱 및 최적화된 검색 알고리즘을 적용
- Chroma, FAISS와 같은 벡터 데이터베이스를 활용해 다양한 저장소에 저장 가능
- ==최종 임베딩된 결과가 저장된 후 이를 효율적으로 검색하는 역할==



## 2. RetrievalQA
### 1. Retriever
- #### Key concept
![[retriever_concept.png]]

- Retriever
	- LangChain이 제공하는 class 또는 interface의 일종
	- document를 검색해서 찾아오는 기능

- Retrieval 과정
	1. Query Embedding: 사용자의 입력 쿼리를 벡터화하여 숫자로 변환
	2. Vector Search: 쿼리 벡터와 DB 내 문서 임베팅 벡터들 간의 유사도를 비교하여, 관련성이 높은 문서를 선별
	3. Document Retrieval: 선별된 문서들이 LLM에 전달되어 최종 응답 생성에 사용

- Retriever의 주요 특징
	- 필터링: 대량의 데이터에서 쿼리에 적합한 문서만 추려냄
	- 정확도 향상: 관련 정보만을 모델에 전달하기 때문에, LLM이 보다 정확하고 관련성 높은 응답을 생성할 가능성이 커짐
	- 속도 향상: 방대한 데이터셋을 다 읽는 것이 아니라, 효율적인 검색을 통해 필요한 정보만 빠르게 추출


### 2. RetrievalQA
- Retriever와 질문 응답 시스템(QA)이 연결되어 동작하는 체인
- RetrievalQA의 주요 구성 요소
	1. retriever: 사용자의 쿼리와 관련된 문서나 정보를 찾아주는 역할. 이 단계에서 관련 문서가 선택됨
	2. LLM (Language Model): Retriever가 반환한 문서들을 기반으로 최종적인 응답을 생성
	3. chain_type: RetrievalQA를 어떤 타입의 체인이라고 설정하는 역할
		- stuff (default): 단순히 Retriever가 선별한 문서들을 읽어서 답변을 생성
		- refine: 선별된 문서들을 읽으면서 답변을 생성하고, 그것을 기반으로 처음의 답변들 업데이트하며 답변을 정제함
		- map_reduce: 선별된 문서들을 순회하면서 개별 답변을 찾아낸 후, 각각의 답변들로 일종의 중간 답변을 생성. 이것을 기반으로 LLM의 최종 응답을 반환
		- map_rerank: 선별된 문서들을 순회하면서 개별 답변을 찾아낸 후, 각 답변에 점수를 매겨 가장 높은 점수의 답변을 최종 응답으로 반환
```python
vectorstore = FAISS.from_documents(docs, cached_embeddings)

chain = RetrievalQA.from_chain_type(
    llm=llm,
    chain_type='stuff',
    retriever=vectorstore.as_retriever(),
)

chain.run("A sentence that you want.")
```



---
> 이미지 출처: [LangChain docs](https://python.langchain.com/docs/introduction/)