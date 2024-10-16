#TIL #스파르타코딩클럽 [[2409]]

## 5.4 Variables to Templates
- 템플릿에 변수로 받고 싶은 부분을 #{변수 이름}으로 한다.
- pug 파일을 렌더링 하는 부분은 컨트롤러이므로, 컨트롤러에서 템플릿으로 보낼 변수를 보낸다.
```javascript
// in base.pug
doctype html
html(lang="ko")
    head
        title #{pageTitle} | Wetube  // pageTitle 이라는 변수를 받음
    body
        block content
    include partials/footer.pug
```
```javascript
// in videoController.js
export const trending = (req, res) => res.render("home", { pageTitle: "Home" });
export const see = (req, res) => res.render("Watch", { pageTitle: "Watch" });
export const edit = (req, res) => res.render("Edit", { pageTitle: "Edit" });
```


## 5.6 MVP Styles
### 1. MVP.css
- 최소한의 스타일을 제공하는 CSS 프레임워크
- 간결하고 직관적인 디자인으로 웹 페이지를 자동으로 보기 좋게 구성
- 웹 페이지 디자인보다는 기능 구현에 집중하고 빠르게 프로토타입을 제작할 때 주로 사용
```pug
// in base.pug
doctype html
html(lang="ko")
    head
        title #{pageTitle} | Wetube
        link(rel="stylesheet" href="https://unpkg.com/mvp.css")  // mvp.css part
```




## 5.7 Conditionals
- pug에서 사용하는 조건문은 Javascript의 조건문과 비슷
- `if` `else if` `else` 키워드를 사용하여 조건에 따라 HTML을 동적으로 렌더링
- 조건문 안에서 변수를 활용하려 출력할 콘텐츠를 제어
- 들여쓰기를 통해 조건문 블록을 구분
```pug
// in base.pug
header
	if fakeUser.loggedIn
		small Hello #{fakeUser.username}
	nav
		ul
			if fakeUser.loggedIn
				li
					a(href="/logout") Log out
			else
				li
					a(href="/login") Login
	h1=pageTitle
```




## 5.8 Iteration
- pug에서 반복문은 `each` 키워드를 사용해 배열이나 객체를 순회
- `each item in items` 구문으로 배열 `items`의 각 요소를 `item` 변수로 접근
- `each` 뒤에 `else`를 붙이면 배열이 비어 있을 때 대체 콘텐츠를 표시
- 객체를 순회할 때는 `each value, key in object` 구문을 사용해 `key`와 `value`에 접근
```javascript
// in videoController.js
export const trending = (req, res) => {
	const videos = [1, 2, 3, 4, 5];
	return res.render("home", {pageTitle: "Home", videos});
};
```
```pug
// in home.pug
extends base.pug

block content
    h2 Welcome here you will see the trending videos
    ul
        each video in videos
            li=video
        else
            li Sorry nothing found.
```




## 5.9 Mixins
- mixin은 데이터를 받을 수 있는 partial을 말함
- Pug에서 Mixins은 재사용 가능한 코드 블록을 정의하는 데 사용
- `mixin` 키워드를 사용하여 함수를 정의하고, 해당 블록을 호출할 때 원하는 인자를 전달
- 인자를 전달하면 `#{}` 구문을 통해 해당 값이 삽입된 콘텐츠를 생성
- Mixin은 여러 곳에서 동일한 구조나 스타일을 반복하지 않고 효율적으로 사용 가능
- Mixin 내부에서 블록을 정의하고, 다른 콘텐츠로 대체할 수 있어 높은 유연성을 가짐
```javascript
// in videoController.js
export const trending = (req, res) => {
  const videos = [
    {
      title: "First Video",
      rating: 5,
      comments: 2,
      createdAt: "2 minutes ago",
      views: 59,
      id: 1,
    },
    {
        title: "Second Video",
        rating: 5,
        comments: 2,
        createdAt: "2 minutes ago",
        views: 59,
        id: 1,
      },
  ];

  return res.render("home", { pageTitle: "Home", videos });
};
```
```pug
// in home.pug
extends base.pug
include mixins/video  

block content
    h2 Welcome here you will see the trending videos
    //- videos from videoController.js
    each video in videos
        //- +mixin_name(object_name to send mixin)
        +video(video)
    else
        li Sorry nothing found.
```
```pug
// in mixins/video.pug

//- mixin mixin_name(received_object_name)
mixin video(video)
    div
        h4=video.title
        ul
            li #{video.rating}/5.
            li #{video.comments} comments.
            li Posted #{video.createdAt}
            li #{video.views} views.
```
