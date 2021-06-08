# L4, L7 이란?
![](https://avinetworks.com/wp-content/uploads/2018/10/l4-l7-network-services.png)
- OSI 7 layer에서 나오는 용어
- L4는 Transport layer에 해당하고, L7는 Application Layer에 해당한다.
- L4에 해당하는 대표적인 프로토콜은 TCP/UDP
- L7에 해당하는 대표적인 프로토콜은 HTTP

# Load balancer란?
- 트래픽을 분산하여 서버에게 보내, 이용자에게는 적절한 응답속도 및 이용성을 제공하고, 서버 측면에서는 부하를 나눠 부담시켜 가용성을 증대시키는 기술을 의미

## L4 Load balancer
- TCP/UDP 상에서, 분석하여 트래픽을 서버에 분배해서 보내는 것.
    - 그 정보는 IP주소, 포트 번호, MAC주소 전송 프로토콜등이 있음..
    - 보통 port 정보를 바탕으로 트래픽을 분산.
- 만약에 서버 한대에, 각기 다른 포트로 여러 프로그램이 작동 중이고, 이에 트래픽을 분산시키고 싶다면 L4 이상 레벨에서 로드밸런서를 이용해야함.
- 또한, 주로 Round Robin 알고리즘을 사용해서 트래픽을 분배.

![](https://post-phinf.pstatic.net/MjAxOTEyMTBfNCAg/MDAxNTc1OTU1MzY3OTM2.nG91HOEOh6Sc1AuUgbN3O4pcnEI-rh24UKSrrrjkrcsg.VcG18MidW4az7Oh0RQfRPLDBHNRyGayE1BsQxDImL3Ig.JPEG/L4-%EB%A1%9C%EB%93%9C%EB%B0%B8%EB%9F%B0%EC%8B%B1.jpg?type=w1200)

## L7 Load balancer
- HTTP/HTTPS header, 쿠키의 내용으로 트래픽을 특정 서버로 분산 시키는 것.
- application level에서 행해지므로, 여러 정보(헤더, 쿠키...) 들을 기반으로 요청을 나눌 수 있다는 장점이 있음.
    - 요청에 대해 상세한 정보를 기반으로 트래픽을 나눌 수 있기 때문에, 다른 레벨의 로드밸런서보다 좀 더 세분화하여 로드 밸랜싱을 할 수 있다는 장점이 있음.
- 특정 패턴 요청을 무시하거나, Dos/DDoS 같은 비정상적인 트래픽을 필터링하는 것도 이 단계에서 이루어짐.

![](https://post-phinf.pstatic.net/MjAxOTEyMTBfMjA1/MDAxNTc1OTU1MzgxODY5.odnG4CRES0e5bH7sOKyWRP1c8uO_XC4VX9A3HPeI1JQg.lNL2eJYbMz6NX1e5YFzfHDMQHn4YrdOJR2VYHmq5e1Ig.JPEG/L7-%EB%A1%9C%EB%93%9C%EB%B0%B8%EB%9F%B0%EC%8B%B1.jpg?type=w1200)

## L4 vs L7
![](https://media.vlpt.us/images/makeitcloud/post/76db786e-1e41-4d91-aff7-9d3a5f6cde42/image.png)

# 참고
- https://avinetworks.com/glossary/l4-l7-network-services/
- https://nesoy.github.io/articles/2018-06/Load-Balancer
- https://velog.io/@makeitcloud/%EB%9E%80-L4-load-balancer-vs-L7-load-balancer-%EB%9E%80
- https://post.naver.com/viewer/postView.nhn?volumeNo=27046347&memberNo=2521903

---

# QnA

## 1. TCP/IP vs OSI 7 layer
![image](https://user-images.githubusercontent.com/22140570/121183244-d5a9e000-c89e-11eb-8888-2df53fdb6be4.png)

> 참고
- https://www.guru99.com/difference-tcp-ip-vs-osi-model.html
- https://goitgo.tistory.com/25

## 2. 로드 밸런서가 필요한 이유
![image](https://user-images.githubusercontent.com/22140570/121183397-fd00ad00-c89e-11eb-9c00-b8173aa65607.png)

> 참고
- https://newrelic.com/blog/best-practices/importance-local-load-balancing
- https://toma0912.tistory.com/87

## 3. L7 계층해서 사용하는 프로토콜 (HTTP 제외)
![image](https://user-images.githubusercontent.com/22140570/121183492-17d32180-c89f-11eb-820d-8669a9124869.png)

## 4. ARP라는 프로토콜은 어떤식으로 동작하는가?
![image](https://user-images.githubusercontent.com/22140570/121183610-3b966780-c89f-11eb-87bc-5b7d80a22a26.png)
> 참고
- https://www.practicalnetworking.net/series/arp/traditional-arp/
- https://musclebear.tistory.com/12