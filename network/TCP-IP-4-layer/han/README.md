# TCP/IP model
![](https://www.guru99.com/images/1/093019_0615_TCPIPModelW1.png)
- 개념적으로 위와 같이 4계층으로 나뉘어져 있음.
- 각각 계층이 가지고 있는 프로토콜들이 존재
- TCP/IP는 여러 프로토콜을 모은 총칭을 의미하기도 함.

## Application Layer
- 기본적으로 user와 직접적으로 소통하는 레벨
- 유저가 로그인 하거나, 
- 이메일 서비스를 제공하는 레벨
- HTTP, FTP, SMTP, Telnet, DNS 프로토콜이 이에 해당함.

## Transport Layer
- 자료의 송수신을 담당.
- TCP, UDP 프로토콜이 이에 해당

## Internet Layer
- IP를 이용해, 네트워크 노드간 전송과 라우팅 기능을 담당.
- 유저가 요청한 최종 목적지까지 연결되도록 연결성을 제공.
- IP, ARP, ICMP, IGMP가 이에 해당

## Network Layer
- 패킷을 네트워크 레이어로 전달하는 역할.
- MAC 주소를 이용해 패킷을 어디에 보내야할지 판단된다.

# 송,수신 단계별
![](https://media.vlpt.us/post-images/conatuseus/d6b2f390-d3c9-11e9-b578-e7ac71f50ec2/image.png)
- 네트워크 계층은 위 컨셉에서 인터넷 레이어를 의미하고, 링크 계층은 네트워크 레이어를 의미하는듯.

# TCP and IP
- 아마도 이 두가지 프로토콜이 인터넷 통신에서 가장 중요하기 때문에, TCP/IP와 같은 명칭이 나오지 않았나 싶음.

## TCP
- 2번째 계층 (트랜스포트) 에 해당
- 클라이언트와 서버 둘이 신뢰할 수 있는 바이트 스트림을 열기 위해서 사용하는 프로토콜임.
- 신뢰를 형성하기 위해 3-hand-shaking등의 방법을 이용 

## IP
- 3번째 계층에 해당하고 (Internet or 네트워크 계층)
- IP 주소는 통신 기기들이 서로 인식하기 위한 특수한 번호
    - 반면 MAC 주소는 각 네트워크 카트에 할당된 **고유한** 값
![](https://media.vlpt.us/post-images/conatuseus/3508a100-d3da-11e9-b72b-91ecbe0f0a65/image.png)
- ARP라는 프로토콜을 이용하여, 수신지의 IP주소를 바탕으로 다음에 요청을 보낼 MAC주소를 조사할 수 있음.
- 위와 같이 계속 요청하다보면 결국 원하는 도착지에 도달하는 형식

# 참고
- https://www.guru99.com/tcp-ip-model.html
- https://d2.naver.com/helloworld/47667
- https://velog.io/@conatuseus/2019-09-10-2009-%EC%9E%91%EC%84%B1%EB%90%A8-xsk0ds2eqf
- [1% 네트워크](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788931556742) -> 102p...~