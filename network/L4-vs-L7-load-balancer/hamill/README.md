# L4 로드밸런서 vs L7 로드밸런서

### 로드밸런싱이란?

부하분산 또는 로드 밸런싱(load balancing)은 컴퓨터 네트워크 기술의 일종으로 둘 혹은 셋 이상의 중앙처리장치 혹은 저장장치와 같은 컴퓨터 자원들에게 작업을 나누는 것을 의미합니다. 이로써 가용성 및 응답시간을 최적화 시킬 수 있습니다.

### 로드밸런서가 필요한 이유

Client가 1~2명인 경우 Server는 사용자가 원하는 결과를 여유롭게 응답 해 줄 수 있지만, Client가 수 천만명이라면 Server는 모든 사람에게 응답해주려고 노력하다가 결국엔 지쳐버려 동작을 멈추게 됩니다.

이러한 문제를 해결하기 위해서 Scale-up, Scale-out 2 가지 방법이 있습니다. 스케일업은 Server가 더 빠르게 동작하기 위해 하드웨어 성능을 올리는 방법이고, 스케일아웃은 하나의 Server 보다는 여러 대의 Server가 일을 나눠서 하는 방법입니다.

스케일아웃의 장점은 하드웨어를 향상하는 비용보다 서버 한 대 추가하는 비용이 더 적고, 여러 대의 Server 덕분에 무중단 서비스를 제공 할 수 있다는 것입니다. 이런 스케일아웃 방법에서 Server에게 균등한 Traffic이 가도록 분산시켜주는 게 로드밸런서입니다.

### L4 로드밸런서

L4 로드밸런싱은 Network Load Balancing (NLB)라고도 하며, OSI 7 계층 중 L4(TCP/UDP)에서 로드밸런싱을 합니다. IP와 포트 정보를 보고 정해진 정책에 따라 라우팅 해주게 됩니다. 클라이언트의 데이터에 관계없이 패킷 레벨에서만 로드를 분산하기 때문에 빠르고 효율적이라는 장점을 가지고 있습니다.

![https://user-images.githubusercontent.com/15958325/89248626-2cc1a180-d64b-11ea-958a-553db05fb74e.png](https://user-images.githubusercontent.com/15958325/89248626-2cc1a180-d64b-11ea-958a-553db05fb74e.png)

L4 로드밸런서는 tls/ssl termination 의 유무에 따라 크게 2가지 디자인이 있습니다.


> tls/ssl termination 이란?
> 트래픽을 암호화 및 암호 해독하는 컴퓨팅 집약적인 작업으로부터 > 백엔드 서버를 해방하기 위한 작업 ex) clinet와 LB 사이의 개방된 네트워크에서는 HTTPS 통신, LB와 server 사이는 HTTP 통신


1. TCP/UDP Termination 로드 밸런서
    - 구현이 쉽다
    - 낮은 지연시간
    - tls termination 기능을 LB 가 수행함
2. TCP/UDP Passthrough 로드 밸런서
    - tls termination 을 LB가 수행하지 않음
    - 대신 연결추적, NAT 를 수행한 뒤 각 연결에 대한 패킷이 선택된 서버로 전달
        - 연결추적: 활성 TCP 연결 상태를 추적함. handshake가 완료되었는지, FIN이 수신되었는지, 연결 유휴 상태가 얼마나 오래되었는지, 연결에 대해 어떤 백엔드를 선택했는지 등의 데이터를 포함한다.
        - NAT: 패킷이 로드밸런서를 통과할 때 패킷의 IP/Port 정보를 변경
    - tls termination을 사용하지 않으므로 LB에 가해지는 부하가 적어짐. termination LB 보다 훨씬 많은 수의 액티브 연결 및 초 당 패킷(PPS)를 처리 가능하고 DSR(Direct Server Return) 사용이 가능해짐.


#### * DSR 이란?
Client에서 Server로 갈 때는 로드 밸런서를 통해 가지만, Server에서 Client로 응답을 줄 땐 로드밸런서를 거치지 않고 바로 가는 방식을 뜻합니다.

