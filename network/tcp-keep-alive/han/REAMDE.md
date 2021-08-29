# TCP keepalive란 무엇이고 왜 필요할까?

- TCP 연결을 맺는 과정에서, 3 way handshake 해야 한다.
- 그런데 주기적으로 연결을 맺어야 하는데, 매번 이러한 과정을 거쳐가야하는 것은 불필요하다고 볼 수 있음.
- 그래서 처음 맺은 연결(세션)을 그대로 사용할 수는 없을까?
- 이 결과 나온 것이 `keepalive`



## TCP keepalive 패킷 흐름

![](https://jihooyim1.gitbooks.io/linuxbasic/content/contents/img/flow_packets_in_TCP_keepalive.png)

- TCP keepalive 환경에서, 세션이 일단 맺어져 있으면, 일정 시간이 지나면 패킷을 보내서 연결이 살아있는지 확인한다.
- 이러한 확인은 클라이언트, 서버 둘 중에 어느 곳에서라든지 할 수 있다.
  - 즉 클라이언트, 서버 중에 한 곳에서만이라도 `keepalive` 를 사용하면 해당 기능이 유지된다.



## 현재 사용하고 있는 네트워크 소켓 확인하기

- `netstat -napo`

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

- Timer 항목에서 확인할 수 있고
- state ESTABLISHED인 소켓에서 keepalive 상태고, 약 70초 정도 남아있음을 알려줌





## TCP keepalive 파라미터

- TCP Keepalive를 유지하는데 필요한 커널 파라미터들
- 총 3개 있음.

```
root@server1:~# sysctl -a |grep -i keepalive

net.ipv4.tcp_keepalive_time = 240 (1)
net.ipv4.tcp_keepalive_probes = 3 (2)
net.ipv4.tcp_keepalive_intvl = 30 (3)
```

- **net.ipv4.tcp_keepalive_time**
  -  keepalvie 소켓의 유지시간을 의미.
-  **net.ipv4.tcp_keepalive_probes**
  - keepalive패킷을 보낼 최대 전송 횟수를 정의
  - keepalive 패킷은, 이제 세션이 연결되어있는지 확인하는 패킷
  - 이 패킷에 한번 응답하지 않았다고 바로 끊진 않고, 여기에 정의된 횟수 만큼 시도해보고
  - 응답이 없으면 연결을 끊는 과정을 거친다.
-  **net.ipv4.tcp_keepalive_intvl**
  - keepalive 재전송 패킷을 보내는 주기
  - keepalive패킷을 보냈는 데, 해당 패킷에 대해 응답이 없을 때 몇초 후 다시 패킷을 보낼 것인가 정의하는 것을 의미한다.



## TCP keepalive와 HTTP keepalive

- TCP keepalive는 두 종단(TCP) 간의 연결을 유지하기 위함이지만, 
- HTTP keepalive는 최대한 연결을 유지하는 것이 목적이다.

- `apache`, `nginx`와 같은 웹 애플리케이션에도 keepalive timeout 이라는 것이 존재
  - HTTP/1.1에서 지원하는 keepalive를 위한 설정 항목
  - https://hodolman.com/16
- 위 두개의 값이 모두 60이라하면..
  - TCP keepalive는 60초 간격으로 연결이 유지되어있는지 확인하고, keepalive 패킷에 응답이 없으면 연결을 끊는다
  - HTTP keepalive는 60초 동안 연결을 유지하고, 60초 지난 후에도 요청이 없으면 연결이 끊는다.
- etc
  - [Tomcat](https://ehdvudee.tistory.com/30)



## 참고

- https://jihooyim1.gitbooks.io/linuxbasic/content/contents/08.html
- https://devidea.tistory.com/60



# Java, Spring connection

- Tomcat과 통신할 때,
- Java URL 클래스를 사용할 때, 커넥션이 어떻게 관리되는지,
- Spring restTemplate을 사용할 때, 커넥션도 다르게 관리되는듯.
- 보통 HTTP 스펙내에서 connetion들이 설정되는 듯



## 참고

- https://taes-k.github.io/2019/11/27/spring-java-connections/

