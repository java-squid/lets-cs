# 네트워크: CORS란 무엇인가
## CORS: Cross-Origin Resource Sharing
### Origin
* URL 구조에서 Protocol + Host + Port를 합친 것을 의미함
```console
location.source
> https://hackmd.io
```
* 간단한 퀴즈: 다음 중 같은 출처가 아닌 것은?
    * ```https://hackmd.io/about```: 같은 출처
    * ```https://hackmd.io:32/about```: 다른 출처(Port 다름)
    * ```http://hackmd.io```: 다른 출처(Protocol 다름)

### Same-Origin Policy
* Postman으로 API를 호출하면 잘 되는데, 브라우저로 호출하면 CORS 에러가 나는 이유는 무엇일까?
* 브라우저가 동일 출처 정책(Same Origin Policy)를 준수하기 때문에 그렇다.
* 예를 들어 hackmd.io 사이트에서 medium.com의 API를 호출하면 서로 다른 출처이므로 CORS 에러가 발생할 수 있다.
* 동일 출처 정책은 XSS나 XSRF 등의 보안 취약점을 노린 공격을 방어할 수 있다.
* 하지만 외부 리소스를 사용해야 하는 경우는 매우 많다. SOP 정책의 예외 사항이 CORS라고 할 수 있다.

### CORS 동작 원리
* Simple Request와 Preflight Request가 있지만, 주로 Preflight Request 방식이 선호된다.

![](https://i.imgur.com/Wv9msLm.png)
* Preflight 요청은 실제 리소스를 요청하기 전에 OPTIONS라는 메서드를 통해 실제 요청을 전송할지 판단한다.
* OPTIONS 메서드로 서버에 예비 요청을 먼저 보내고, 서버는 이 예비 요청에 대한 응답으로 Access-Control-Allow-Origin 헤더를 포함한 응답을 브라우저에 보낸다.
* 브라우저는 단순 요청과 동일하게 Access-Control-Allow-Origin 헤더를 확인해서 CORS 동작을 수행할지 판단한다.

### CORS 에러 해결 방법
* 서버에서 ```Access-Control-Allow-Origin``` 헤더를 포함한 응답을 브라우저에 보내는 방식으로 CORS 에러를 해결할 수 있다.
* Node.js 기준으로 cors라는 라이브러리가 있고, Spring Boot에서는 WebMvcController를 설정해주면 된다.

#### Access-Control-Allow-Origin: <origin> | *
* 헤더가 작성된 링크만 브라우저가 접근할 수 있도록 설정할 수 있다.
* 사용 예시
```
Access-Control-Allow-Origin: https://medium.com
```
```
Access-Control-Allow-Origin: * // 와일드 카드를 쓰면 모든 사이트 다 된다
```
* 서버에서 아래와 같이 응답 헤더를 설정해서 보내주면 된다.
```javascript=
router.options('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', '*')
  res.send()
})
router.put('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', '*')
  res.send()
})
```
#### Access-Control-Allow-Methods: <method>[, <method>]*
* 리소스 접근을 허용하는 HTTP 메서드를 지정해 주는 헤더이다.
```javascript=
router.options('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', '*')
  res.set('Access-Control-Allow-Methods', req.get('Access-Control-Request-Method'))
  res.send()
})
router.put('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', '*')
  res.send()
})
```
#### Access-Control-Expose-Headers: <header-name>[, <header-name>]*
* 서버에서 응답 헤더에 Access-Control-Expose-Headers를 추가해 줘야 브라우저의 자바스크립트에서 헤더에 접근할 수 있다.
```javascript=
router.options('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', '*')
  res.set('Access-Control-Allow-Methods', req.get('Access-Control-Request-Method'))
  res.send()
})
router.put('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', '*')
  res.set('Access-Control-Expose-Headers', 'X-Custom-Beomy')
  res.set('X-Custom-Beomy', 'Bemoy')
  res.send()
})
```

#### Access-Control-Allow-Headers: <header-name>[, <header-name>]*
* 브라우저에서 보내는 요청 헤더에 포함된 Access-Control-Request-Headers 헤더에 대한 응답 결과이다.
```javascript=
router.options('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', '*')
  res.set('Access-Control-Allow-Methods', req.get('Access-Control-Request-Method'))
  res.set('Access-Control-Allow-Headers', req.get('Access-Control-Request-Headers'))
  res.send()
})
router.put('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', '*')
  console.log(req.get('X-Custom-Request')) // Beomy
  res.send()
})
```
#### Access-Control-Max-Age: <delta-seconds>
* preflight 요청 결과를 캐시 할 수 있는 시간을 나타낸다.
* 아래와 같이 초 단위로 캐시 시간을 설정한다.
```
Access-Control-Max-Age: 60
```
* 위의 코드는 60초 동안 preflight 요청을 캐시 하는 코드이다.
* 60초 동안 OPTIONS 메서드를 사용하는 예비 요청을 보내지 않습니다.
```javascript=
router.options('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', '*')
  res.set('Access-Control-Allow-Methods', req.get('Access-Control-Request-Method'))
  res.set('Access-Control-Max-Age', 60)
  res.send()
})
router.put('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', '*')
  res.send()
})
```
#### Access-Control-Allow-Credentials: true
* 자바스크립트 요청에서 credentials가 include일 때 요청에 대한 응답을 할 수 있는지를 나타낸다.
* false로 설정해 주고 싶을 경우에는 헤더를 생략하면 된다.
```javascript=
router.options('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', 'https://www.google.com')
  res.set('Access-Control-Allow-Methods', req.get('Access-Control-Request-Method'))
  res.set('Access-Control-Allow-Credentials', true)
  res.send()
})
router.put('/cors', (req, res, next) => {
  res.set('Access-Control-Allow-Origin', 'https://www.google.com')
  res.set('Access-Control-Allow-Credentials', true)
  res.send()
})
```
* Access-Control-Allow-Credentials: true 추가 뿐만 아니라, Access-Control-Allow-Origin 헤더도 와일드카드가 아닌 출처를 명시해주어야 한다.

## 프록시 서버
* 프론트엔드와 백엔드 사이에 프록시 서버를 두어서 CORS를 해결할 수 있다.
* 개발 환경에서는 Webpack Dev Server 등의 라이브러리를 사용하여 프록시 설정을 할 수 있다.

## 참고 링크
* https://beomy.github.io/tech/browser/cors/
* https://evan-moon.github.io/2020/05/21/about-cors/
