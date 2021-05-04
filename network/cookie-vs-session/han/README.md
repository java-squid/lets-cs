# Cookie
- text file, plan text
- 클라이언트(웹 브라우저등..)에 저장됨
- Key - Value의 구조로 저장됨.
- 클라이언트 사이드에 저장되기 때문에 그리 안전하지 못함.
    - 즉 쿠키 정보를 수정하여, 잘못된 요청을 줄 수 있음.
- 클라이언트에서 받는 요청의 Header에 `Set-cookie` 가 있을 경우, 그 옵션에 따라 브라우저는 저장하게됨. 
    ```
    Set-Cookie: <em>value</em>[; expires=<em>date</em>][; domain=<em>domain</em>][; path=<em>path</em>][; secure]
    ```

## Cookie Options 
- domain
    - 쿠키가 보내진 곳
    - 해당 값은 `Set-cookie` header에 포함된 값이어야 함. 그 값이 아닐 경우 무시될 수 있음.
- path
    - url path
    - 보통 `/` 많이 쓰는 듯.
    - 왜? 루트 패스 이하 url에서 해당 쿠키를 이용하기 위해서..
- secure
    - flag option
    - secure 옵션을 가지고 있는 쿠키는, request가 SSL, HTTPS protocol에 의해 만들어질 때만 서버에 전송되어질 수 있음.
- HttpOnly
    - XSS를 방지하기 위해 등장 (cross-stie scripting)
    - 해당 옵션을 가지고 있는 쿠키는 Javascript의 `document.cookie` 를 통해 접근할 수 없도록 되어있음


# Session
- 위 쿠키와 거의 비슷, 다만 서버 쪽에 저장됨.
    - 서버쪽에 저장되기 때문에, 사용자가 많아지면 서버쪽 메모리를 과다하게 사용하게 될 수 도 있음.
- 서버쪽에 저장되므로, 보안상에서 쿠키 보다는 나음.
- 클라이언트가 서버쪽에 요청을 보내면, 고유 ID를 담아서 내려주는 방식


# Cookie vs Session
- 두가지 모두 사용자의 상태를 관리하기 위해서 존재함.
- 다만 어느 쪽 (클라이언트, 서버) 에 저장되는 지가 가장 큰 차이점.

# 더 알아보면 좋을 내용..
- Sticky session
- Session Clustering
- JWT Token


# 참고
- https://humanwhocodes.com/blog/2009/05/05/http-cookies-explained/#:~:text=Quite%20simply%2C%20a%20cookie%20is,on%20a%20set%20of%20rules.
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie
- https://stackoverflow.com/questions/6253633/cookies-vs-sessions
- https://www.youtube.com/watch?v=y0xMXlOAfss