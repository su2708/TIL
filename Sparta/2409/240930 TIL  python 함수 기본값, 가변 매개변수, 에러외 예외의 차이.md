#TIL #스파르타코딩클럽 [[2409]]

## 기본값 (Default Parameter)
- 함수 정의 시 **매개변수에 미리 지정된 값**을 의미
- 매개변수에 기본값을 줬으므로 함수 호출 시 인수 생략 가능
- ==인수가 전달되지 않으면 기본값이 사용됨==
- 여러 매개변수에 대해 기본값을 가질 수 있음
- 그러나 **기본값이 있는 매개변수는 항상 기본값이 없는 매개변수 뒤**에 와야 함

```python
def order(customer, hour, fee=20000, extra_fee=1000):
	return f"{customer}님, {hour}시간 이용하셔서 총 {fee + extra_fee*hour}원 입니다."

print(order("Jason", 1))               # 총 21000원
print(order("Matt", 2, 25000))         # 총 27000원
print(order("Adam", 2, 30000, 1500))   # 총 33000원
```




## 가변 매개변수 (Variable-Length Arguments)
### 1. args
- ==임의의 위치 인수의 튜플==을 받음
- 인수의 개수가 정해지지 않았을 때 유용

```python
def addition(*args):
	return sum(args)

print(addition(1, 3, 19))  # 23
```


### 2. kwargs
- ==임의의 키워드 인수의 딕셔너리==를 받음

```python
def show_car_spec(**kwargs):
	for key, value in kwargs.items():
		print(f"{key}: {value}")

show_car_spec(name="k5", color="white", release="2018")
# name: k5
# color: white
# release: 2018
```




## 에러와 예외의 차이
### 1. 에러 (Error)
- 프로그램이 실행 중에 발생하는 문제. 종종 프로그램이 비정상적으로 종료되게 함


### 2. 예외 (Exception)
- 에러의 한 종류로, 예외가 발생하면 프로그램이 멈추고 에러 메시지를 출력
- 예외 처리를 통해 프로그램이 멈추지 않고, 에러를 처리하며 계속 실행되게 할 수 있음


### 3. 예외 처리의 기본 구조 (try, except)

```python
try:
	# 예외가 발생할 가능성이 있는 코드
except:
	# 예외가 발생했을 때 실행할 코드
```


### 4. 여러 예외 처리
- 하나의 try 블록에서 여러 개의 except 블록을 사용해 다양한 예외를 처리 가능

```python
try:
	x = int(input("Please enter a number"))
	result = 100/x

except ValueError:
	print("Invalid number")
except ZeroDivisionError:
	print("Do not divided by 0")
```
```python
try:
	pass
except Exception as e:
	print(f"{e} error occured")  # 모든 예외 처리
```


### 5. finally
- finally 블록은 예외 발생 여부에 상관없이 항상 실행되는 코드 블록
- 주로 리소스 정리와 같은 작업을 수행할 때 사용

```python
try:
	# 예외가 발생할 가능성이 있는 코드
except 예외 타입:
	# 예외가 발생했을 때 실행할 코드
finally:
	# 예외 발생 여부와 상관없이 항상 실행할 코드
```

