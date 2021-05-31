# TCP/IP의 4계층 모델

### 1계층: 네트워크 인터페이스 계층 (Network Interface Layer)

OSI 계층의 물리 계층과 데이터 링크 계층에 해당합니다. Node-To-Node 간의 신뢰성 있는 데이터 전송을 담당하는 레이업니다. 물리적인 주소로 MAC을 사용하고, LAN, 패킷망 등에 사용됩니다. 

- 에러 검출 기능(Detecting errors), 패킷의 프레임화(Framing packets)
- 주요 프로토콜: Ethernet, Token Ring, Frame Relay, ATM

### 2계층: 인터넷 계층 (Internet Layer)

OSI 계층의 네트워크 계층에 해당합니다. 호스트 간의 라우팅을 담당합니다. 상위 트랜스포트 계층으로부터 받은 데이터에 IP 패킷 헤더를 붙여 IP 패킷을 만들고 이를 전송합니다. 어드레싱(Addressing), 패키징(Packaging), 라우팅(Routing) 기능을 제공합니다.

- 통신 노드 간의 IP 패킷을 전송하는 기능과 라우팅 기능을 담당
- 주요 프로토콜
    - IP (Internet Protocol): 비신뢰성, 비연결지향 데이터그램 프로토콜
    - ARP (Address Resolution Protocol): 주소변환 프로토콜 (IP 주소를 MAC 주소로 변환하는 프로토콜)
    - RARP (Reverse ARP): MAC 주소로 IP 주소를 찾는 프로토콜
    - ICMP (Internet Control Message Protocol): 상태 진단 메세지 프로토콜. (ex: ping)
    - IGMP (Internet Group Management Protocol): 멀티 캐스트용 프로토콜
    - OSPF (Open Shortest Path First): 링크 상대 라우팅 프로토콜 (인터넷에서 연결된 링크의 최적의 경로를 선택한다)

### 3계층: 전송 계층 (Transport Layer)

OSI 계층의 전송 계층에 해당합니다. 프로세스 간의 신뢰성 있는 데이터 전송을 담당합니다. 통신 노드 간의 연결을 제어하고, 네트워크 양단의 송수신 호스트 사이에서 신뢰성 있는 데이터 전송을 담당합니다.

- 주요 프로토콜: TCP, UDP

### 4계층: 응용 계층 (Application Layer)

OSI 계층의 세션 계층, 표현 계층, 응용 계층에 해당합니다. 사용자와 가장 가까운 계층으로, 서버나 클라이언트 응용 프로그램이 이 계층에서 동작합니다. 동작하기 위해서는 전송 계층의 주소, 즉 포트 번호를 사용합니다. TCP/UDP 기반의 응용프로그램을 구현할 때 사용합니다.

- 주요 프로토콜
    - 파일 전송: FTP(파일을 받거나 올릴 때 사용. 데이터 전송 포트번호:20, 제어용:21), TFTP
    - 원격 로그인: Telnet(원격 터미널을 접속할 때 사용. 포트번호:23)
    - 전자메일 범주: SMTP(메일 전송용 프로토콜. TCP 포트번호:25), POP3(메일 수신용 프로토콜. TCP 포트번호:110)
    - 지원 서비스 범주: DNS, SNMP
    - 기타 프로토콜: HTTP(TCP 기반의 프로토콜. 포트번호:80), SSH(보안이 약한 텔넷을 보완한 프로토콜. 포트번호:22)

### TCP/IP의 장점

- 다른 컴퓨터 간 연결을 설정하는데 도움이 된다.
- 운영 체제와 독립적으로 작동한다.
- 많은 라우팅 프로토콜을 지원한다.
- TCP/IP 모델에는 확장성이 뛰어난 클라이언트-서버 아키텍처가 있다.
- 독립적으로 작동 할 수 있다.
- 여러 라우팅 프로토콜을 지원한다.
- 두 컴퓨터 간의 연결을 설정하는데 사용 할 수 있다.

### 출처

- [https://m.blog.naver.com/soojin_2604/221950485931](https://m.blog.naver.com/soojin_2604/221950485931)