# Blocking vs Non-blocking
## Synchronous vs Asynchronous
### Synchronous
* 동기는 두 가지 이상의 작업이 서로 시간을 맞추어 행동하는 것을 의미한다.
* 호출한 함수가 다른 호출한 함수의 작업이 끝나 결과값을 반환하기를 기다리거나, 지속적으로 호출된 함수에게 확인 요청을 하는 경우가 있다.
* 작업 A와 작업 B가 있다고 가정하자.
    * 작업 A와 작업 B의 시작 시간과 종료 시간이 같으면 동기라고 볼 수 있다.
        * 쓰레드 A, 쓰레드 B가 동시에 작업을 시작하고 종료하는 경우
        * 메소드 리턴 시간 A와 결과를 전달받는 시간 B가 일치한 경우
    * 작업 A가 끝나는 시간과 작업 B가 시작하는 시간이 같으면 동기라고 볼 수 있다.

### Asynchronous
* 비동기는 두 가지 이상의 작업을 할 때 대상들이 서로의 시간을 맞추지 않고 행동하는 것을 의미한다.
* 호출하는 함수가 호출되는 함수에게 작업을 맡겨놓고 더 이상 신경을 쓰지 않는 경우가 있다.

### Blocking vs Non-Blocking
* 블록킹과 논블록킹은 직접 제어할 수 없는 대상을 어떻게 처리할 것인가에 따라 나눠지는 개념이다.
* 직접 제어할 수 없는 대상으로는 I/O, 멀티 쓰레드 동기화 등이 있다.

#### Blocking
* 블록킹은 직접 제어할 수 없는 대상의 작업이 끝날 때까지 제어권을 넘겨주지 않는 것이다.
* 예를 들어 호출하는 함수가 I/O를 요청했을 때 I/O 처리가 완료될 때까지 아무 일도 하지 못한 채 기다리는 것을 말한다.

#### Non-Blocking
* 논블록킹은 직접 제어할 수 없는 대상의 작업 처리 여부와 무관하게 처리하는 것을 의미한다.
* 예를 들어 호출하는 함수가 I/O를 요청했을 때 I/O 처리 여부와 상관없이 바로 자신의 작업을 할 수 있는 것을 말한다.

