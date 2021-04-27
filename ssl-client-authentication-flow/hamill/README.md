# SSL 인증 과정을 알기 위한 사전 지식

### authentication 인증 vs authorization 권한부여
- 인증 : 자신이 누구라고 주장하는 사람을 확인하는 절차
- 권한부여 : 가고 싶은 곳으로 가도록 혹은 원하는 정보를 얻도록 허용하는 과정

### HTTPS
- HTTP + Secure
- HTTP의 보안 문제를 해결하기 위해 나온 프로토콜
    - HTTP로 정보를 전달하는 과정 중간에서 공격자가 정보를 가로챌 수 있다.
- SSL (Secure Socket Layer)
    - HTTP 통신 시 정보를 안전하게 전송하기 위한 인터넷 통신 규약 프로토콜

### HTTPS와 SSL
- 다음 이미지는 HTTPS와 SSL의 관계를 잘 보여준다. 
- HTTPS는 SSL 프로토콜 위에서 돌아가고, HTTPS 외에도 FTP, NNTP, XMPP 등도 SSL 위에서 돌아가면 정보 전달 시 보안을 유지 할 수 있다.
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FYQRTt%2FbtqxPSXDzOr%2FAbJopbCnnGtSbSK9ZMhnok%2Fimg.png)

### TLS
TLS란 SSL 과 동일한 의미이다. 네스케이프에서 보안 문제를 해결하기 위해 SSL 을 발명했고, 이것이 표준이 되면서 TLS 라는 이름으로 바뀌게 되었다. 현재는 둘을 혼용해서 사용하지만 SSL 이라는 이름을 더 많이 사용하고 있으며, 오픈소스의 이름 역시 OpenSSL 이다.

### SSL 인증서
- 클라이언트와 서버 간의 안전한 정보 전달을 보증해주는 문서. 
- 클라이언트가 서버에 접속하게 된다면 서버는 클라이언트에게 이 인증서를 전달해 신뢰를 얻게 된다.
- 이점
    - 스니핑 방지
        - 스니핑 : 네트워크 주변을 지나다니는 패킷을 엿보면서 계정(ID)과 패스워드를 알아내기 위한 행위다.
    - 피싱 방지
        - 피싱 : 피싱 사이트를 만들어 사용자로부터 주요 정보를 빼내는 행위다.
    - 데이터 변조 방지

### SSL 인증서 암호화
SSL은 크게 2 가지 암호화 방법을 사용한다.
- 대칭키 암호화
    - 하나의 비밀키를 양쪽(client & server)가 모두 같이 사용
    - 암호화와 복호화에 사용하는 키가 같은 암호화 알고리즘
- 공개키(비대칭키) 암호화
    - 비밀키 하나만 가지는 대칭키 암호화 방법과 달리 공개키와 비밀키 두 개 존재
    - 암호화와 복호화에 사용하는 키가 서로 다름
    - 암호화 할 때의 키를 공개키, 복호화 할 때의 키를 개인키라고 한다.

HTTPS 통신에서 실제 전송되는 데이터의 암호화에는 대칭키 암호화 방식을 사용하고, 키 교환에는 공개키(비대칭키) 암호화를 사용한다.

### SSL 인증서의 내용
- CA에 대한 정보
    - CA (Certificate authority 또는 Root Certificate) : 말 그대로 인증기관을 의미한다. 클라이너트가 접속한 서버가 정상적인 서버가 맞는지 확인하여 맞다면 인증서를 제공해주는 역할을 한다.
    - 공인된 기업들에서 이 역할을 맡고 있고, 이러한 CA 로부터 SSL 인증서를 구매하여야 사용 할 수 있다.  
- 서버와 통신하기 위한 공개키

[참고] 네이버의 SSL 인증서
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FrIQyc%2FbtqxQTIvM3e%2FhkENy1duCIJzLRm0mjpva1%2Fimg.png)
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcxCcsP%2FbtqxSiug2rE%2FyemriKPAJ2so6mJomCrfkk%2Fimg.png)

# SSL 동작 과정
1. 브라우저가 www.naver.com 에 연결을 시도한다.
2. www.naver.com 서버는 브라우저에 SSL 인증서를 보낸다. 
    - 이 인증서에는 www.naver.com 서버의 공개키와 이 공개키가 실제로 www.naver.com 에 속해 있다는 증거들을 포함하고 있다.
3. 브라우저는 SSL 인증서를 확인하여 www.naver.com 에 대한 적절한 공개키가 있는지 확인한다.
4. 브라우저는 www.naver.com 서버 연결에 사용할 임의의 새 대칭키 K를 선택한다. 앞에서 받은 www.naver.com 서버의 공개키로 K를 암호화한다.
5. www.naver.com 서버는 개인키를 사용하여 K를 해독한다. 이제 브라우저와 네이버 서버 모두 K를 알고 있는 상태가 된다.
6. 브라우저가 www.naver.com 로 무언가를 보내려고 할 때마다 K로 암호화한다. www.naver.com 서버는 수신 즉시 암호를 해독한다. www.naver.com 서버는 브라우저로 무언가를 보내려고 할 때마다 K로 암호화한다.

[참고] SSL Authentication flow

![](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/01/38/03/metablogapi/6574.060215_0710_ClientCerti2.png)


### 출처
- https://httpd.apache.org/docs/2.2/ko/howto/auth.html
- https://terms.naver.com/entry.naver?docId=2067365&cid=42107&categoryId=42107
- https://gaeko-security-hack.tistory.com/123
- https://offbyone.tistory.com/274
- https://stackoverflow.com/questions/470523/how-does-ssl-really-work
- https://techcommunity.microsoft.com/t5/iis-support-blog/client-certificate-authentication-part-1/ba-p/324623