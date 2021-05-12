# SSL
- Secure Sockets Layer, TLS (Tranport Layer Security) 전송 계층 보안
- SSL 3.0을 개선시켜서 나온 게 TLS 1.0..
- TLS 1.0 이 후 SSL은 deprecated 됨.
    - 엄밀하게 말하면 SSL, TLS는 다른 것이지만..
    - SSL/TLS은 TLS를 의미하는 걸로 이해됨.

# TLS
![image](https://user-images.githubusercontent.com/22140570/116811965-26833600-ab87-11eb-9847-004fb1385b24.png)
- 응용 계층 데이터를 암호화해주고, 암호화되어 전송 계층으로 들어온 데이터를 복호화 해주는 역할
- TCP의 443포트 사용
- 제공하는 3가지 기능
    1. 암호화 Encryption (내가 보낸 데이터를, 원하는 상대방만 확인할 수 있도록)
    2. 인증 Authentication (내가 받은 데이터가, 원하는 상대방으로 부터 온 것인지 확인하는 것)
    3. 무결성 Integrity (메세지가 전송 중 위/변조 되지 않았음을 확인)
- HTTPS 의 기반 기술

## 어떻게 암호화 되는가?
![image](https://user-images.githubusercontent.com/22140570/116811987-44e93180-ab87-11eb-8d2a-1c08685f9dad.png)
- TLS HandShake를 이용


## 어떻게 인증 하는가?
- TLS conection 을 성립하기 위해 필수적인 부분 
    즉 자신이 기대하고 있는 대상과 커넥션이 되어있는 지 확인하는 과정
![image](https://user-images.githubusercontent.com/22140570/116813385-816c5b80-ab8e-11eb-90d8-5326a3e293f2.png)
- Alice, Bob은 서로 public key를 공유하고,
- Alice는 메세지를 자신의 private key로 암호화하여, Bob에게 보내면
- Bob은 Alice의 public키로 해당 메세지를 인증한다. (Alice가 보냈구나!)

- 사이트 간 인증 방법
![image](https://user-images.githubusercontent.com/22140570/116813054-d7d89a80-ab8c-11eb-9343-fa1de8c5f85b.png)
- **Chain of Trust and Certificate Authorities**
1. Root CA는 중간 인증 기간에 인증서를 주고..
2. 중간 인증 기간은 특정 사이트에 인증서를 준다..
3. 해당 사이트에서 어떤 요청을 보낼 때, 브라우저는 중간 인증서에 공개키로 암호화하여 보낸다.

## 어떻게 무결성을 확인 하는가?
![image](https://user-images.githubusercontent.com/22140570/116811906-b8d70a00-ab86-11eb-8b23-cf2afe2f5209.png)
1. 보내는 사람은 자신의 메세지를 MAC Algorithm을 통해 암호화하여 보냄
2. 받는 사람은 메세지를 받은 후, 해당 메세지를 포함된 MAC Algorithm을 통해 암호화 하여 자신이 받은 데이터와 동일한지 판단.

# 더 알아보면 좋은 내용..
 - X.509 certificates
 - 공개키, 대칭키 암호화

# 참고
- https://www.ssl.com/faqs/faq-what-is-ssl/
- https://www.youtube.com/watch?v=EPcQqkqqouk&list=PLgXGHBqgT2TvpJ_p9L_yZKPifgdBOzdVH&index=74
- https://hpbn.co/transport-layer-security-tls/
- https://webactually.com/2018/11/16/http%EC%97%90%EC%84%9C-https%EB%A1%9C-%EC%A0%84%ED%99%98%ED%95%98%EA%B8%B0-%EC%9C%84%ED%95%9C-%EC%99%84%EB%B2%BD-%EA%B0%80%EC%9D%B4%EB%93%9C/
- https://engineering.linecorp.com/ko/blog/best-practices-to-secure-your-ssl-tls/ 

# QnA

## Hamil
> 쿠키와 세션은 상태를 유지하기 위해 사용한다고 하는데요. 그렇다면 상태를 유지하기 위해 어떤 방법을 사용하는 게 더 좋을까요?

- 어떤 상태 유지가 목적인지에 따라 갈릴듯
- 로그인 상태 유지이면, 서버에서 관리하는 세션이 좀 더 안전한 방법이라 생각되고,
- 단순 유저의 행동을 기록하기 위해서라면, 중요한 내용이 아니라면, 쿠키를 통해 관리하는 게 좋은 선택으로 생각됨.

> HTTPS 를 제공하는 사이트의 SSL 인증서를 직접 확인해보고, SSL 인증서에는 대표적으로 어떤 내용이 들어있는지 알아봅시다

- 이름 : *.google.com -> 특정 사이트가 아닌 꽤 많은 사이트들이 이 인증서가 보장하고 있는 듯 싶네요.
- 유효 기간
- 공개키..
- 확장키..
- 지문 : 아마도 해당 인증서가 변조되었는지 판단하는 기준이지 않을까 합니다.

## Hoi

> 세션을 사용하면 서버에 부하가 갈수도 있다면 부하의 상황에 대비하여 어떤 해결책이 있나요 ? 클라이언트에 쿠키로 저장하는게 방법인지 궁금합니다. 간단한 예시 하나 부탁드려요

- 서버 마련된 세션 DB를 사용하는 것이 아닌, 세션 만을 위한 다른 DB를 사용하면 될 것 같네요.

- 예를 들면, Redis 와 같은 DB를 사용하는 방향을 말씀드릴 수 있을 것 같아요.
- 즉 서버 쪽에 세션을 마련된 공간을 사용하면, 이용량이 늘어날수록 다른 서비스에 영향을 줄 수 있으니,
- 아예 세션만을 담당하는 DB를 마련 (Redis) 이곳에 Session 을 저장하고 다른 서버에서 사용하는 방식을 사용하는 방식 입니다

> 메세지의 무결성은 어떤식으로 보장해 주는지 궁금합니다 !

1. 자신의 메세지로 부터 나온 MAC (메세지 인증 코드) 같이 묶어 암호화 하여 수신자에게 전송
2. 수신자는 메세지를 그대로 받고, 이 메세지로 부터 MAC'를 도출해내어서,
3. 처음 받은 MAC과 자신이 도출해놓은 MAC' 를 비교하여 메세지가 변조되었음을 감지한다고 합니다!