#### Synchronous Blocking I/O
![](https://i.imgur.com/v6uOhUA.png)
```java=
device = IO.open()
// 이 Thread는 데이터를 읽을 때까지 아무 일도 할 수 없음
data = device.read()
print(data)
```
* Synchronous: 애플리케이션의 ```read()``` 메소드가 리턴하는 시간과 커널에서 결과를 가져오는 시간이 일치한다.
* Blocking: 커널의 작업이 완료될 때까지 대기한다.

#### Synchronous Non-Blocking I/O
![](https://i.imgur.com/BRrD3I6.png)
```python=
device = IO.open()
ready = False
while not ready:
    print("There is no data to read!")

    # 다른 작업을 처리할 수 있음

    # while 문 내부의 다른 작업을 다 처리하면 데이터가 도착했는지 확인한다.
    ready = IO.poll(device, IO.INPUT, 5) 
data = device.read()
print(data)
```
* Synchronous: read() 메서드(애플리케이션)가 리턴하는 시간과 커널에서 결과를 가져오는 시간이 일치한다.
* Non-Blocking: 애플리케이션으로부터 요청을 받은 커널은 작업 완료 여부와 상관없이 바로 반환하여 제어권을 애플리케이션에게 넘겨준다. 커널의 작업이 완료되면 작업 결과를 애플리케이션에게 반환한다.
* 대표적인 예로는 멀티플랙싱을 수행하는 select(), epoll() 함수가 있다.
* Application에서 I/O를 요청 후 바로 return되어 다른 작업을 수행하다가 특정 시간에 데이터가 준비가 다되었는지 상태를 확인한다. 데이터의 준비가 끝날 때까지 틈틈이 확인을 하다가 완료가 되었으면 종료된다.
* 여기서 주기적으로 체크하는 방식을 폴링(Polling) 이라고 한다. 그러나 이러한 방식은 작업이 완료되기 전까지 주기적으로 호출하기 때문에 불필요하게 자원을 사용하게 된다.

#### Asynchronous Non-Blocking I/O (AIO)
![](https://i.imgur.com/GrI8VfR.png)
```python=
ios = IO.IOService()
device = IO.open(ios)

def inputHandler(data, err):
    "Input data handler"
    if not err:
        print(data)

device.readSome(inputHandler)
# 이 thread는 데이터가 도착했는지 신경쓰지 않고 다른 작업을 처리할 수 있다.
ios.loop()
```
* Asynchronous: readSome() 메서드(애플리케이션)가 리턴하는 시간과 커널에서 결과를 가져오는 시간이 일치하지 않는다.
* Non-Blocking: 애플리케이션으로부터 요청을 받은 커널은 작업 완료 여부와 상관없이 바로 반환하여 제어권을 애플리케이션에게 넘겨준다. 작업이 끝나면 애플리케이션에게 시그널 또는 콜백을 보낸다.
* 대표적인 예로는 윈도우에서 멀티플랙싱을 수행하는 IOCP가 있다.
* I/O 요청을 한 후 Non-Blocking I/O와 마찬가지고 즉시 리턴된다. 허나, 데이터 준비가 완료되면 이벤트가 발생하여 알려주거나, 미리 등록해놓은 callback을 통해서 이후 작업이 진행된다. 이전 두 I/O의 문제였던 Blocking이나 Polling이 없기 때문에 자원을 보다 더 효율적으로 사용할 수 있다.

#### Asynchronous Blocking
* 비동기이면서도 블록킹을 하는 경우는 비효율적이므로 자주 사용되지 않는다고 한다.
* 하지만 Asynchronous Non-Blocking 모델 중에서 Blocking 형태로 동작하는 모델이 있는 경우 의도와 다르게 Asynchrnous Blocking으로 동작하는 경우가 있다고 한다.
* 예를 들어, Node.js는 비동기로 작업하려고 하지만 MySQL 드라이버가 Blocking 방식으로 동작하므로 어쩔 수 없이 Asynchronous Blocking 방식으로 동작한다고 한다.

#### 본격 Case Study : 박 상병님, 저희 짬통 언제 버리러 가나요?
* Blocking & Synchronous
```
김 일병 : 박 상병님, 오늘 짬통 언제 버리러 갑니까?
박 상병 : 있어봐, 중대 운전병에게 물어볼게.
김 일병 : (...)
대표님 : (수고하십니다 XX대대 상병 박공군입니다)
김 일병 : (박 상병님 전화 받고 있는 것을 수화기를 들고 같이 듣고 있음)
이 병장: 김 일병, 언제 버리러 가는거야?
김 일병: (수화기로 전화 내용을 듣고 있으며 말한다) 지금 전화 중이십니다. 잠시만 기다려주십시오.
이 병장: 오오 알겠어.
```
* Blocking & Asynchronous
```
김 일병 : 박 상병님, 오늘 짬통 언제 버리러 갑니까?
박 상병 : 있어봐, 중대 운전병에게 물어볼게.
김 일병 : (...)
대표님 : (수고하십니다 XX대대 상병 박공군입니다)
김 일병 : (과정 지켜봄.. 궁금함.. 계속 서 있음)
이 병장: 김 일병, 언제 버리러 가는거야?
김 일병: (아무런 일도 하지 않으며 말한다) 잘 모르겠습니다. 뭐 전화오면 박 상병님께서 알려주시지 않겠습니까?
이 병장: 흠.. 폐급인가?
```

* Non-blocking & Synchronous
```
김 일병 : 박 상병님, 오늘 짬통 언제 버리러 갑니까?
박 상병 : 있어봐, 중대 운전병에게 물어볼게.
김 일병 : 넵!
박 상병 : (수고하십니다 XX대대 상병 박공군입니다)
김 일병 : 연락 되셨습니까?
박 상병 : 이병이 받던데. 좀 이따 해볼게.
김 일병 : 연락 되셨습니까?
박 상병 : 운전병 제초하러 갔대. 나중에 전화준댄다.
이 병장 : 김 일병, 언제 버리러 간대?
박 상병 : 하.. 조금 이따 연락 주신다고 하는데 저도 진짜 궁금합니다.
이 병장 : 그래? 알겠어.
김 일병 : 연락 되셨습니까?
박 상병 : ㅅㅂ 왜 자꾸 물어보냐 너 폐급이냐? 아직 전화 안왔다니까;
```

* Non-blocking & Asynchronous
```
김 일병 : 박 상병님, 오늘 짬통 언제 버리러 갑니까?
박 상병 : 있어봐, 중대 운전병에게 물어볼게.
김 일병 : 넵!
박 상병 : (수고하십니다 XX대대 상병 박공군입니다)
박 상병 : 오늘 9시에 간댄다. 준비하자.
김 일병 : 넵! 짬통 일병들이랑 옮기고 있겠습니다.
```

#### 정리
* Blocking/NonBlocking
    * Blocking/NonBlocking은 호출되는 함수가 바로 리턴하느냐 마느냐가 관심사다.
    * 호출된 함수가 바로 리턴해서 호출한 함수에게 제어권을 넘겨주고, 호출한 함수가 다른 일을 할 수 있는 기회를 줄 수 있으면 NonBlocking이다.
    * 그렇지 않고 호출된 함수가 자신의 작업을 모두 마칠 때까지 호출한 함수에게 제어권을 넘겨주지 않고 대기하게 만든다면 Blocking이다.

* Synchronous/Asynchronous
    * Synchronous/Asynchronous는 호출되는 함수의 작업 완료 여부를 누가 신경쓰냐가 관심사다.
    * 호출되는 함수에게 callback을 전달해서, 호출되는 함수의 작업이 완료되면 호출되는 함수가 전달받은 callback을 실행하고, 호출하는 함수는 작업 완료 여부를 신경쓰지 않으면 Asynchronous다.
    * 호출하는 함수가 호출되는 함수의 작업 완료 후 리턴을 기다리거나, 또는 호출되는 함수로부터 바로 리턴 받더라도 작업 완료 여부를 호출하는 함수 스스로 계속 확인하며 신경쓰면 Synchronous다.

### 추가 설명
* Blocking 방식은 요청하고 응답 올때까지 기다리는 방식이고, Non-Blocking 방식은 요청하고 딴 일하다 나중에 응답신호가 오면 결과를 읽어 처리하는 방식이다.
* 동기방식은 요청자와 제공자사이에 계속 Connection이 맺어져 있어야 하고, 비동기방식은 Connection은 끊어지고 서로간에 이벤트를 통해 통신하는 방식이다.
* 비동기방식은 요청자와 제공자 사이에 Message Broker라는 또 다른 서비스가 중계해주지만, 동기 방식은 요청자 어플리케이션에 비동기 처리를 하는 로직이 있다.
* 최근 흔히 사용하는 REST API는 동기 방식이고 보통 Non-Blocking방식으로 통신한다.
* Frontend와 backend사이는 거의 대부분 동기+non-blocking 방식으로 구현한다.
* 비동기방식 패턴 중 제일 대표적인 것이 Publish/Subscribe (또는 Producer-Consumer) 패턴이다.
    * Publisher인 유튜버들은 구독을 요청할 수 있는 채널을 만들고, 그 채널에 동영상을 계속 publishing해둔다.
    * Subscriber인 사용자가 특정 유튜브채널을 구독 신청하면 새로운 동영상이 업로드 될때 첫화면에 추천동영상으로 받아보거나 모바일앱 알림 등으로 받아 볼 수 있게 된다.
    * 즉, 사용자는 구독만 해놓고 있으면 새 동영상 올라올때까지 Youtube에 머물러 있을 필요가 없다.
    * 비동기 메시징 방식은 추후 CS 스터디 주제로 발제할 생각이다.
    * 이런 비동기 방식을 사용하는 이유는 큰 서비스를 이루는 마이크로서비스 중 하나가 실패해도 전체적인 서비스는 그대로 진행되도록 하기 위함이다.
        * 예를 들어 쇼핑몰 상품 주문 시 주문서비스, 결제서비스, 배송서비스가 필요한데 동기방식으로 하면 결제나 배송 서비스에 문제가 생기면 상품 주문은 실패하게 된다.
        * 그러나 비동기방식을 사용하면 Queue에 처리해야할 메시지가 있기 때문에 일시적 장애가 발생해도 복구가 되면 처리 가능하다. 따라서 상품 주문은 어떠한 경우에도 정상적으로 처리될 수 있게 된다. 
* 일반적으로 Front와 Backend 서비스간에는 동기방식을 사용하고, backend의 서비스들간에는 비동기방식을 사용한다. 
* 또한 데이터 조회 서비스는 동기방식을 사용하고, 데이터 변경 서비스는 비동기방식을 사용한다.

### Spring WebFlux란 무엇일까?
* Non-blocking을 통해서 적은 수의 리소스로 동시성을 다룰 수 있게 해주는 프레임워크이다.
* 우리가 Spring MVC와 RDBMS를 사용하고 있으면 Blocking I/O를 사용하고 있는 것이다.
* Application에서 I/O 요청을 한 후 완료되기 전까지는 Application이 Block이 되어 다른 작업을 수행할 수 없다. 이는 해당 자원이 효율적으로 사용되지 못하고 있음을 의미한다.
* 물론, Blocking 방식임에도 불구하고 마치 Block이 안된듯이 동작하는 것처럼 보인다. 이는 멀티 쓰레드 기반의 방식이기 때문이다. 정확히는, 하나의 쓰레드가 블록되는 순간 다른 쓰레드가 동작하도록 함으로써 블록 문제를 해결하였다.
* 하지만 쓰레드 간의 Context Switching 등의 비용이 존재하므로, 여러 개의 I/O를 동시다발적으로 효율적인 처리를 하려면 논블로킹으로 처리해야만 한다.

#### Blocking I/O 예시: RestTemplate
* RestTemplate은 Multi-Thread와 Blocking방식을 사용한다.
* Thread pool은 요청자 어플리케이션 구동시에 미리 만들어 놓는다. 
* Request는 먼저 Queue에 쌓이고 가용한 스레드가 있으면 그 스레드에 할당되어 처리된다.
* 즉, 1 요청 당 1 스레드가 할당됩니다.
* 각 스레드에서는 Blocking방식으로 처리되어 응답이 올 때까지 그 스레드는 다른 요청에 할당될 수 없다.
* 아래는 RestTemplate을 Connection Pool에 Spring Bean으로 등록하기 위한 예제이다.
    * 요청 당 20개의 RestTemplate client를 만들고, 최대 50개까지 증가할 수 있도록 했다. 

```java=
@Configuration
public class RestTemplateConfig {
	public RestTemplate getRestTemplate(int defaultMaxPerRoute, int maxTotal) {
		PoolingHttpClientConnectionManager connManager = new PoolingHttpClientConnectionManager();
		connManager.setDefaultMaxPerRoute(defaultMaxPerRoute);
		connManager.setMaxTotal(maxTotal);

		HttpClient client = HttpClientBuilder.create().setConnectionManager(connManager).build();

		HttpComponentsClientHttpRequestFactory factory = new HttpComponentsClientHttpRequestFactory(client);
		factory.setConnectTimeout(3000);
		factory.setReadTimeout(3000);

		return new RestTemplate(factory);

	}

	@Bean
	public RestTemplate coffeeRestTemplate() {
		return getRestTemplate(20, 50);
	}
}
```
* 요청을 처리할 스레드가 있으면 아무런 문제가 없지만, 스레드가 다 차는 경우 이후의 요청은 Queue에 대기하게 된다. 
* 대부분의 문제는 네트워킹이나 DB와의 통신에서 생기는데 이런 문제가 여러 스레드에서 발생하면 가용한 스레드수가 현저하게 줄어들게 되고, 결국 전체 서비스는 매우 느려지게 된다.
* RestTemplate을 이용하여 API 중 응답 시간이 약 3초 정도 걸리는 요청을 3번 반복하는 예제를 제시한다.
```java=
@Test
public void blocking() {
    final RestTemplate restTemplate = new RestTemplate();

    final StopWatch stopWatch = new StopWatch();
    stopWatch.start();

    for (int i = 0; i < 3; i++) {
        final ResponseEntity<String> response =
                restTemplate.exchange(THREE_SECOND_URL, HttpMethod.GET, HttpEntity.EMPTY, String.class);
        assertThat(response.getBody()).contains("success");
    }

    stopWatch.stop();

    System.out.println(stopWatch.getTotalTimeSeconds());
}
```
* Spring의 HTTP 요청 라이브러리인 RestTemplate을 사용하여 3초가 걸리는 API를 3번 호출하였다. 결과는 9.xx초가 나온다. 이유는 I/O가 요청 중일 때에는 아무 작업도 할 수 없기 때문이다.

#### Non-Blocking I/O: WebClientSpring WebClient
* Spring WebClient는 Single Thread와 Non-Blocking방식을 사용한다.
* Core 당 1개의 Thread를 이용한다. 
![](https://i.imgur.com/7MP2vt0.png)


[그림출처](https://luminousmen.com/post/asynchronous-programming-blocking-and-non-blocking)

* 각 요청은 Event Loop내에 Job으로 등록이 된다.
* Event Loop는 각 Job을 제공자에게 요청한 후, 결과를 기다리지 않고 다른 Job을 처리한다. Event Loop는 제공자로부터 callback으로 응답이 오면, 그 결과를 요청자에게 제공한다.
* WebClient는 이렇게 이벤트에 반응형으로 동작하도록 설계되었다고 한다.
* 아래 예제는 위에서 제시한 요청시간이 약 3초 정도 걸리는 동일한 API를 가져왔다고 가정한 것이다.
```java=
@Test
public void nonBlocking3() throws InterruptedException {
    final StopWatch stopWatch = new StopWatch();
    stopWatch.start();
    for (int i = 0; i < LOOP_COUNT; i++) {
        this.webClient
                .get()
                .uri(THREE_SECOND_URL)
                .retrieve()
                .bodyToMono(String.class)
                .subscribe(it -> {
                    count.countDown();
                    System.out.println(it);
                });
    }

    count.await(10, TimeUnit.SECONDS);
    stopWatch.stop();
    System.out.println(stopWatch.getTotalTimeSeconds());
}
```
* WebFlux에서 제공하는 WebClient를 사용해서 위와 동일하게 3초가 걸리는 API를 호출하였다. for문 안의 변수인 LOOP_COUNT는 100으로 코드상에서 설정되어있다. 3초 걸리는 API를 100번 호출한다 하더라도 3.xx초 밖에 걸리지 않는다.
* 만약, Blocking을 위처럼 많은 요청을 동시에 처리하려면 그 만큼의 Thread이 생성되어야 한다. 그러나 이렇게 처리한다 해도 Context Swiching에 의한 오버헤드가 존재할 것이다.

### 성능비교
* 아래는 RestTemplate을 사용하는 Spring Boot1과 WebClient를 사용하는 Spring Boot2의 성능비교 결과이다.
* ![](https://i.imgur.com/ACQsu6g.png)
 
* 1000명까지는 비슷하지만 동시사용자가 늘수록 RestTemplate은 급격하게 느려지는것을 볼 수 있다. 
[그림출처](https://alwayspr.tistory.com/44)
* Spring 커뮤니티에서는 RestTemplate을 이미 Depreciated시키고 WebClient를 사용할것을 강력히 권고하고 있다.
```
NOTE: As of 5.0 this class is in maintenance mode, 
with only minor requests for changes and bugs to be accepted going forward. 
Please, consider using the org.springframework.web.reactive.client.WebClient 
which has a more modern API and supports sync, async, and streaming scenarios.
```

## 참고한 링크
* https://velog.io/@codemcd/Sync-VS-Async-Blocking-VS-Non-Blocking-sak6d01fhx
* https://devahea.github.io/2019/04/21/Spring-WebFlux%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%A0%81%EC%9D%80-%EB%A6%AC%EC%86%8C%EC%8A%A4%EB%A1%9C-%EB%A7%8E%EC%9D%80-%ED%8A%B8%EB%9E%98%ED%94%BD%EC%9D%84-%EA%B0%90%EB%8B%B9%ED%95%A0%EA%B9%8C/
* https://happycloud-lee.tistory.com/220
* https://happycloud-lee.tistory.com/154?category=902418
* https://musma.github.io/2019/04/17/blocking-and-synchronous.html
* http://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/
