#TIL #스파르타코딩클럽 [[2411]]

## 1. metadata
### metadata
- FAISS 벡터 스토어에서 검색된 벡터가 어떤 원본 데이터를 나타내는지 추적하기 위한 부가 정보
- FAISS는 벡터에 대한 추가 정보를 제공하지는 않고, 벡터를 저장하고 검색하는 데 특화됨


### metadata가 필요한 이유
1. 검색된 벡터가 어떤 데이터와 연결되어 있는지 확인
2. 검색 결과를 사용자에게 텍스트 형태로 표시
3. FAISS는 검색의 결과로 **벡터의 인덱스**를 반환하고, `metadata` 배열에서 **인덱스를 참조**해 원본 데이터를 가져옴

### Document 객체와 metadata
#### 1. 문서 컨텍스트 제공
- `metadata`는 문서의 작성자, 생성 날짜, 카테고리, 태그와 같은 컨텍스트 정보를 포함 가능
- 컨텍스트 정보를 통해 **원본 문서 뿐만 아니라 문서의 맥락 파악**에도 도움을 줌

#### 2. 효율적인 검색 및 필터링
- 대량의 문서 데이터를 다룰 때 `metadata`를 활용하면 효율적인 검색 및 필터링이 가능
- 검색하고자 하는 정보를 `metadata`에 저장하고 이를 기반으로 쿼리를 수행

#### 3. 데이터 정렬 및 분류
- `metadata`는 문서를 특정 기준으로 분류하거나 정렬하는 데 유용

#### 4. 추가 처리와 분석 지원
- `metadata`는 문서 내용을 기반으로 추가적인 처리를 하거나 분석 작업을 수행할 때 중요한 정보를 제공
- 인공지능 모델을 활용하여 문서를 분류하거나 요약하려면 `metadata`를 참고하여 보다 정확한 결과를 얻는 것이 가능

#### 5. 데이터 통합 및 상호운용성
- 다양한 시스템 간 데이터를 교환할 때 `metadata`는 문서를 다른 시스템과 통합하는 데 필요한 표준화된 정보를 제공
- 데이터의 일관성을 유지하고 상호운용성을 높일 수 있음

#### 6. 예제 코드

```python
from langchain.schema import Document

# Document 객체 생성
doc = Document(
    page_content="이 문서는 Python의 metadata에 대한 설명을 포함합니다.",
    metadata={
        "author": "홍길동",
        "created_date": "2024-11-27",
        "category": "기술 문서",
        "tags": ["Python", "metadata", "문서 관리"]
    }
)

# Metadata 활용
print("작성자:", doc.metadata["author"])
print("작성일:", doc.metadata["created_date"])
print("카테고리:", doc.metadata["category"])
print("태그:", doc.metadata["tags"])

---
# 출력
"""
작성자: 홍길동
작성일: 2024-11-27
카테고리: 기술 문서
태그: ['Python', 'metadata', '문서 관리']
"""
```