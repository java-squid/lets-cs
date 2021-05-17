# Cookie && Session

### Cookie

쿠키는 서버와 클라이언트 간 상태를 관리하는 유용한 기능입니다. 상태를 유지하지 않는 특징을 가진 통신 특성상 쿠키에 정보를 저장하고 서버와 클라이언트가 정보를 공유하여 이해할 수 있는 기능에는 로그인 유지와 같은 일들을 가능하게 합니다.

**쿠키를 위한 헤더 필드**

Set-Cookie (Response) : 상태 관리 개시를 위한 쿠키 정보

Cookie (Request) : 서버에서 수신한 쿠키 정보

### Set-Cookie

서버에서 클라이언트의 쿠키 환경에 담을 수 있는 헤더 필드 입니다.

- **Name=Value** : 쿠키에 부여된 이름과 값 (해당 필드는 필수)
- **Expires** : 쿠키의 유효 기간을 지정 (지정되지 않는 경우는 브라우저의 세션을 유지하는 기간만 적용)
- **Path** : 쿠키 적용 대상이 되는 서버 상의 디렉토리
- **Domain** : 쿠키 적용 대상이 되는 도메인 명
- **Secure** : HTTPS를 통신하는 경우만 쿠키 송신
- **HttpOnly** : JavaScript상에서 쿠키 값에 적용하지 못하도록 제한하는 필드

위와 같이 서버에서 여러가지 속성을 통해서 쿠키 정보를 설정한 후 전달이 가능하고 클라이언트도 역시 JavaScript에서 쿠키를 담을 수 있습니다.

### 쿠키의 한계

유용하게만 보인 쿠키에는 몇 가지 제약적인 사항이 존재합니다.

- 4KB의 크기를 넘을 수 없습니다. 때문에 방대한 양의 데이터를 쿠키에 저장하는 일은 불가능 합니다.
- 도메인마다 다르지만 쿠키의 개수는 20개로 한정됩니다.

### Session

웹브라우저에는 정보를 저장할 수 있는 Storage가 존재하며 session과 local로 분류할 수 있습니다.
두 저장 공간은 각각 아래와 같은 차이를 가집니다.

**localStorage** : 페이지 세션을 종료해도 데이터를 만료하지 않는다.

**sessionStorage** : 페이지 세션이 종료되는 시점에 제거된다.

또한 sessionStorage에 저장된 자료는 페이지에 해당하는 프로토콜별로 구분합니다.

`http://my-web.com , [https://my-web.com](https://my-web.com) 이 둘은 서로 다른 저장 공간에 저장 !`

### 참고 링크

만화로 배우는 HTTP & Network basic

[https://ko.javascript.info/cookie](https://ko.javascript.info/cookie)

[https://developer.mozilla.org/ko/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API](https://developer.mozilla.org/ko/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API)
