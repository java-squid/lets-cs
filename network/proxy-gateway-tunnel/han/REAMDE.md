# Proxy
- 서버 혹은 프로그램
- Proxy란 의미는 보통 '대체' 라는 의미로 사용되는 듯.
- 즉 클라이언트의 요청을 바로 서버에 전달되는 것이 아닌, Proxy 라는 서버 혹은 프로그램을 통해서 서버에 전달되도록 함.
- 이를 통해 얻는 장점?
    - 서버의 보안성을 유지할 수 있음. (서버 IP를 숨김..)
    - 서버에 리소스를 굳이 요청하지 않아도, 프록시 서버가 처리하도록 할 수 있음 (캐싱)
- 어느쪽에서 해당 기능을 사용하냐에 따라 Forward, Reverse로 나뉘는듯..
    ![](https://kinsta.com/wp-content/uploads/2020/08/Forward-Proxy-vs-Reverse-Proxy-Servers.png)

## Forward Proxy
- gateway와 비슷한 역할
- 프록시 서비스를 클라이언트쪽에 제공
- 클라이언트의 IP등을 숨기고 요청을 보내는 등의 서비스가 Forward Proxy를 이용하는것에 해당하는 듯.
- [TOR](https://www.torproject.org/)

## Reverse Proxy
- 클라이언트 요청을 서버로 바로 전달하지 않도록, 서버 앞단에 프록서 서버를 배치하는 것.
- 서버 보안성, 캐시, 로드밸런서등의 장점을 취득할 수 있음.
- `Nginx` 등이 이에 해당.


# Gateway
- 보통 인터넷 서비스에 필요한 장비를 일컫는 말.
- 라우터 역할
- 프록시 여러 역할을 가지고 있기보다는, 단순하게 request, response를 **전달** 하는 역할만 있는 듯.

# Tunnel
- 인터넷 상에서 이루어지는 논리적인 형태를 의미.
    - 보통 두 endpoints 에서 이루어지는 연결을 의미함.
- local -- vpn -- internet 형태라면, 터널이 생겼다고 말할 수 있을듯.
    - 이러한 터널은, local 혹은 도착치에서 각각 제거될 수 있을듯.

# 참고
- https://stackoverflow.com/questions/10377679/whats-distinction-of-http-proxy-tunnel-gateway
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Proxy_servers_and_tunneling
- https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9D%EC%8B%9C_%EC%84%9C%EB%B2%84
- https://www.quora.com/What-are-the-differences-among-proxy-gateway-and-tunnel