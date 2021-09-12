# CSRF

- Cross Site Request Forgery
- 사이트 간 요청 위조
  - 요청을 보내는 데, 그 요청이 정상적인 요청이 아니다.
  - 뭔가 목적을 위해서, 요청을 위조해서 보냈음.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcnluOR%2FbtqAslpDWKV%2FjUdwVwOTxZUL1qMA5jGCd1%2Fimg.png)

- 사용자 (희생자) 가 자신의 의지와는 무관하게 특정 웹 사이트를 공격하게 만드는 것.
  - 즉 공격 목표인 웹 서버 - 희생자 클라이언트 브라우저의 간에 신뢰상태에 있는 것을 기반으로
  - 희생자는 정상적인 서비스를 이용하지만,
  - 그 내부적인 요청에는 공격자가 원하는 요청(희생자는 원하지 않는 요청)까지 포함되어 있음을 의미한다.
- 해당 공격은 유저 (희생자) 가 로그인 상태이어야만 유의미한 공격이 된다.



## 어떻게 하면 막을 수 있을까?

1. Referer 체크
   - 해당 요청이 신뢰할 만한 페이지에서 발생한 것인지 확인.
   - 이 또한 조작할 수 있음.
2. 토큰 발급
   - 로그한 유저에게만 토큰을 발급해서, 요청 시에 해당 토큰도 함께 보내도록
   - 이 토큰이 유효할 때만, 해당 요청을 처리하도록..
3. Spring Security
   - https://zzang9ha.tistory.com/341

# XSS

- Cross site scripting

- 공격자는 해당 사이트에 접속하는 사용자로 하여금 원하지 않는 scripting(malicious code)을 실행시키게 만드는 것을 의미.

  - 이를 통해, 쿠키, 세션등에 담긴 정보등을 탈취할 수 있음.
  - 혹은 사이트 이용자로 하여금 원하지 않는 동작 을 하게 만들거나..

- 보통 이러한 공격으로 인한 결과가, CSRF 취약점을 통한 결과보다 심각할 경우가 많음.

- 3가지 타입이 존재한다.

  - **Stored XSS**, **Reflected XSS,** and **DOM-based XSS**
  - 스크립트를 저장해놓거나, (Stored XSS)
  - URL 변수 부분에 입력하거나 .. 특정결과를 바로 노출시키거나 (Reflected XSS)
  - URL 변수 입력을 통해서, HTM DOM 파싱에서 문제를 일으키거나.. (DOM-based XSS)
  
## 보통 어떻게 동작할까?

- API 호출에, script를 삽입

  ```
  https://example.com/search?query=<script>alert(1)</script>
  ```



- 아래 사진과 같은 결과가 나올 수 있음 (제대로 방어가 안되어있으면)

![](https://miro.medium.com/max/626/0*m9RzWEjGqOK-rNk9.png)






## 어떻게 하면 막을 수 있을까?

  1. Database의 유저가 작성한 값이 저장될 때, 검증하도록 하는 방법
     - script와 비슷한 구조를 모두 필터링하거나..
     - 일부 태그만 허용하는 방식
  2. Automatic HTML escaping

# 참고

- https://program-developer.tistory.com/99
- https://lucete1230-cyberpolice.tistory.com/23
- https://portswigger.net/web-security/csrf/xss-vs-csrf\
- https://medium.com/humanscape-tech/xss%EC%99%80-csrf-fe0e219b4c38