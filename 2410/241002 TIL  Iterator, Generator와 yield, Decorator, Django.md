#TIL #스파르타코딩클럽 [[TIL]]

## 이터레이터(Iterator)
### 1. 반복 가능한 객체(Iterable)
- 하나씩 차례대로 값을 꺼내올 수 있는 객체
- python에서 list, tuple, string, dictionary 등은 모두 iterable
- 이들은 `for` 루프에서 반복 가능, 내부적으로는 `__iter__()` 메서드를 통해 이터레이터를 반환


### 2. 이터레이터(Iterator)
- 반복 가능한 객체의 요소를 하나씩 꺼내오는 객체
- `__iter__()`와 `__next__()` 메서드를 구현해야 함
- `__next__()` 메서드를 호출할 때마다 다음 요소를 반환
- 더 이상 꺼낼 요소가 없으면 `StopIteration` 예외를 발생시켜 순회를 종료
```python
class MyIterator:
    def __init__(self, data):
        self.data = data
        self.index = 0
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.index < len(self.data):
            item = self.data[self.index]
            self.index += 1
            return item
        else:
            raise StopIteration

# 이터레이터 사용
my_iter = MyIterator([1, 2, 3])
for item in my_iter:
    print(item)
```




## 제너레이터(Generator)와 yield
### 1. 제너레이터(Generator)
- 이터레이터의 한 종류로, 함수 내에서 `yield` 키워드를 사용해 값을 순차적으로 반환
- 일반 함수와 달리, 제너레이터는 한 번에 하나씩 값을 생성
- 호출될 때마다 이전 상태를 기억하고 중단된 지점부터 실행을 재개


### 2. yield
- `yield`는 반환 후 함수의 상태를 저장하고, 이후 호출 시 다시 이어서 실행
- 메모리 효율이 뛰어나 큰 데이터셋을 처리할 때 유용
```python
def my_gen():
    yield 1
    yield 2
    yield 3

# 제너레이터 사용
gen = my_gen()
for value in gen:
    print(value)
```


### 3. 제너레이터 표현식
- 리스트 컴프리헨션과 비슷하게 `()`를 사용하여 제너레이터 생성 가능
```python
gen_exp = (x * x for x in range(5))

for num in gen_exp:
    print(num)
```




## 데코레이터(Decorator)
### 1. 데코레이터(Decorator)
- 기존 함수나 메서드에 새로운 기능을 추가하거나 수정할 때 사용되는 함수
- 다른 함수를 인자로 받아, 그 함수의 동작을 변경하거나 확장하는 방식으로 동작
- `@` 기호를 함수나 메서드 앞에 붙여 사용
- 함수에 직접 수정 없이 기능을 추가할 수 있어 코드 재사용성과 가독성을 높임
```python
def deco_func(original_func):
    def wrapper(*args, **kwargs):
        # 추가할 기능
        print("before decoration")
        original_func(*args, **kwargs)
        print("after decoration")
    return wrapper

@deco_func  # display에 데코레이터 적용
def display():
    print("execute original function")

display()

# before decoration
# execute original function
# after decoration
```


### 2. 데코레이터 체이닝
- 여러 데코레이터를 하나의 함수에 적용할 수 있음
- 이 경우 데코레이터는 안쪽부터 바깥쪽으로 차례대로 적용
```python
@decoration1  # 나중에 적용
@decoration2  # 먼저 적용
def func():
	pass
```




## Django
### 1. Django
- python 기반의 고수준 웹 프레임워크로, 빠르고 효율적인 웹 개발을 돕는 도구
- 보안이 강화된 기본 기능을 제공하며, 스케일링이 가능해 대규모 앱 개발에도 적합


### 2. 주요 구성 요소
1) 모델(Model)
	- 데이터베이스 구조를 정의하고, 데이터와 상호작용하는 역할
2) 뷰(View)
	- 사용자가 보는 화면을 담당. 모델에서 가져온 데이터를 사용해 템플릿에 전달
3) 템플릿(Template)
	- HTML을 생성하는 역할. 뷰에서 전달받은 데이터를 표시
4) URL 컨피그(URLconf)
	- URL과 뷰를 연결하는 역할. 사용자가 특정 URL로 접근할 때 어떤 뷰를 호출할 지 결정


### 3. Django의 기본 동작 흐름
1) URL 요청
	- 사용자가 웹 브라우저에서 특정 URL을 요청
2) 뷰 호출
	- Django의 URLconf가 해당 URL에 매핑된 뷰를 호출
3) 데이터 처리 및 템플릿 렌더링
	- 뷰는 필요한 데이터를 모델에서 가져오고, 템플릿에 전달하여 HTML을 생성
4) HTML 응답
	- 생성된 HTML을 웹 브라우저에 반환하여 사용자에게 표시


### MVT(Model - View - Template) 패턴
- Django에서 사용하는 아키텍처 패턴
- 데이터(모델), 비즈니스 로직(뷰), 프레젠테이션(템플릿)으로 나우어 웹 애플리케이션의 구조를 정의
- URL 요청 -> 뷰 호출 -> 모델에서 데이터 조회 -> 템플릿에서 HTML 생성 -> 사용자에게 응답