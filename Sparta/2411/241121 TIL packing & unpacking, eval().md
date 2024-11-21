#TIL #스파르타코딩클럽 [[2411]]

## 1. packing, unpacking
### packing
- `print()`처럼 함수가 받을 인자의 개수를 유연하게 지정하기 위한 python 기능
- **인자로 받은 여러 개의 값을 하나의 객체(tuple)로 합쳐서** 받을 수 있도록 함
- **위치 인자 패킹은 `*` 한 개를 매개변수 앞에 붙여서 사용**
```python
def func(*args):
	print(args)
	print(type(args))

func(1, 2, 3, 4, 'a', 'b')

'''
(1, 2, 3, 4, 'a', 'b')
<class 'tuple'>
'''
```

- **키워드 인자는 패킹한 인자들을 키워드와 인자 쌍으로 이뤄진 딕셔너리로 관리**
- **키워드 인자 패킹은 `**`를 매개변수 앞에 붙여서 사용**
```python
def print_family_name(dad, mom, **sibling):
	print("parents")
	print("dad: ", dad)
	print("mom: ", mon)
	if sibling:
		print("sibling")
		for title, name in sibling.items():
		print('{}: {}'.format(title, name))

print_family_name("홍길동", "신사임당", 누나="김태희", 여동생="윤아")

'''
parents
dad: 홍길동
mom: 신사임당
sibling
누나: 김태희
여동생: 윤아
'''
```


### unpacking
- packing과 반대되는 개념
- **여러 개의 객체를 포함하고 있는 하나의 객체를 풀어줌**
- packing과 반대로 **인자 앞에 `*`를 붙여 사용**
	- 위치 인자를 unpacking하는 경우 - `*`
	- 키워드 인자를 unpacking하는 경우 - `**`
```python
def sum(a, b, c):
	return a+b+c

nums = [1, 2, 3]

print(sum(*nums))  # 출력: 6
```

```python
def cal(first, op, second):
    if op == '+':
        return first + second
    if op == '/':
        return first / second
    if op == '-':
        return first - second
    if op == '*':
        return first * second

prob = {
  'first': 12,
  'second': 34,
  'op': '*'
}

cal(**prob) # 결과 : 408
```

---
## 2. eval()
### eval()
- 임의의 문자열 기반 또는 컴파일된 코드 기반 인풋의 표현식을 평가해서 실행하는 python 내장 함수
- `eval()`함수의 처리 순서
	1. 문자열 기반 표현식을 파싱
	2. 파싱한 결과를 바이트 코드로 컴파일
	3. 파이썬 표현식으로 평가
	4. 평가한 결과를 하나의 값으로 반환
```python
##-- Math expressions
eval("2 + 6")  # 8

eval("10**2")  # 100

eval("sum([1, 2, 3, 4, 5])")  # 15

import math
eval("math.pi * pow(5, 2)")  # 78.53981633974483

## -- eval() for Boolean expressions
x = 10

## (a) value comparison operators: <, >, <=, >=, ==, !=
eval("x > 5")  # True

## (b) logical operstors: and, or, not
eval("x > 5 and x < 9")  # False

## (c) membership test operators: in, not in
eval("x in {1, 5, 10}")  # True

## (d) identity operators: is, is not
eval("x is 10")  # True
```


### eval()을 사용하는 이유
- 동적으로 문자열 기반의 조건절 표현식을 바꿔가면서 쓸 수 있기 때문에 강력하고 편리함
```python
def func(a, b, condition):
	if eval(condition):
		return a+b
	return a-b

func(5, 10, "a>b")  # -5

func(3, 7, "a<=b")  # 10

func(2, 8, "a is b")  # -6
```


### eval()을 사용하지 않는 이유
- 아래의 단점들 때문에 **되도록 사용하지 않는 것을 권장**
	- `eval()`에 넣어준 표현식을 그대로 실행하는 것이기 때문에 보안에 매우 취약
	- 코드의 가독성을 떨어뜨리고, 디버깅을 어렵게 만듦
	- `eval()`을 사용해 일부 로컬 환경에 의존하도록 구현한 경우, 환경 의존성이 생길 수 있음