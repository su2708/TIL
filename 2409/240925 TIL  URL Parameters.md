#TIL #스파르타코딩클럽 [[TIL]]

## 4.7 ~ 4.8 URL Parameters
### 1. URL의 특정 부분을 변수로 처리
- 프로그래머가 매번 변수에 맞게 라우터를 생성할 수는 없기 때문에 이를 변수로 처리
- `express`에게 변수임을 알려주기 위해 ':' 사용
- :id는 어떤 숫자나 문자열일 수 있으며, 요청이 들어왔을 때 req.params를 통해 접근 가능

```javascript
videoRouter.get("/:id/edit", (req, res) => {
  const videoId = req.params.id;
  res.send(`Video ID: ${videoId}`);  // video의 ID를 나타냄
});
```

### 2. 항상 정수로 받기 위한 인자 추가
```javascript
// in videoRouter.js
videoRouter.get("/upload", upload);
videoRouter.get("/:id(\\d+)", see);
videoRouter.get("/:id(\\d+)/edit", edit);
videoRouter.get("/:id(\\d+)/delete", deleteVideo);
```
- 기존에는 `/upload` 부분을 `/:id` 부분보다 무조건 위에 써야 했음
- id는 정수로만 받는다는 것을 알려줬으니  `/upload` 부분이 `/:id` 부분보다 밑에 있어도 무방