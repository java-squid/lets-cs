# CSRF XSS 공격
## CSRF: Cross-Site Request Forgery
* 사이트간 요청 위조라는 뜻인데, Cross-Origin 정책을 우회해야 공격에 성공하니 해당 정책을 우회하는 특징을 가지고 있다.
* CSRF는 **사용자가** 의도하지 않은 행위를 취하도록 만드는 공격이다.
* CSRF는 "한 쪽으로만" 공격하는 행위인데, 왜냐면 공격자가 피해 PC로 HTTP 요청을 보내게 유도할 수는 있지만 공격자가 "요청에 대한 응답을 받지는" 못하기 때문이다.
* 브라우저가 HTTP Request를 보낼 때 쿠키 안에 Session 값을 기본적으로 넣기 때문에 해당 공격이 가능하다.
    * 쿠키에 세션 id나 accessToken을 인증에 이용하는 구조에 CSRF 취약점이 있다면 해커는 CSRF 공격으로 API 콜 요청시 자동으로 인증 정보가 쿠키에 담겨 서버로 보내진다. 공격자는 유저 권한으로 정보를 가져오거나 액션을 수행할 수 있다.

### 방어하는 방법
* localstorage나 쿠키에 access token을 저장하지 말고 다른 곳에 보관한다. 요청에 보내는 쿠키에는 refresh token을 받아와서 새로운 access token을 가져온다. (secure httpOnly의 경우에도 마찬가지)
* Referrer를 검증한다. 지금 접속한 페이지가 어느 곳에서 왔는지를 검증하면 기초적인 CSRF 공격은 다 방어할 수 있게 된다.
* Spring Security CSRF 토큰 활용한다. 세션에 난수 값을 저장하고 이를 타임리프 form에다가 hidden 값으로 보낸다. 사용자가 request를 보낼 때 난수 값이 맞지 않으면 40X 에러코드를 뿝어낸다. [참고](https://velog.io/@max9106/Spring-Security-csrf)

## XSS: Cross Site Scripting
* 서버 측에서 제공되는 스크립트가 아니라, 임의의 공격자가 웹사이트에 자기 스크립트 올려서 그걸 사용자가 실행하게 해서 피해보도록 한다.
* 그러니까 서버와는 관련이 없고 **클라이언트**와 관련이 있다.
### Stored XSS
* 게시판에다가 자기 스크립트 올리고 사용자가 그걸 클릭해서 피해를 볼 수 있으면 저장형 XSS 공격이다. 게시물을 클릭하면 스크립트가 실행되어 사용자의 쿠키가 털리는 경우이다. [참고](https://rjswn0315.tistory.com/175)
### Reflected XSS
* 마치 "반사" 된다고 표현하는 데, 사용자가 공격자가 걸어놓은 악성 링크를 타고 들어가면 공격 스크립트 코드가 "삽입된" 사이트로 이동하게 되고 입력된 스크립트가 반사되어 실행한다.
* 예를 들어 User 이름을 적는 form이 있는 사이트로 접속한다고 했을 때, 해당 form에다가 미리 script를 적어놓은 악성 링크로 피싱하여 접속한다고 쳤을 때, 악성 링크를 클릭할 경우 마치 이름을 적는 form 사이트 인 것처럼 보이지만 실제로는 해커가 심어놓은 스크립트가 실행되는 것이다. [참고](https://rjswn0315.tistory.com/176)
### DOM-based XSS
* URL에서 #이후에 입력받은 값은 서버측에 전송되지 않는점을 이용하여 공격하는 방식. # 뒤에 스크립트를 넣는다.
* 해당 방식은 서버측에서 Script를 필터링할 수 없다. [참고](https://www.acunetix.com/blog/articles/dom-xss-explained/)
[참고 2](https://ddungkill.tistory.com/135)

## 대응 방안
* XSS 공격이 무서운 이유는 localstorage 안에서는 쿠키의 모든 정보를 접근하고 편집하고 삭제할 수 있기 때문이다.
* Cookie에서 HttpOnly 옵션을 설정하여 HTTP용 통신에서만 쿠키 정보에 접근하도록 만들고 JavaScript에서는 가져올 수 없게 설정할 수 있다. 그런데 HttpOnly property를 켜놓게 되면 SPA 환경에서 AJAX 통신을 할 수 없다는 단점이 존재한다. [참고3](https://jhyuxxk.tistory.com/7)
* 사용자로부터 받은 신뢰할 수 없는 입력의 경우 Espace 과정을 거친다. HTML Body 등을 만났을 때 실행하지 않고 Escape한다는 의미이다. [참고](https://onlydev.tistory.com/78)
    * Escape: 특정 문자를 원래의 기능에서 벗어나게 변환하는 행위
    * 참고로, 이전에 페이스북이 Stored XSS 버그 바운티가 있었는데, 해시태그가 SNS에서 많이 쓰다보니 #을 encoding하지 않기로 했는데 그러다보니 #을 URL 바로 뒤에다가 붙여서 해당 링크로 forwarding할 수 있게 하는 버그였다. [참고](https://www.hahwul.com/2018/03/21/hacking-hsah-tag-xss-escape-quot-with/)
* Double Submit Cookies라는 방법도 있다고 한다. Same Origin Policy에 따라 도메인이 다르면 이전 도메인의 쿠키 값에 접근하지 못하도록 한다. 이를 이용하여 스크립트 단에서 요청시 난수를 생성하고 이를 쿠키에 저장한다. 동일한 난수 값을 요청 파라미터에 저장하여 서버에 전송했을 때 두 난수 값이 서로 일치하면 올바른 요청이라고 판단하고 아니라면 기각한다. 서버에서 토큰값을 저장할 필요가 없어 세션 검증보다 가볍다. [링크](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
