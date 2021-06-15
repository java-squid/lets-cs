# 프록시, 게이트웨이, 터널은 무엇이고 어디서 이용되는가?

### Proxy 서버

다른 클라이언트를 대신하여 요청하기 위해 서버와 클라이언트 역할을 모두 수행하는 중간 프로그램입니다. 요청은 내부적으로 또는 번역이 가능하게(possible translation)하여 다른 서버로 전달하여 처리됩니다. 

**프록시 서버 사용 목적**

- 익명으로 컴퓨터를 유지 (주로 보안을 위해)
- 캐시를 사용하여 리소스로 접근을 빠르게 하기 위함. 웹 프록시는 웹 서버로부터 웹 페이지를 캐시로 저장하는 데 흔히 사용됨
- 네트워크 서비스나 콘텐츠로의 접근 정책을 적용하기 위해 (이를테면 원치 않는 사이트 차단)
- 사용률을 기록하고 검사하기 위해 (이를테면 회사는 인터넷 이용을 파악)
- 역으로 IP 추적을 당하지 않을 목적으로
- 전달에 앞서 악성 코드를 목적으로 전달된 콘텐츠를 검사하기 위해
- 밖으로 나가는 콘텐츠를 검사하기 위해 (데이터 유출 보호)
- 지역 제한을 우회하기 위해

[참고 자료]

![](https://t1.daumcdn.net/cfile/tistory/992031425A49F3320D)

### Gateway 서버

다른 서버의 중개자 역할을 하는 서버. 프록시와 달리 게이트웨이는 요청된 리소스의 Origin 서버인 것처럼 요청을 수신합니다. 클라이언트는 게이트웨이와 통신하고 있음을 인식하지 못할 수 있습니다.

프록시, 게이트웨이, 터널은 무엇이고 어디서 이용되는가?

**게이트웨이 사용 목적**

컴퓨터 네트워크에서 게이트웨이는 현재 사용자가 위치한 네트워크에서 다른 네트워크로 이동하기 위해 반드시 거쳐야 하는 거점을 의미합니다. 해외 여행을 가기 위해서 공항을 거쳐야하는 것과 같은 개념입니다.

두 컴퓨터가 네트워크 상에서 서로 연결되려면 동일한 통신 프로토콜을 사용해야 합니다. 따라서 프로토콜이 다른 네트워크 상의 컴퓨터와 통신하려면 두 프로토콜을 적절히 변환해 주는 변환기가 필요한데, 게이트웨이가 바로 이러한 변환기 역할을 합니다. 

게이트웨이는 하나 이상의 프로토콜을 사용하여 통신한다는 점에서 라우터, 스위치와는 구별되며 OSI 7계층 가운데 어느 곳에서도 동작이 가능하므로 전송 방식이 다른 통신망도 흡수하여 서로 다른 기종끼리도 접속을 가능하게 합니다.

이러한 기능은 HTTP 요청으로 FTP 서버에 있는 파일을 가져오고 싶은 경우 중간에 있는 게이트웨이가 두 프로토콜이 호환 가능하도록 중개자 역할을 하는 것입니다.

프록시 서버와는 달리 게이트웨이는 웹 사이트를 차단 할 수 없기 때문에 항상 내부 네트워크를 인터넷에 노출시킬 위험이 있습니다.

[참고 자료]

![](https://t1.daumcdn.net/cfile/tistory/99A281435A49F44613)

### Tunnel

2개의 커넥션 사이에 블라인드 중개(blind relay) 역할을 하는 중개 프로그램. 일단 활성화된 터널은 HTTP 요청에 의해 터널이 시작되었을 수 있지만 터널은 HTTP 통신의 당사자로 간주되지 않습니다. 중개된 연결부의 양쪽 끝이 닫히면 터널이 사라집니다.

터널링은 연결해야 할 두 지점간에 마치 터널이 뚫린 것처럼 통로를 생성하는 것을 의미하는데, 이 터널은 터널링을 지원하는 프로토콜을 사용하여 구현되고 있으며, 사설망과 같은 보안 기능을 제공할 수 있어서 암호화되어 통신이 필요한 기술인 VPN, SSL 서비스 등에 사용됩니다.

### 출처

- [https://www.w3.org/Protocols/rfc2616/rfc2616-sec1.html#sec1](https://www.w3.org/Protocols/rfc2616/rfc2616-sec1.html#sec1)
- [https://stackoverflow.com/questions/10377679/whats-distinction-of-http-proxy-tunnel-gateway](https://stackoverflow.com/questions/10377679/whats-distinction-of-http-proxy-tunnel-gateway)
- [https://www.techwalla.com/articles/difference-between-a-proxy-server-a-gateway](https://www.techwalla.com/articles/difference-between-a-proxy-server-a-gateway)
- [https://dororongju.tistory.com/53](https://dororongju.tistory.com/53)
- [https://ko.wikipedia.org/wiki/프록시_서버](https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9D%EC%8B%9C_%EC%84%9C%EB%B2%84)
- [https://internetofthingsagenda.techtarget.com/definition/gateway](https://internetofthingsagenda.techtarget.com/definition/gateway)
- [https://liveyourit.tistory.com/3](https://liveyourit.tistory.com/3)
