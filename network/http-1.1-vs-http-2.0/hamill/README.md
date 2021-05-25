# HTTP 1.1란?

HTTP는 TCP 연결 기반 위에서 동작하는 프로토콜입니다. 신뢰성 확보를 위해 연결을 맺고 끊는데 있어서 핸드 쉐이크가 이뤄집니다. 거기에다 HTTP는 비연결성 프로토콜이기 때문에 한 번 연결로 한 번의 요청과 응답을 하고 응답이 끝나면 연결을 끊어 버립니다. 연결을 맺고 끊을 때마다 핸드 쉐이크를 하기 때문에 비연결성 프로토콜에선 오버헤드(어떤 처리를 하기 위해 들어가는 간접적인 처리 시간 및 메모리)가 생깁니다. 특히 요즘 웹서비스들은 하이퍼 텍스트라기보다는 많은 정적 데이터로 이뤄진 하이퍼 미디어로 발전했기 때문에 오버헤드가 커질 수 있습니다. 이런 이유에서 HTTP/1.1 에선 **Keep-alive** 기능이 추가되었는데, 이는 한 번 맺어졌던 연결을 끊지 않고 지속적으로 유지하여 불필요한 핸드 쉐이크를 줄여 성능을 개선하는 것입니다. 이런 식으로 웹페이지가 예전의 텍스트 위주와는 다르게 점점 미디어들이 추가되고 상태(쿠키, 세션 등)를 유지하려는 기술들이 요구되다 보니 성능 개선이 반드시 필요하게 되었고 이를 위해 부가적인 기능들이 추가되다 HTTP/2.0까지 발전하게 된 것입니다.

HTTP/1.1에서 성능 개선을 위해 파이프라이닝이라는 기술이 도입되었습니다. 하나의 커넥션에서 한 번에 순차적인 여러 요청을 연속적으로 하고 그 순서에 맞춰 응답을 받는 방식으로 지연 시간을 줄이는 방법입니다. 순차적으로 데이터를 요청하고 받아야 하다 보니 먼저 받은 요청이 끝나지 않으면 그 뒤에 있는 요청의 처리가 아무리 빨리 끝나도 먼저 온 요청이 끝날 때까지 기다려야 합니다. 이를 HTTP의 HOL(Head Of Line) Blocking 문제라고 하고 파이프라이닝의 큰 문제입니다. 그래서 모던 브라우저들의 대부분은 파이프라이닝을 사용하지 못하도록 막아 놓았습니다. 그래서 HTTP/1.x 로 통신할 때 클라이언트(브라우저)가 병렬 요청을 하기 위해서 6~8개(브라우저마다 다름)의 커넥션을 이용해 데이터를 가져오는 방식으로 성능을 개선하고 있습니다.

**HTTP/1.1의 파이프라이닝**

![](https://www.whatap.io/ko/blog/38/img/http_1_1.png)

**크롬 개발자 도구로 확인한 HTTP/1.1의 데이터 요청 응답 과정에서 6개의 커넥션 ID가 사용됐다.**

![](https://www.whatap.io/ko/blog/38/img/http_1_2.png)


# HTTP/2.0의 바이너리 프레임과 멀티플렉싱

HTTP/2.0의 핵심은 바이너리 프레이밍 계층을 사용해 요청과 응답의 멀티플렉싱을 지원한다는 것입니다. HTTP 메세지를 바이너리 형태의 프레임으로 나누고 이를 전송 후 받은 쪽에서 다시 조립합니다. 요청과 응답이 동시에 이루어지니 하나의 연결에 여러 요청과 응답이 뒤섞여 있습니다. 프레이밍 작업은 서버와 클라이언트(브라우저)에서 해주기 때문에 큰 변경사항을 고려하지 않아도 됩니다. 바이너리 프레이밍과 멀티플렉싱을 이용해 여러 개의 연결 없이 병렬 처리도 할 수 있고 파이프라이닝과 달리 HOL 문제를 해결한 것입니다.

또한 그 외에도 요청 우선순위를 지정할 수 있고 헤더를 압축하여 헤더 오버헤드를 줄이고 클라이언트가 명시적 요청을 하지 않아도 필요한 리소스를 미리 푸시하여 응답 시간을 줄이는 서버 푸시 기능이 추가되었습니다. HTTP/2.0의 발전 과정과 목표, 자세한 기술적인 내용은 [구글 개발자 페이지](https://developers.google.com/web/fundamentals/performance/http2/)에서 볼 수 있습니다.

**바이너리 프레이밍 계층**

![](https://www.whatap.io/ko/blog/38/img/http_2.png)

**Multiplexed Streams (Keep-Alive, Pipelining 개선)**

![](https://t1.daumcdn.net/cfile/tistory/9903B4455BE1592C10)

**Stream 우선 순위 지정 (HOL 문제 해결 방법)**

![](https://t1.daumcdn.net/cfile/tistory/99266D465BE1595F06)

**Server Push (서버는 클라이언트가 요청하지 않은 리소스도 보내줄 수 있음 → 클라이언트 요청 최소화)**

![](https://t1.daumcdn.net/cfile/tistory/99A8B5395BE159C136)

**Header Compression (헤더 압축. Header 정보를 압축)**

- Header 정보를 압축하기 위해 Header Table과 Huffman Encoding 기법을 사용하여 처리
- 이를 HPACK 압축 방식이라 부르며 별도의 명세서(RFC 7531)로 관리
- 아래 그림과 같이 클라이언트가 2 번의 요청을 보낸다고 가정하면 HTTP/1.x의 경우 2 개의 요청 Header에 중복 값이 존재해도 중복된 값을 전송하지만 HTTP/2.0 에선 Header에 중복값이 존재하는 경우 Static/Dynamic Header Table 개념을 사용하여 중복 Header를 검출하고 중복된 Header는 index값만 전송하고 중복되지 않은 Header 정보값은 Huffman Encoding 기법으로 인코딩 처리하여 전송한다.

![](https://t1.daumcdn.net/cfile/tistory/99A736355BE15A1A32)

![](https://t1.daumcdn.net/cfile/tistory/99B9D9425BE15A5824)

### 용어 설명

- 스트림 : 구성된 연결 내에서 전달되는 바이트의 양방향 흐름이며, 하나 이상의 메세지가 전달 될 수 있습니다.
- 메세지 : 논리적 요청 또는 응답 메세지에 매핑되는 프레임의 전체 시퀀스입니다.
- 프레임 : HTTP/2.0에서 통신의 최소 단위이며 각 최소 단위에는 하나의 프레임 헤더가 포함됩니다. 이 프레임 헤더는 최소한으로 프레임이 속하는 스트림을 식별합니다.

### 성능 비교 사이트
[HTTP/ 1.1과 HTTP/2.0의 성능 비교 사이트](https://http1.golang.org/gophertiles?latency=1000)

### 출처

- [https://www.digitalocean.com/community/tutorials/http-1-1-vs-http-2-what-s-the-difference](https://www.digitalocean.com/community/tutorials/http-1-1-vs-http-2-what-s-the-difference)
- [https://developers.google.com/web/fundamentals/performance/http2/](https://developers.google.com/web/fundamentals/performance/http2/)
- [https://www.whatap.io/ko/blog/38/index.html](https://www.whatap.io/ko/blog/38/index.html)
- [https://hun-developer.tistory.com/33](https://hun-developer.tistory.com/33)
- [https://ijbgo.tistory.com/26](https://ijbgo.tistory.com/26)