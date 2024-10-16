#TIL #스파르타코딩클럽 [[2409]]

## 4.0 What are Routers?
### 1. 라우터
- 작업중인 주제를 기반으로 컨트롤러나 url를 그룹화 해 관리를 쉽게 해줌


## 4.1 Making Our Routers

```Node.js
const globalRouter = express.Router();
const userRouter = express.Router();
const videoRouter = express.Router();
  
const handleHome = (req, res) => res.send("Home");
const handleEditUser = (req, res) => res.send("Edit User");
const handleWatchVideo = (req, res) => res.send("Watch Video");

globalRouter.get("/", handleHome);            // handle ".../" url
userRouter.get("/edit", handleEditUser);      // handle ".../users/edit" url
videoRouter.get("/watch", handleWatchVideo);  // handle ".../videos/watch" url

app.use("/", globalRouter);      // "/"로 시작하는 url이면 globalRouter로 이동
app.use("/users", userRouter);   // "/users"로 시작하는 url이면 userRouter로 이동
app.use("/videos", videoRouter); // "/videos"로 시작하는 url이면 vodioRouter로 이동
```



## 4.2 Cleaning the Code
### 1. Node.js에서 모든 폴더 및 파일은 서로에게 영향을 주지 않고 독립적임
### 2. 따라서 다른 파일의 코드를 사용하려면 해당 내용을 import 해야함
### 3. import 하려는 파일은 그 파일에서 export default를 설정해야함

```javascript
// in ./routers/globalRouter.js
import express from "express";

const globalRouter = express.Router();
const handleHome = (req, res) => res.send("Home");
globalRouter.get("/", handleHome);

export default globalRouter;

--------------------------------
// in ./server.js
import globalRouter from "./routers/globalRouter.js";
```



## 4.3 Exports
### 1. export 하고 싶은 함수가 여러 개인 경우
- 함수 선언 앞에 `export` 붙여주기

```javascript
// in videoController.js
export const watch = (req, res) => res.send("Watch");
export const edit = (req, res) => res.send("Edit");

-----------------------------
// in videoRouter.js
import { watch, edit } from "../controllers/videoController";
```

