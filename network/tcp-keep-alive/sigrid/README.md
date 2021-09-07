# TCP Keep-Alive란?
* 인터넷에 연결된 컴퓨터 간의 정보 를 안정적이고 순서대로, 에러없이 통신할 수 있게 해주는 것이 TCP
* TCP 기반의 통신에서 세션을 어떻게 유지하는 가에 대한 문제이다.
* TCP Keep-Alive란 두 종단(end-point)간 맺어진 세션을 지속적으로 유지해서 통신이 일어날 때마다 이미 **존재하고 있는** 세션을 이용하도록 한다.

## TCP three handshaking
![](https://i.imgur.com/zwBoGxw.png)
* TCP는 **연결 지향적** 이라고 하는데 이름부터가 transmission connection protocol이다.

### STEP 1

![](https://i.imgur.com/3Rdep2h.png)

* Client는 SYN 패킷을 보낸다.
    * SYN, ACK 패킷에는 모두 ISN이라고 하는 initial sequence number가 있는데, 이는 random하게 구성된다.
    * 서버에서 SYN+ACK 패킷을 보내기를 기다리는 상태, 곧 SYN_SENT 상태가 된다.
    * SYN(Synchronization) : 연결 요청, 세션을 설정하는데 사용되며 초기에 Sequence Number를 보냄

### STEP 2
![](https://i.imgur.com/aDwwCsa.png)

* Server는 Listen 상태로 Port 서비스가 가능한 상태여야 한다.
* Server는 연결 요청을 받게 되고, 연결 수락을 위해 ACK(a+1) + SYN(b) 패킷을 보낸다.
    * 이 때 b는 Server의 Sequence Number를 뜻하고, ACK를 보낼때 Client로 부터 받은 Sequence Number인 a에 +1 한 값을 보낸다.
    * ACK(Acknowlegdement) : 승인을 뜻한다.

### STEP 3
![](https://i.imgur.com/lPZnevo.png)
* Client는 SYN(b)를 받아 Server에게 ACK(b+1) 패킷을 보낸다.

### Wireshark로 보기
* 위 3번의 과정을 거치게 되면 Client와 Server 간의 연결이 성립(Establish)이 되어 Server로 부터 데이터를 받게 된다.

![](https://i.imgur.com/Z8HXvb3.png)

* Client가 Server에게 SYN(0)을 보낸다.
* Server는 Client에게 ACK(1) + SYN(0)을 보낸다.
* Client는 Server에게 ACK(1)을 보낸다.
* 연결이 성립되었으므로 4번째 줄을 보면 Client가 Server에게 HTTP 프로토콜로 ```/wp-admin/```  의 정보를 요청한다.

[참고](https://lactea.kr/entry/Network-%E2%80%93-tcp-%EC%97%B0%EA%B2%B0-%EB%B6%84%EC%84%9D#recentComments)

### 연결을 해제하는 four way hand shake
![](https://i.imgur.com/Q2ssguZ.png)
* ESTABLISHED 상태가 된 연결을 대상으로 한다.
* TCP 커넥션 종료는 클라이언트와 서버 어느 쪽에서도 할 수 있기 때문에, 클라이언트와 서버로 나누지 않고 active close와 passive close로 나눈다.
    * Active close: TCP 연결 해제 요청한 쪽, 그러니까 트래픽을 전송(request)하고 정상적으로 전송 되었으면 연결을 끊는 쪽
    * Passive close: TCP 연결 해제 요청을 받은 쪽, 트래픽을 받는 쪽(api 서버라고 치면 클라이언트에게 응답을 주는 쪽)
* 아래의 예시에서는 Client가 Active close, Server가 Passive close라고 가정하자.
1. Client가 연결을 종료 하겠다고 FIN 패킷을 보낸다. (Client는 FIN_WAIT1 상태)
2. Server는 FIN을 받고 확인 메시지인 ACK를 보낸다. (Server는 그 후 데이터를 모두 보낼 때까지 잠깐 TIME_OUT or CLOSE_WAIT 상태가 되며, Client는 FIN_WAIT2 상태가 된다)
3. 데이터를 다 보냈을 경우, Server는 연결이 종료 되었다고 FIN 패킷을 Client에게 보낸다. (Server는 LAST_ACK 상태가 된다)
4. Client는 FIN 패킷을 받고 확인 메시지인 ACK를 보낸다. (Client는 아직 서버로 부터 받지 못한 데이터가 있을 것을 대비해 일정 시간 동안 세션을 남겨놓고 패킷을 기다리는 과정을 거치기 위해 TIME_WAIT 상태가 된다)
5. Server는 소켓 연결을 close 한다.

[참고 1](https://tech.kakao.com/2016/04/21/closewait-timewait/)
[참고 2](https://smjeon.dev/etc/tcp-state/)

### 최종 정리
![](https://i.imgur.com/o8n4nbc.png)


### ISN은 Randomnize 한 번호이다
* Wireshark가 위에서는 ```relatively``` sequence한 번호를 보여주기는 하는데 이는 그냥 편의를 위해서 그렇게 표시한 것이다.
* 실제로는 32비트로 random한 난수를 사용하는데, 그 이유는 서버 측에서 SYN을 보고 패킷을 구분하는데 난수가 아닌 순차적인 number로 전송된다면 이전의 connection에서 온 패킷으로 오해할 수 있는 여지가 있어서 그렇다고 한다.
* Connection을 맺을 때 사용하는 포트(port)는 유한 범위 내에서 사용하고 시간이 지남에 따라 재사용된다. 따라서 두 통신 호스트가 과거에 사용된 포트 번호 쌍을 사용하는 가능성이 존재한다.
* 이는 실제로 TCP Sequence Prediction Attacks라고 하는 해킹 기법과도 연관이 있어서 난수를 사용해야 한다고 한다. [참고](https://github.com/dreamgonfly/TIL/blob/master/TCP-Sequence-Prediction-Attack.md#why-is-initial-sequence-numberisn-random)

```
In the first two steps of the 3-way handshake, both computers exchange an initial sequence number (ISN). This number can be arbitrary, and should in fact be unpredictable to defend against TCP sequence prediction attacks.
```

[참고](https://packetlife.net/blog/2010/jun/7/understanding-tcp-sequence-acknowledgment-numbers/)

## 그런데 매번 handshaking 하면 불편하다
* 매 통신마다 3 way handshaking을 하고 있으면 매우 불편할 것이다. 특히 통신량이 많고 지속적인 경우라면 더더욱 그렇다.
* 통신이 지속적으로 이뤄진다면 기존에 성사된 세션을 유지할 수 있는 방법은 없을까 고민할 수 있다. 그것이 바로 TCP Keep Alive이다.

![](https://i.imgur.com/DZgNegh.png)
* 위의 그림을 보면 TCP Keepalive는 일정 시간이 지나면 연결된 세션의 두 종단이 서로 살아 있는지 확인하는 아주 작은 byte의 패킷을 보낸다.
* 연결을 유지하고 싶은 쪽에서 보내면 되니까 클라이언트던 서버던 한 쪽에서 보내기만 하면 된다.
* 현재 네트워크 소켓의 keepalive 설정이 어떤지 확인하고 싶으면 ```netstat``` 명령어를 활용하면 된다.

```
# netstat -napo
root@server1:~# netstat -napo
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name Timer
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      576/rpcbind      off (0.00/0/0)
tcp        0      0 0.0.0.0:465             0.0.0.0:*               LISTEN      1322/master      off (0.00/0/0)
...
tcp        0      0 10.0.2.15:22            10.0.2.2:51040          ESTABLISHED 1787/sshd: vagrant  keepalive (7198.14/0/0)
...
udp        0      0 0.0.0.0:111             0.0.0.0:*                           576/rpcbind      off (0.00/0/0)
....
```
*  제일 마지막 열에 있는 Timer 항목을 통해서 현재 소켓에 설정된 타이머 값을 볼 수 있다. ESTABLISHED 상태의 소켓에서는 Keepalive 타이머를 확인할 수 있다.
    *  sshd 데몬이 사용하는 소켓에 keepalive 옵션이 켜져 있고 타이머가 70초 가량 남아있다.
* 해당 타이머의 시간이 다 되면 연결이 살아있는지를 확인하는 작은 패킷을 하나 보낸다.
* 대부분의 애플리케이션에서는 TCP Keepalive 기능을 제어할 수 있는 명령어를 제공하니까 이를 활용하면 되겠다.

### 실제 서비스에서는 어떻게 활용할 수 있는가
* DB 서버 한대와 애플리케이션 서버 한 대가 있다고 가정한다.
* 만약 DB 서버에서 설정된 iptables로 인해 FIN패킷을 받지 못해서, 클라이언트 입장에서는 DB서버와의 연결이 끊어졌는지 알 방법이 없다고 가정하자.
* 그렇다면 클라이언트 측 소켓은 여전히 ESTABLISHED로 연결이 지정되어 있을 것이다.
* 하지만 keepalive옵션을 활용한 소켓이기 때문에 keepalive 타이머에 설정된 일정 시간이 지나면 keepalive 패킷에 대한 응답을 받지 못했기 때문에 소켓이 종료된다.
* 이를 통해 TCP Keepalive를 활용하여 좀비 커넥션을 종료시킬 수 있다라고 하는 것이다.

[참고](https://jihooyim1.gitbooks.io/linuxbasic/content/contents/08.html)

### Case Study - MQ 서버와 로드 밸런서
* 비동기적인 작업을 하기 위해서 메시지큐 서버를 이용한다고 해보자. 메시지큐 서버를 이중화하기 위하여 로드 밸런싱도 해본다고 쳐보자.
* 클라이언트는 여러 대의 MQ 서버 중 한 대에 붙어야 하는데, 만약 자기가 연결한 서버에 오류가 났다면 로드 밸런서에 의존해서 해당 오류를 deroute해야 한다.
![](https://i.imgur.com/g4FRmP1.png)
* 아무런 이슈없이 이중화까지 잘 동작할 것 같은 이 MQ구조에 어떤 문제가 있을까?
    * 클라이언트에서는 간헐적으로 타임아웃이 발생하며, 서버에서는 사용하지 않는 것으로 보이는 소켓이 ESTABLISHED 상태로 유지되고 있다는 두 가지 이슈가 있다.
* 왜 이런 문제가 나타나는 것일까?
    * 로드 밸런서는 클라이언트와 서버간 TCP handshake를 끝내고 정상적으로 맺어진 세션들을 세션 테이블에 저장한다.
    * 그래서 두 종단 간에 세션이 정상적으로 맺어져 있음을 기억하고 클라이언트의 요청을 특정 서버로 보낼 수 있다.
    * 로드 밸런서에는 Idle timeout기능이 있는데, 일정 시간동안 사용되지 않은 세션을 세션테이블에서 정리하는 기능이 있다. 그런데 로드 밸런서의 세션 테이블에서만 지워진다는 것이고 두 종단의 세션 테이블이 지워졌음을 알리는 역할은 하지 않는다.
    * 만약 로드 밸런서의 idle timeout 에 걸리면 클라이언트와 서버는 알아채지 못하지만 둘 사이의 세션 정보는 로드 밸런서에서 사라지게 되는 것이다.
    * 클라이언트는 요청을 보냈지만 제대로된 응답을 받지 못하고 RST 패킷("너 누구냐")을 받았기 때문에 TCP Handshake를 맺고 다시 요청을 보내야한다.
    * 그리고 이때 소요되는 시간이 애플리케이션에서 설정한 타임아웃 임계치를 넘어가게 되면서 Timeout Exception을 경험하게 된다.
    * 서비스의 특성상 사용자의 요청이 적은 새벽 시간대에는 맺어져 있는 세션으로 패킷이 흐르지 않을 가능성이 크며, 바로 이때 idle timeout에 걸려서 커넥션 풀로 열어놓은 세션들이 로드 밸런서에서 지워지는 일이 발생한다.
    * 지워진 후에 클라이언트가 다시 한번 요청을 보내면 로드 밸런서는 자신에게 연결되어있는 서버 중 아무 서버에게나 이 패킷을 전달한다. (어디로 갈 지는 로드 밸런서의 밸런싱 정책에 따를 수밖에 없다)
    * 아주 운좋게 기존에 연결된 서버에 패킷이 전달되면 문제가 없겠지만, 그렇지 않은 경우라면 서버쪽에서는 연결도 맺지 않은 클라이언트가 데이터를 쓰겠다고 요청하니 당연히 거부 의사를 밝히게 되고, 클라이언트는 타임아웃을 경험하게 된다.
    * 그러면 클라이언트는 자신이 현재 관리하고 있는 커넥션 풀이 잘못되어 있음을 인지하고 새롭게 커넥션을 열어서 새로운 서버와 연결한다.
    * 문제는 기존에 연결되어 있던 서버는 클라이언트의 이런 작업을 전혀 알 수 없기 때문에 계속해서 연결되어 있다고 착각하고, 이런 경우 들이 모여서 서버쪽에 다량의 좀비 커넥션이 남게 되는 것이다.
* 어떻게 해결할 수 있는가?
    * 로드 밸런서의 Idle timeout에 걸리지 않도록 keepalive 관련 파라미터들을 수정해서 적용하면 지속적으로 세션을 유지할 수 있게 된다.

[참고](https://jihooyim1.gitbooks.io/linuxbasic/content/contents/08.html)

### 결론
* TCP 종단간의 연결 유지를 위한 TCP Keepalive에 대해 살펴보았다.
* TCP Keepalive는 특히 네트워크 단절 등 여러가지 이유로 발생할 수 있는 좀비 커넥션을 방지하는데 큰 도움이 된다.
* TCP 환경은 언제든지 패킷 손실이 일어날 수 있으며 FIN패킷도 손실될 수 있다.
* 커널은 TCP keepalive라는 기능을 제공해서 종단 간의 세션을 유지하거나, 비정상적인 소켓이라고 판단 되면 정리할 수 있도록 도와준다.
* 다양한 애플리케이션들에서 TCP Keepalive를 지원하며, 필요하다고 판단되면 해당 애플리케이션에서 어떻게 TCP Keepalive 옵션을 켤 수 있는지 찾아서 설정해주어야 한다.

## TCP Header
### Segments
* TCP 패킷을 '세그먼트' 라고 부른다.
### Source port, Destination port
* 세그먼트의 출발지와 목적지를 나타내는 필드로, 각각 16 bits 를 할당받는다.
* 이때 출발지와 목적지의 주소를 판별하기 위해서는 IP 주소와 포트 번호가 필요하다. TCP에서는 port number만 갖고 있다. IP 주소는 하위 계층 network에서 알려줄테니까...
### Sequence Number
* 시퀀스 번호는 전송하는 데이터의 순서를 의미하며, 32 bits를 할당받는다.
* 시퀀스 번호를 활용하여 수신자는 쪼개진 세그먼트의 순서를 파악하여 올바른 순서로 데이터를 재조립할 수 있게 된다.
* 송신자가 최초로 데이터를 전송할 때는 이 번호를 랜덤한 수로 초기화 한다.
* 이후 자신이 보낼 데이터의 1 bytes당 시퀀스 번호를 1씩 증가시키며 데이터의 순서를 표현하다 4,294,967,296를 넘어갈 경우 다시 0부터 시작한다.

### Acknowledgement Number
* 승인 번호는 데이터를 받은 수신자가 예상하는 다음 시퀀스 번호를 의미하며, 32 bits를 할당받는다.
* 연결 설정과 연결 해제 때 발생하는 핸드쉐이크 과정에서는 ```상대방이 보낸 시퀀스 번호 + 1로 자신의 승인 번호```를 만든다.
* 실제로 데이터를 주고 받을 때는 ```상대방이 보낸 시퀀스 번호 + 자신이 받은 데이터의 bytes```로 승인 번호를 만든다.
* 만약, ```tcpdump```를 사용하여 패킷을 검사해보면 송신 측이 보낸 데이터의 길이만큼 수신 측의 승인 번호가 증가하는 모습을 확인할 수 있다.

### Data Offset
* 전체 세그먼트 중에서 헤더가 아닌 데이터가 시작되는 위치가 어디부터인지를 표시한다. 32bit word 단위이다. 32bit 체계에서의 1 Word = 4 bytes를 의미하니까, 이 필드의 값에 4를 곱하면 세그먼트에서 헤더를 제외한 실제 데이터의 시작 위치를 알 수 있게 된다.

### Flags
* ACK: Acknowledgment(승인 번호) 필드에 값이 채워져있음을 알리는 플래그. 이 플래그가 0이라면 승인 번호 필드 자체가 무시된다.
* RST: Reset 플래그. 이미 연결이 확립되어 ESTABLISHED 상태인 상대방에게 연결을 강제로 리셋해달라는 요청의 의미이다.
* SYN: Synchronize 플래그. 상대방과 연결을 생성할 때, 시퀀스 번호의 동기화를 맞추기 위한 세그먼트임을 의미한다.
* FIN: Finish 플래그. 상대방과 연결을 종료하고 싶다는 요청인 세그먼트임을 의미한다.

### Window Size
* 윈도우 사이즈 필드에는 한 번에 전송할 수 있는 데이터의 양을 의미하는 값을 담는다. 2^16 = 65536 만큼 데이터를 표현할 수 있다. 이게 64KB인데 너무 작으니까, 그래서 비트를 왼쪽으로 시프트하는 방식으로 윈도우 사이즈의 최대 크기를 키울 수 있는 방식도 사용하고 있다고 한다. 몇 번 시프트할 지는 옵션 필드의 WSCALE 필드를 사용하여 표기한다고 한다.

### Checksum
* 체크섬은 데이터를 송신하는 중에 발생할 수 있는 오류를 검출하기 위한 값이다.
* 수신 측은 데이터를 받으면 1의 보수를 취하지 않은 값인 10001010까지만 만든 다음, 이 값과 송신 측이 보낸 체크섬을 더해서 모든 비트가 1이라면 이 데이터가 정상이라고 판단할 수 있다.

### Options
* 옵션 필드는 TCP의 기능을 확장할 때 사용하는 필드들이며, 이 필드는 크기가 고정된 것이 아니라 가변적이다.
* 그래서 수신 측이 어디까지가 헤더고 어디서부터 데이터인지 알기 위해 위에서 설명한 데이터 오프셋 필드를 사용하는 것이다.
* 대표적인 옵션으로는 윈도우 사이즈의 최대 값 표현을 확장할 수 있는 WSCALE 있다.

[출처](https://evan-moon.github.io/2019/11/10/header-of-tcp/)
