# CORS란?

- Cross-Origin Resource Sharing 정책을 의미함.
- 왜 사용할까?
  - 가져오는 리소스의 최소한의 안전성을 보장하기 위해.
  - 안정성은, 악의적인 목적을 가진 리소스는 아니다를 보장하기 위해.



## Origin이란?

![](https://evan-moon.github.io/static/e25190005d12938c253cc72ca06777b1/6af66/uri-structure.png)

- `origin` 이 같다는 건, `Protocol` + `Host` + `Port`  가 같음을 의미한다.
  - 보통 web 주소에서 포트번호가 주어지지 않으면 80 임.
- 이 세가지 중에 하나만 달라도, 다른 `Origin` 으로 판단된다.
  - 즉 `CORS` 오류가 발생한다.
  - 다른 `Origin` 에서 가져오는 리소스 이므로, 안정성을 보장할 수 없다 라는 취지의 오류가 발생한다는 의미인듯.



# Same - Origin Policy

- 리소스 요청을 제한하는 두 가지 정책
  - CORS
  - SOP

- SOP?
  -  [RFC 6454](https://tools.ietf.org/html/rfc6454#page-5) 에서 처음 등장한 정책
  - **같은 Origin의 리소스만 공유할 수 있음**을 명시한 규칙
  - 다른 출처의 리소스에 접근할 수 있는 몇가지 예외 조항이 있음
    1. CORS 정책을 지킨 요청
    2. 실행가능한 스크립트, 렌더될 이미지, 스타일 시트 요청.

- 2번에 해당하는 자원을 요청한다면..

  ```html
  <img src="https://evanmoon.tistory.com/rss">
  <script src="https://evanmoon.tistory.com/rss"></script>
  ```

  - CORS 위반하지 않고 요청 자체는 성공.
  - 해당 요청 헤더들에 `Sec-Fetch-Mode: no-cors` 가 포함되어 있음.
  - 이 헤더가 있으면, 브라우저는 해당 필드값이 `no-cors` 인 경우에 다른 출처여도 CORS 위반 여부를 검사하지 않는다.
  - 그러면 이 헤더값을 이용해서 CORS 정책을 우회할 수 있지 않을까?
    - 못함.
    - 브라우저는 이 헤더값을 이용해서 온 응답에 대해 javascript에서 사용하지 못하도록 해놨음



- SOP를 지키려면 많은 어려움이 있다.
  - 웹 환경에서는 다른 Origin으로 부터 자원을 불러오는 이유는 흔하다. 즉 이용상 불편함이 많음
  - 그래서 CORS가 등장. 
    - 최소한의 원칙 (Origin) 을 어길 수 있으려면, **요청과 응답 헤더에 적절한 CORS에 대한 정보**가 있어야함.
- Same Origin 예시

![image](https://user-images.githubusercontent.com/22140570/129508965-f4021eee-2492-4996-87bb-737d78c62774.png)

- 위에서도 언급했듯이, URL 구성요소 중  `Scheme`, `Host`, `Port` 3가지로 판단.
- 또한 출처를 비교하는 것은 **브라우저에서 구현된 스펙**(서버가 아니다)
  - 어떠한 리소스 요청이(CORS 정책을 위반하는) 서버에게 가는 데, 해당 서버가 같은 출처에서만 보낸 요청을 받겠다는 로직이 있는 경우가 아니라면, 서버는 **정상적으로 응답**을 함
  - 그렇지만 브라우저가 받은 응답을 분석해서, **CORS 정책 위반이라 판단**(Same-Origin이 아니면..)되면 그 응답을 사용하지 않고 버린다.
- 다시 말하자면, CORS는 **브라우저의 구현 스펙에 포함되는 정책**이기에, 브라우저를 통하지 않고 서버간 통신을 할 때는 이 정책이 적용되지 않는다.
  - 즉 브라우저 - 서버 통신 시에, CORS 오류가 발생했더라도, 서버쪽 로그는 정상 응답을 했다고 남는다.





# CORS 어떻게 동작할까?

- 요청
  - HTTP 프로토콜을 사용해서 리소스 사용 요청을 보내게 되는데, **요청 헤더의 Orgin 이라는 필드**에 출처를 함께 담아서 보냄.
  - `Origin: https://evan-moon.github.io`
- 응답
  - 서버가 이 요청에 대한 응답을 내려줄 때, **Response Header에 Access-Control-Allow-Origin** 이라는 값에, 응답 리소스에 접근할 수 있도록 허용한 Origin을 내려줌.
- 브라우저
  - 응답 받은 브라우저는 자신이 보낸 request header Origin 과 서버가 보내준 response의 Access-Control-Allow-Origin 을 비교해본다음에, 유효한 응답인지 아닌지를 **판단**한다.
  - 최종 판단은 브라우저가!



## Preflight Request

- 예비요청을 의미함.
- 일반적으로 개발할 대 가장 많이 마주치는 시나리오
- 이 상황일 때는, 브라우저는 요청을 한번에 보내지 않고, **예비요청** + **본 요청**으로 나눠서 서버로 전송.
  - HTTP method 중에 `OPTIONS` 가 사용된다.
- 예비요청의 역할은, 브라우저 스스로... 해당 요청(본 요청)을 보내기 전에 미리 보내서, 안전한 요청(CORS를 위한반 요청이 아닌지 등..) 인지 확인하는 듯함.

![](https://evan-moon.github.io/static/c86699252752391939dc68f8f9a860bf/6af66/cors-preflight.png)

- Preflight request example

  ```http
  OPTIONS https://evanmoon.tistory.com/rss
  
  Accept: */*
  Accept-Encoding: gzip, deflate, br
  Accept-Language: en-US,en;q=0.9,ko;q=0.8,ja;q=0.7,la;q=0.6
  Access-Control-Request-Headers: content-type
  Access-Control-Request-Method: GET
  Connection: keep-alive
  Host: evanmoon.tistory.com
  Origin: https://evan-moon.github.io
  Referer: https://evan-moon.github.io/2020/05/21/about-cors/
  Sec-Fetch-Dest: empty
  Sec-Fetch-Mode: cors
  Sec-Fetch-Site: cross-site
  ```

  - 이러한 옵션 요청에는 본 요청에 대한 정보도 포함되어있음.
  - `Access-Control-Request-Headers` 는 본 요청에서 사용할 `Content-Type` 헤더를 알려주거나
  - `Access-Control-Request-Method` 를 이용해서 본 요청에서 사용할 메서드가 무엇인지 서버에게 미리 알려주고 있는 것.

- Server response

  ```http
  OPTIONS https://evanmoon.tistory.com/rss 200 OK
  
  Access-Control-Allow-Origin: https://evanmoon.tistory.com
  Content-Encoding: gzip
  Content-Length: 699
  Content-Type: text/xml; charset=utf-8
  Date: Sun, 24 May 2020 11:52:33 GMT
  P3P: CP='ALL DSP COR MON LAW OUR LEG DEL'
  Server: Apache
  Vary: Accept-Encoding
  X-UA-Compatible: IE=Edge
  ```

  - 응답 부분에서 `Access-Control-Allow-Origin`  은 요청과 같음. (Same-Origin 이구나..)

    - 즉 해당 서버에서 이 리소스에 접근할 수 있는 `Origin` 은 위 자원이라고 브라우저에게 이야기 해줌.

  - 다른 출저에서 이 리소스를 요청할 경우, `CORS` 오류가 발생함을 알게 될 것.

  - 예상 에러

    ```text
    🚨 Access to fetch at ‘https://evanmoon.tistory.com/rss’ from origin ‘https://evan-moon.github.io’ has been blocked by CORS policy: 
    Response to preflight request doesn’t pass access control check: The ‘Access-Control-Allow-Origin’ header has a value ‘http://evanmoon.tistory.com’ that is not equal to the supplied origin. 
    Have the server send the header with a valid value, or, if an opaque response serves your needs, set the request’s mode to ‘no-cors’ to fetch the resource with CORS disabled.
    ```

- 정리

  - CORS 정책 위반과, 응답 성공 여부는 상관 없음
  - CORS 정책 위반이어도 `200` 이 나올 수 있음.
  - 중요한 것은 예비 요청의 성공/실패 여부가 아니라, 서버에서 온 응답 헤더에 유효한 `Access-Control-Allow-Origin` 이 있는 지 임.



## Simple Request

- [단순 요청](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS#%EC%A0%91%EA%B7%BC_%EC%A0%9C%EC%96%B4_%EC%8B%9C%EB%82%98%EB%A6%AC%EC%98%A4_%EC%98%88%EC%A0%9C)
- 예비 요청(Preflight) 을 보내지 않고, 서버에게 본 요청을 보낸 후에, 서버에서 온 응답 헤더에 `Access-Control-Allow-Origin` 을 확인하여, 브라우저가 **CORS 정책 위반 여부**를 검사하는 방식

![](https://evan-moon.github.io/static/d8ed6519e305c807c687032ff61240f8/6af66/simple-request.png)

- 아무때나 사용할 수 있는 것이 아닌, 특정 조건을 만족하는 경우에만 예비 요청을 생략(단순 요청)하고 보낼 수 있음.

  1. 요청의 메소드는 `GET`, `HEAD`, `POST` 중 하나여야 한다.

  2. `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, `DPR`, `Downlink`, `Save-Data`, `Viewport-Width`, `Width`를 제외한 헤더를 사용하면 안된다.

  3. 만약 `Content-Type`를 사용하는 경우에는 `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`만 허용된다.

- 그렇지만, 이러한 경우는 거의 없다고 보면 된다.



## Credentialed Request

- 인증된 요청을 사용하는 방법.
- 다른 출처 간 통신에서, 좀 더 보안을 강화하고 싶을 때 사용하는 방법임.
- 기본적으로 브라우저가 제공하는 비동기 리소스 요청 API는 XMLHttpRequest 객체나 fetch API는 별도의 옵션 없이 브라우저의 쿠키 정보나, 인증과 관련된 헤더를 함부로 요청에 담지 않음.
  - 이 때 요청에 인증과 관련된 정보를 담을 수 있도록 해주는 것이 **credentials 옵션**임.
- 이 옵션에는 3가지 값이 있음.

![image](https://user-images.githubusercontent.com/22140570/129509948-efc20cc3-7547-443e-befc-01c41ec1df54.png)



- `incldue`

  - 이 옵션을 사용해서 리소스 요청에 인증 정보가 포함된다면, 
  - 브라우저는 다른 출처 리소스를 요청할 때 단순히 `Access-Control-Allow-Origin` 만 확인하는 것이 아닌 **뭔가 더 확인하는 과정**을 거치게 됨

  - 요청 (fetch api 이용)

    ```javascript
    fetch('https://evan-moon.github.io/feed.xml', {
      credentials: 'include',...
    });
    ```

  - 응답

    ```text
    🚨 Access to fetch at ’https://evan-moon.github.io/feed.xml’ from origin 
    ’http://localhost:8000’ has been blocked by CORS policy: 
    The value of the ‘Access-Control-Allow-Origin’ header in the response must not be the wildcard ’*’ 
    when the request’s credentials mode is ‘include’.
    ```

    - 브라우저가 알려주고 있음.
    - credential option이 `include` 일 경우(모든 요청에 인증정보가 담길 경우), 
    - 모든 요청을 허용하는 `*` 를 `Access-Control-Allow-Origin` 헤더에 사용하면 안된다고 알려주는 오류임. (모든 요청을 허용하지 말라는 의미인듯. 보안적으로 좋지 않으므로..)
    - 즉 모든 요청에 인증 정보가 담겨있는 상태에서, 다른 출처 리소스를 요청하게 되면 브라우저는 CORS 위반을 검사하는 룰에 밑에 두가지를 추가하게됨.
      1. 서버 응답 헤더의 `Access-Control-Allow-Origin`에는 *를 사용할 수 없으며, 명시적인 URL이어야 함.
      2. 서버 응답 헤더에는 반드시 `Allow-Control-Allow-Credentials: true`가 존재해야함.



# CORS 해결



## Access-Control-Allow-Origin 셋팅하기

- 서버에서 `Access-Control-Allow-Origin` 에 알맞은 값을 세팅해주는 것.
  - 단`*` 를 사용하는 것은, 이상한 출처에서 오는 요청까지 모두 허용하겠다는 의미로 심각한 보안 이슈가 발생할 수 있음.
  - 그래서 가급적이면 어떠한 출처에서 오는 요청을 허용할지 명시해주는 것이 좋다.
- 그리고 Nginx, Apache같은 Web Server에서 허용해 줄수도 있지만, 불편하기 때문에 소스코드 내 미들웨어 등에서 위 같은 세팅하는 것을 추천.
  - 예시 : Spring —> Cors Mapping 설정 해주는 것.



## 리버스 프록싱

- nginx등에서 제공하는 proxy 기능을 이용하여, 브라우저에게 마치 CORS 정책을 지키고 있는 것처럼 알려주는 방법
- webpack에서도 이러한 기능을 제공해 줄 수 있는듯.
- 즉  브라우저 요청 (`:80/api/me`) -> 리버스 프록싱 (`:80/server/api/me`) -> 서버 (`:8080/server/..`)
  - 실제로 보면, CORS 정책을 어긴 것이지만,
  - 중간에 Origin을 바꿔서 요청함으로써, 브라우저가 최종적으로 판단하기에는 CORS 어기지 않을 것 처럼 된다.

# 참고

- https://evan-moon.github.io/2020/05/21/about-cors/
- https://velog.io/@jesop/SOP%EC%99%80-CORS
- https://www.nginx.com/resources/glossary/reverse-proxy-server/