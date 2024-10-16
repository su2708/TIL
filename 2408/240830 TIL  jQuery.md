#TIL #알고리즘 #스파르타코딩클럽 [[2408]]

## 1. jQuery

### 1) jQuery란?

- JavaScript로도 모든 기능을 구현할 수 있지만, 코드가 복잡하고 브라우저 간 호환성 문제도 고려해야함
- 이를 해결하기 위해 jQuery 라는 라이브러리 등장
- jQuery를 사용하기 위해서는 임포트 필요

### 2) jQuery 사용하기

1. jQuery 임포트하기  
    아래의 주소에서 jQuery CDN 부분을 참고하여 복사

```html
https://www.w3schools.com/jquery/jquery_get_started.asp
```

2. < head>와 </ head> 사이에 넣으면 끝

```html
<head>
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.4/jquery.min.js"></script>
</head>
```

3. jQuery를 사용하는 방법

- css와 마찬가지로 "가리켜야" 조작 가능 (선택자를 사용)
- jQuery에서는 id 값을 통해 특정 버튼 / div / p / ... 등을 가리킴

### 3) jQuery 예시

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simple jQuery Example</title>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script> // jQuery 임포트
</head>
<body>

<h1 id="myHeading">Hello, World!</h1>
<button id="changeText">Change Text</button>

<script>
  	// Change Text 버튼을 눌렀을 때 Hello, World!가 Hello, jQuery!로 변경되도록 하는 함수
    $(document).ready(function() {
        $('#changeText').click(function() {
            $('#myHeading').text('Hello, jQuery!'); // 텍스트 변경
        });
    });
</script>

</body>
</html>
```