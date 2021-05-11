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