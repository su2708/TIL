#TIL #스파르타코딩클럽 [[TIL]]

## Server  
- 서버는 인터넷에 연결되어 있으며 24시간 꺼지지 않는 컴퓨터  
- 서버는 클라이언트에서 보낸 request 를 받고 response 보냄

## Request, Response
- 클라이언트와 서버는 개방된 포트를 통해 request 와 response 를 송수신
- 서버는 request 를 받으면 반드시 response 를 송신
- HTTP request 는 어떤 route(url) 에 대한 HTTP Method 요청이고 서버는 그 요청에 대한 response 를 송신
  
## Controller  
- 컨트롤러는 전달받은 request를 처리하고 response를 전달하기 위한 콜백함수
- 컨트롤러는 request 와 response 오브젝트를 수신
  
## Middleware  
- 미들웨어는 컨트롤러가 request 처리 작업을 완료하고 response 를 전달하기 전에 request 처리를 도와주는 콜백함수
- 모든 컨트롤러는 미들웨어가 될 수 있고, 모든 미들웨어는 컨트롤러가 될 수 있음
- 미들웨어는 request 오브젝트와, response 오브젝트 말고도 next 파라미터를 가짐
- next 파라미터는 다음으로 request 를 처리할 콜백함수를 담고있음

## Morgan
-  node.js 서버로 구성된 웹 환경에서 HTTP request 로그를 관리하기 위한 미들웨어
- 많이 사용하는 로그 관리 미들웨어를 정교하게 만들어 놓은 것
- combined, common, dev, short, tiny의 5가지 옵션이 있음