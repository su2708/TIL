#TIL #스파르타코딩클럽 [[2411]]

## 1. DocumentGPT

### DocumentGPT

- Transformer 아키텍쳐를 기반으로 하는 문서 작업에 특화된 GPT
- 문서의 요약, 정리 분석 및 다양한 문서 기반 작업을 수행
- 언어 모델의 기술적 원리를 바탕으로 문서에서 중요한 정보만을 효율적으로 추출하고, 긴 문서의 흐름과 논리 구조를 이해하여 사용자의 다양한 요청에 맞춰 문서 기반 작업을 수행

### DocumentGPT의 아키텍쳐

- Self-Attention Mechanism  
    - 각 단어가 문장 내 다른 단어와의 연관성을 계산하여 문맥을 이해  
    - 문서 내의 긴 문장 구조와 핵심 내용 간의 연관성을 파악  
    
- Layer Normalization  
    - 모델 학습 시 안정성과 성능 향상을 위해 레이어 별로 정규화 과정을 거침
    
- Residual Connections  
    - 정보를 더욱 효과적으로 전달하기 위해 각 레이어에 이전 레이어의 출력을 더하여 정보 손실을 방지
    

---

## 2. DocumentGPT의 기능과 기술 원리

### 1. 문서 요약

- Self-Attention과 여러 Transformer 레이어가 결합된 결과
- 문서에서 중요한 정보를 자동으로 식별해내는 방식으로 작동
- 모델은 문서의 주요 개념과 주제를 파악하고, 중요하지 않은 세부사항을 무시하도록 학습됨
- Attention Head  
    - 여러 Attention Head가 병렬로 동작하여 문서의 다양한 측면을 포착
- Hierarchical Attention  
    - 여러 단락에 걸쳐 연관된 문맥을 파악하여 중요한 구간을 선별하는 방식  
    

### 2. 문서의 의미적 분석

- Embedding을 사용하여 문서의 각 문장이 의미적으로 유사하거나 연결되는지를 평가
- Positional Encoding  
    - 문서 내 위치 정보를 반영
- Semantic Embedding  
    - 문서의 각 문장을 임베딩 공간에 매핑하여 의미적으로 유사한 문장이나 구문을 연결