# Web Socket이란? 어떻게 동작할까?

### 웹 소켓

웹소켓은 단일 TCP 연결을 통해 **전이중 통신 채널을 제공하는 컴퓨터 통신 프로토콜입니다. 웹소켓 프로토콜은 IETF에 의해 2011년 RFC 6455로 표준화되었으며 Web IDL의 웹소켓 API는 W3C에 의해 표준화되고 있습니다.

웹소켓과 HTTP 두 프로토콜 모두 OSI 모델 7계층에 있으며 4계층인 TCP에 의존하지만 서로 다릅니다. 그렇지만 RFC 6455에 따르면 "웹소켓은 HTTP 포트 443 및 80을 통해 작동하고 HTTP 프록시 및 중개자를 지원하도록 설계되었다"라고 명시되어 있습니다.

호환성을 확보하기 위해 WebSocket 핸드 셰이크는 HTTP 업그레이드 헤더를 사용하여 HTTP 프로토콜에서 WebSocket 프로토콜로 변경합니다

**전이중 통신 : 동시에 양방향 전송이 가능한 방식으로 전송량이 많고, 전송매체의 용량이 클 때 사용한다.

### 웹 소켓 동작 원리

서버와 클라이언트 간의 웹소켓 연결은 HTTP 프로토콜을 통해 이루어집니다. 연결이 정상적으로 이루어진다면 서버와 클라이언트 간에 웹소켓 연결(TCP/IP 기반)이 이루어지고 일정 시간이 지나면 HTTP 연결은 자동으로 끊어집니다.

기본적으로 웹소켓 API는 아주 간단한 기능들만을 제공하기 때문에 대부분의 경우 SockJS나 [Socket.Io](http://socket.Io) 와 같은 오픈소스 라이브러리를 많이 사용하고 있으며 메세지 포맷 또한 STOMP 같은 프로토콜을 같이 이용합니다.

[참고 자료] 웹소켓 핸드쉐이킹

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcmqBIY%2FbtqKBOBCLJS%2FyKS7Ci7bq5DTki4DuJRlYk%2Fimg.png)

1. Opening Handshake

웹소켓 클라이언트에서 핸드쉐이크 요청(HTTP Upgrade)을 전송하고 이에 대한 응답으로 핸드쉐이크 응답을 받는데, 이 때 응답 코드는 101입니다. 101은 '프로토콜 전환'을 서버가 승인했음을 알리는 코드입니다.

웹 소켓을 여는 과정에서 주고받는 요청과 응답 헤더를 살펴보겠습니다. ws://localhost:8080/chat 으로 접속한다고 가정합니다.

```jsx
GET /chat HTTP/1.1
Host: localhost:8080
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
Origin: http://localhost:9000
```

- Upgrade : 프로토콜을 전환하기 위해 사용하는 헤더. 웹소켓 요청 시엔 반드시 websocket 이라는 값을 가지며, 이 값이 없거나 다른 값이면 cross-protocol attack 으로 간주하여 웹소켓 접속을 중지시킵니다.
- Connection : 현재의 전송이 완료된 후 네트워크 접속을 유지 할 것인가에 대한 정보. 웹소켓 요청 시에는 반드시 Upgrade라는 값을 가지며, Upgrade와 마찬가지로 이 값이 없거나 다른 값이면 웹소켓 접속을 중지시킵니다.
- Sec-WebSocket-Key : 유효한 요청인지 확인하기 위해 사용하는 키값
- Sec-WebSocket-Protocol : 사용하고자 하는 하나 이상의 웹소켓 프로토콜을 지정. 필요한 경우에만 사용
- Sec-WebSocket-Version : 클라이언트가 사용하고자 하는 웹소켓 프로토콜 버전. 현재 최신 버전 13(20.10.12 기준)
- Origin : 모든 브라우저는 보안을 위해 이 헤더를 보냅니다 (Cross-Site WebSocket Hijacking 과 같은 공격을 피하기 위해). 대부분 애플리케이션은 이 헤더가 없는 요청을 거부하며, 이러한 이유로 CORS 정책이 만들어졌습니다. 이 외에도 여러 메세지나 서브 프로토콜, Referer나 Cookie와 같은 공통 헤더, 인증 헤더 등을 추가해 보낼 수 있습니다.

```jsx
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
Sec-WebSocket-Protocol: chat
```

- Sec-WebSocket-Accept : 요청 헤더의 Sec-WebSocket-Key에 유니크 아이디를 더해서 SHA-1로 해싱한 후, base64로 인코딩한 결과. 웹소켓 연결이 개시되었음을 알립니다.

2. Data Transfer

핸드쉐이크를 통해 웹소켓 연결이 수립되면 데이터 전송 파트가 시작됩니다. 여기에는 클라이언트와 서버가 메세지라는 개념으로 데이터를 주고 받는데, 여기서 메세지를 한 개 이상의 프레임으로 구성되어 있습니다. (프레임은 텍스트, 데이터, 바이너리 데이터, 컨트롤 프레임 등이 있습니다)

핸드쉐이크가 끝난 시점부터 서버와 클라이언트는 서로가 살아있는지 확인하기 위해 heartbeat 패킷을 보내며, 주기적으로 ping을 보내 체크합니다. 이는 서버와 클라이언트 양측에서 설정이 가능합니다.

3. Close Handshake

클라이언트와 서버 모두 커넥션을 종료하기 위한 컨트롤 프레임을 전송할 수 있습니다. 이 컨트롤 프레임은 Closing Handshake를 시작하라는 특정한 컨트롤 시퀀스를 포함한 데이터를 가지고 있습니다. 위 그림에서는 서버가 커넥션을 종료한다는 프레임을 보내고, 클라이언트가 이에 대한 응답으로 Close 프레임을 전송합니다. 그러면 웹 소켓 연결이 종료됩니다. 연결 종료 이후에 수신되는 모든 추가적인 데이터는 버려집니다.

### 출처

- [https://en.wikipedia.org/wiki/WebSocket](https://en.wikipedia.org/wiki/WebSocket)
- [https://kellis.tistory.com/m/65?category=849606](https://kellis.tistory.com/m/65?category=849606)
- [https://choseongho93.tistory.com/266](https://choseongho93.tistory.com/266)