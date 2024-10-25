#TIL #스파르타코딩클럽 [[2410]]


## NLTK (Natural Language Toolkit)
### NLTK
- 자연어 처리를 위한 파이썬 라이브러리


### 주요 기능
- 텍스트 토큰화 (Tokenization): 문장을 토큰으로 나누어 해석
```python
from nltk.tokenize import word_tokenize

text = "This is an example sentence."
tokens = word_tokenized(text)
print(tokens)   # ['This', 'is', 'an', 'example', 'sentence', '.']
```

-  불용어 제거 (Stopwords Removal): 의미가 적은 단어 필터링
```python
from nltk.corpus import stopwords

stop_words = set(stopwords.words('english'))
filtered_tokens = [word for word in tokens if word.lower() not in stop_words]
print(filtered_tokens)  # ['example', 'sentence', '.']
```

- 어간 추출 및 표제어 추출 (Stemming and Lemmatization): 단어의 원형 또는 어근으로 변환
	- Stemming: 단순히 접미사 제거
	- Lemmatization: 문법적 의미를 고려한 원형 추출

