# websocket이란?
- TCP 접속 중에, 쌍방향 통신 채널을 제공하도록 하는 통신 프로토콜을 의미
- 보통 HTTP(80, 443) 위에서 동작하도록 설계되어 있음.

## 등장 배경
- 양방향 통신의 필요성 대두
- 클라이언트가 요청을 보내면, 응답하는 전형적인 HTTP 프로토콜 방식의 한계 (화면 깜박임등..)
- 이를 해결하기 위해, Polling, Long Polling, Streaming 등의 방식이 나왔지만..
    - 기본적인 단방향 메세지 교환 규칙의 한계를 벗어날 순 없었음.
    - 즉 진정한 의미에서 양방향, 상호교환 작용하는 웹이 필요해졌음.
- cf) Polling, Long Polling, Steaming 작동방식 참고
    - http://www.secmem.org/blog/2019/08/17/websocket-socketio/


## websocekt 등장
![](https://d2.naver.com/content/images/2015/06/helloworld-1336-1-1.png)
- 클라이언트와 서버와 간의 실시간 통신을 가능토록 해줌
    - 클라이언트 요청에 의해서. 서버가 응답을 주는 방식이 아닌, 서버가 데이터를 클라이언트에게 보내는 방식을 채택

## websocket protocol
```
GET /... HTTP/1.1  
Upgrade: WebSocket  
Connection: Upgrade  
```
- 헤더에 `Upgrade`항목을 붙여서, 서버에 요청함.
- 웹 서버에서 websocket 기능을 지원할 경우,
    1. 브라우저는 키를 랜덤 생성
    2. 브라우저는 upgrade 헤더 항목과 함께 키를 서버에 보냄
    3. 서버는 브라우저가 전송한 키를 바탕으로 토큰 생성
    4. 서버는 토큰을 브라우저에게 돌려줌
    5. 이제 서버 -- 브라우저는 Websocket 프로토콜을 기반으로 실시간 통신을 할 수있도록 연결되었다고 볼 수 있음.


# socekt.io 이란?
- Javascript 를 이용해서, 브라우저 종류와 상관없이 실시간 통신을 할 수 있도록 지원하는 라이브러리
- 어떻게 여러 브라우저에서 사용할 수 있을까?
    - 이용자는 하나의 API 만 사용하도록 유도하고, 해당 api 내부에서 이용자의 브라우저, 웹서버를 파악하여 가장 적절한 기술을 선택해서 사용하도록 설계되어있음.
- https://d2.naver.com/helloworld/1336 에서, 간단하게 테스트해볼 수 있다!


# websocket vs socekt.io
- 

# 참고
- https://ko.wikipedia.org/wiki/%EC%9B%B9%EC%86%8C%EC%BC%93
- https://d2.naver.com/helloworld/1336
- https://socket.io/