- Passthrough LB 를 기반으로 함
- 전형적인 HTTP 요청과 응답 패턴에서 10% 정도가 요청이고 90% 정도가 응답이라고 할 수 있음. DSR 방식을 사용한다면 LB의 부하를 획기적으로 줄일 수 있음
- NAT 대신 LB에서는 일반적으로 GRE(Generic Routing Encapsulation)을 사용하여 LB에서 Server로 가는 IP 패킷을 캡슐화함. Server에서는 캡슐을 분해해서 Client의 IP와 TCP 포트정보를 얻을 수 있음
- 가장 중요한 건 Server가 로드밸런싱에 참여한다는 것임. Server에 GRE 터널이 올바르게 구성되어 있어야 함


### L7 로드밸런서

L7 로드밸런싱은 Application Load Balancing (ALB) 라고도 하며, OSI 7 계층 중 L7을 기준으로 로드밸런싱하는 역할을 합니다. IP와 포트 정보뿐만 아니라 패킷의 URL 정보, 쿠키, payload 정보들을 보고 정해진 정책에 따라 라우팅해주게 됩니다. 하위 로드밸런서보다 세부적인 로드밸런싱이 가능하다는 것이 특징입니다.

![https://user-images.githubusercontent.com/15958325/89260203-c64a7c80-d666-11ea-8b8f-e053087ea887.png](https://user-images.githubusercontent.com/15958325/89260203-c64a7c80-d666-11ea-8b8f-e053087ea887.png)

마이크로 서비스 간의 통신은 보통 HTTP를 사용합니다. 그래서 HTTP 기반 로드밸런서는 이러한 통신을 쉽게 처리 할 수 있다는 장점도 있습니다. 또한, DOS 같은 비정상적인 트래픽을 필터링 할 수 있어 안전성이 높습니다. 하지만, 패킷 내용을 복화하여 처리해야 하므로 부하가 많이 걸릴 수 있습니다.

### 로드밸런서가 Server를 선택하는 알고리즘 종류

1. Round Robin : 서버에 들어온 요청을 순서대로 돌아가면서 배정하는 방식
2. Weighted Round Robin : 각 서버에 가중치를 매기고, 가중치가 높은 서버에 우선적으로 요청을 배분하는 방식
3. IP Hash: 클라이언트의 IP 주소를 특정 서버로 매핑하여 요청을 처리. 클라이언트가 항상 동일한 서버로 연결되는 것을 보장하는 방식
4. Least Connetions : 연결 개수가 가장 적은 서버를 선택하는 방식. 트래픽으로 인해 세션이 길어지는 경우 권장하는 방식
5. Least Response Time : 서버의 현재 연결 상태와 응답 시간을 모두 고려하여 트래픽을 배분하는 방식

### 로드밸런서가 장애가 났을 경우 시나리오

로드 밸런서를 이중화하여 장애를 대비 할 수 있습니다.

![https://nesoy.github.io/assets/posts/20180602/7.png](https://nesoy.github.io/assets/posts/20180602/7.png)

![https://nesoy.github.io/assets/posts/20180602/8.gif](https://nesoy.github.io/assets/posts/20180602/8.gif)

1. 이중화된 로드 밸런서들은 서로 Health Check를 합니다. 
2. 메인 로드 밸런서가 동작하지 않으면 가상 IP(VIP, Virtual IP)는 여분의 로드 밸런서로 변경됩니다. 
3. 여분의 로드 밸런서로 운영하게 됩니다. 

### 출처

- [https://ko.wikipedia.org/wiki/부하분산](https://ko.wikipedia.org/wiki/%EB%B6%80%ED%95%98%EB%B6%84%EC%82%B0)
- [https://nesoy.github.io/articles/2018-06/Load-Balancer](https://nesoy.github.io/articles/2018-06/Load-Balancer)
- [https://avinetworks.com/glossary/l4-l7-network-services/](https://avinetworks.com/glossary/l4-l7-network-services/)
- [https://gruuuuu.github.io/network/lb01/](https://gruuuuu.github.io/network/lb01/)