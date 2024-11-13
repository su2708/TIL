#TIL #스파르타코딩클럽 [[2411]]

## LangSmith
### LangSmith
- 언어 모델 개발 및 배포를 돕는 도구로, 언어 모델 기반 앱을 효율적으로 관리하고 최적화할 수 있도록 설계됨
- 주로 LangChain 라이브러리와 함께 사용되며, LLM을 활용하는 과정에서 성능을 모니터링하고 최적화하는 데 도움을 줌
- logging, tracing, 성능 모니터링과 같은 기능을 통해 개발자가 모델의 동작을 이해하고 개선할 수 있는 환경을 제공


### LangSmith의 동작 원리
- 주로 LangChain이 생성하는 각 호출과 응답을 추적해 기록
- 대화형 응답, 생성된 텍스트, 메모리 사용량 등의 다양한 메트릭을 수집&분석 가능
- LangSmith의 동작 방식
	1) 로깅 및 추적: 모든 API 호출을 기록하고 이를 추적하여 각 호출의 성능을 분석 가능
	2) 실패 디버깅: 에러 로그를 통해 문제의 원인을 파악하고 해결
	3) 성능 최적화: 각 요청의 응답 시간을 분석해 모델이 처리 속도를 최적화하도록 도움


### LangSmith의 이점
- 효율적인 디버깅: 에러 로그와 호출 추적을 통해 에러를 쉽게 확인하고 수정 가능
- 성능 모니터링: 각 호출의 응답 시간을 분석해 앱의 성능을 지속적으로 개선 가능
- 최적화된 개발 환경: 로그와 데이터를 시각화하여 모델의 작동 방식과 성능 파악에 도움
- LangSmith 화면 예시
![[langsmith_tracing_example.png]]


### LangSmith에서의 Prompt 생성 과정
1. Prompt Template 로컬 저장
	- prompt template을 미리 정의하고, 이를 로컬에 저장하거나 코드 내에서 관리
	- template에는 기본적으로 사용될 구조와, 특정 변수 자리 표시자가 포함됨
2. Prompt Template 로드
	- 앱이 실행될 때, LangSmith가 prompt template을 로컬에서 불러옴
3. Input Variable과 결합
	- input variable을 template의 변수 자리 표시자({{variable}})에 매핑하여 결합
4. 최종 Prompt 생성
	- 최종 prompt를 생성한 후, 이를 LLM 모델에 전달하여 원하는 결과를 생성
![[prompt_vs_prompt_template.png]]


### LangSmith와 Prompt 생성의 연관성
- **일관된 테스트 환경**
	- 동일한 prompt template을 여러 변수와 조합해 다양한 상황에서 테스트해 보면, 모델의 일관성 및 응답 품질을 분석하기가 쉬워짐
	- LangSmith를 통해 일관된 prompt 형식을 추적하면, 모델이 특정 형태의 입력에 어떻게 반응하는지 더 체계적으로 파악 가능
    
- 성능 최적화
	- prompt template의 구조를 변경해보며 어떤 형식이 최적의 성능을 내는지 실험 가능
	- LangSmith는 prompt에 따른 성능 데이터를 제공해, 어떤 template이 최적의 결과를 내는지 확인하고 최적화할 수 있는 근거를 제시해줌
    
- 디버깅 편의성
	- 특정 상황에서 모델의 응답이 예상과 달랐을 때, 사용된 prompt 템플릿과 변수 조합을 확인하여 원인을 파악하기 쉬움
	- LangSmith는 이를 위해 prompt template과 관련된 로깅 기능을 제공하여, prompt의 구성 요소와 결과 간의 상관관계를 추적