#TIL #스파르타코딩클럽 [[2411]]

## 1. ConversationBufferMemory
### Conversation Buffer Memory
- 대화의 모든 내용을 문자열로 저장하는 방식
- 오래된 대화까지 모두 그대로 담기 때문에 메모리 사용량에 주의
```python
from langchain.memory import ConversationBufferMemory

memory = ConversationBufferMemory()

# 메모리에 정해진 대화 내용을 저장하는 함수 (모든 메모리 API에서 동일)
memory.save_context({"input": "hi"}, {"output": "How are you?"})

# 메모리에 저장된 대화들을 불러오는 함수 (모든 메모리 API에서 동일)
memory.load_memory_variables({})

'''
{'history': 'Human: hi\nAI: How are you?'}
'''
```

```python
# return_message=True: 대화 내용을 리스트에 담아서 보여줌
memory = ConversationBufferMemory(return_messages=True)  

memory.save_context({"input": "hi"}, {"output": "How are you?"})

memory.load_memory_variables({})

'''
{'history': [HumanMessage(content='hi'), AIMessage(content='How are you?')]}
'''
```


---
## 2. ConversationBufferWindowMemory
### Conversation Buffer Window Memory
- Window의 크기만큼만의 최신 대화 내용을 저장
- window size를 넘어가는 메세지가 들어오면 제일 오래된 메세지가 탈락
- 장점: 버퍼가 너무 커지지 않도록 관리
- 단점: 긴 대화의 흐름이 필요한 경우 이전 대화 기록이 없어질 수 있음

```python
from langchain.memory import ConversationBufferWindowMemory

memory = ConversationBufferWindowMemory(return_messages=True, k=3) # k: window size

def add_message(input, output):
    memory.save_context({'input': input}, {'output': output})

add_message(1, 1)
add_message(2, 2)
add_message(3, 3)

memory.load_memory_variables({})

'''
{'history': [HumanMessage(content='1'),
  AIMessage(content='1'),
  HumanMessage(content='2'),
  AIMessage(content='2'),
  HumanMessage(content='3'),
  AIMessage(content='3')]}
'''
```

```python
# k값보다 더 많이 메세지를 넣을 경우

add_message(4, 4)
memory.load_memory_variables({})

'''
{'history': [HumanMessage(content='2'),
  AIMessage(content='2'),
  HumanMessage(content='3'),
  AIMessage(content='3'),
  HumanMessage(content='4'),
  AIMessage(content='4')]}
'''
```