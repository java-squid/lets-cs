# HTTP/1.1

- 클라이언트 컴퓨터와, 웹 서버에서 정보를 교환할 떄 사용하는, top-level application protocol임
- 클라이언트 `GET`, `POST` 라고 불리는 메서드를 text-basted request에 담아 서버에 보냄.
  - 그러면 서버는 요청에 따른 리소스를 전달해줌.

- 예시

  ```http
  GET /index.html HTTP/1.1
  Host: www.example.com
  ```

  - `GET` 메서드

  - `www.example.com` 이라는 호스트에 `/index.html` 라는 자원을 요청하는 request

  - 만약 이 html에 포함된 image, stylesheet등의 다른 자원이 있다면, 첫번째 요청에 **모든 자원들이 전달되는 것은 아니다.**

  - 브라우저에서 필요할 때, 다시 서버에 요청하게 됨.![](https://assets.digitalocean.com/articles/cart_63893/Protocol_Stack.png)

- 느리다.. 왜?

  - 헤더가 큼 -> 많은 메타 정보들이 들어가 있음. 특히 쿠키!
  - HOL(Head Of Line) Blocking (특정 응답 지연)
    - 클라이언트 ,서버 응답 순서가 동기화되어야하기 때문에, 어느 한곳에서 응답이 늦어지면.. 문제가 생김
  - RTT(Round Trip TIme) 증가 (양방향 지연)



### Pipelining and Head-of-Line Blocking

- 클라이언트에서 온 `GET` 메서드에 대해 서버가 응답을 보냈을 경우, 하나의 페이지를 제대로 그려서 사용자에게 보여주지 못함.
  - 즉 리소스(image, style sheet)에 대한 추가적인 요청을 보내야함..
- 만약 어떤 리소스에 대한 요청을 회수할 수 없는 상황이라면, 그 자체가 추후 진행되는 모든 리퀘스트를 막는 현상이 발생함.



# HTTP/2.0

- 서버, 클라이언트간의 메세지를 주고받을 떄 사용하는 프로토콜임.

- 그러나 HTTP 1.1을 이용했을 경우, 발생되는 문제(웹 페이지가 로드 될 때 생기는 지연)를 최소화하기 위해 등장

- binary framing layer임!

  - HTTP 1.1과 가장 구별되는 차이점
  - 즉 text로 주고받는 1.1에 비해서, 2.0은 binary 값으로 원하고자 하는 데이터를 주고 받을 수 있음
- 하나의 TCP connection에서, 여러개의 데이터를 주고 받을 수 있게함.

  

### Advantages of the Binary Framing Layer

- 여러개의 데이터 스트림 환경을 두 기기간에 만듬.
- **Multiplexing!**
- 각각의 스트림들은 여러개의 메세지를 가질 수 있는데, 이 메세지의 포멧이 http request/response 형태임
  - 이 메세지들은 frames라는 단위로 나뉘어질 수 있음.
- 즉 하나의 커넥션에 여러개의 데이터를 주고 받을 수 있다는 장점이 있음.

![](https://assets.digitalocean.com/articles/cart_63893/Streams_Frames.png)



### Stream Prioritization

- 요청 리소스간의 의존 관계를 설정해놓음
  - 왜?
  - 여러 request들이 하나의 리소스를 얻기 위해 경쟁함으로써 생기는 성능 이슈를 방지하고,
  - 개발자로 하여금 request의 우선순위를 정하게 하여, 좀 더 나의 어플리케이션 성능을 제공하기 위해



### Server push

- 클라이언트는, html 자원을 요청했어도, 서버쪽에서 이에 필요한 자원들을 미리 보내주는 것을 의미.
  - 클라이언트는 받은 자원을 캐시할 수도, 혹은 거부할 수도 있음.



### Compression

- binary framing layer를 사용하고
- HPACK 알고리즘을 이용하여, 헤더를 압축함. 
  - 이를 통해 상당히 사이즈를 줄일 수 있음.
- 이 과정을 통해 서버-클라이언트 통신 과정에서 발생할 수 있는 지연을 줄일 수 있음.




# HTTP 1.1 vs 2.0

![](https://miro.medium.com/max/1328/1*rf2AnDQyHfGO_ThYfb-hWA.png)



# 참고

- https://medium.com/@shlee1353/http1-1-vs-http2-0-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EA%B0%84%EB%8B%A8%ED%9E%88-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0-5727b7499b78
- https://www.digitalocean.com/community/tutorials/http-1-1-vs-http-2-what-s-the-difference
- https://goldfishhead.tistory.com/26