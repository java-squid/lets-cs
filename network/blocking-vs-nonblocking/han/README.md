# blocking, non-blocking

- 막는다, 안 막는다. --> 무엇을?
  - 제어할 수 없는 대상을..
- 동기, 비동기화 차이가 확실히 있을듯 한데, 개념상 헷갈리는 측면이 있는듯.
- 네트워크 보다는 운영체제..에 가까운 주제이지 않을까



## blocking

- **블로킹**
- 직접 제어할 수 없는 대상을 처리하는 방법에 대한 내용
  - 즉 직접 제어할 수 없는 대상의 작업이 끝날 때 까지 기다린다 (return 값을 받을 때 까지)
- 주로 I/O, 멀티쓰레드 동기화 작업에서 많이 사용되는 개념.



## non-blocking

- **논블로킹**
- blocking과 반대되는 개념.
- 직접 제어할 수 없는 대상의 작업 상태와는 상관 없이, 자신의 작업을 게속 진행할 수 있음.
- 예를 들면 **I/O** 작업 완료 여부와 상관 없이, 다음 코드로 넘아갈 수 있다.



# synchronous, asynchronous

- 동기, 비동기
- 시간을 함께 맞춘다, 시간을 함께 맞추지 않는다.
- 어떤 대상 간의 **시간**을 맞추거나, 맞추지 않는 것이 핵심인듯.



## synchronous

- 두 가지 이상의 대상(함수, 스레드등) 이 서로 시간을 맞추어 동작하면 동기라고 보는듯.

![](https://media.vlpt.us/post-images/codemcd/7e027a20-4a1d-11ea-99c6-dd89d1c118fe/SpringCamp-AsyncSpring-2.png)

- A가 완료되는 시점에, B가 시작하는 **시간** 이다.
- 끝나는 시간과 , 시작하는 시간이 같다 



## asynchronous

- A가 완료되는 시점에, B가 실행되지 않음.
- 즉 A는 B가 언제 완료되는지 신경쓰지 않는 것을 의미.



# 차이점에 대해

- blocking vs synchronous

  - blocking의 경우, 대상(스레드, 애플리케이션) 의 상태를 나타내는 말인듯, 블로킹, 멈춰있음. 되어있다.,,,
    - 현재 내 상태
  - synchronous의 경우, 시간이 연결되는 느낌.
    - 즉 A,B  synchronous하다 라는 의미는, A에서 B를 콜한다고 할 때, B가 끝나는 시간(return)에, A의 시간이 다시 흐르는 경우를 의미하는듯.
  - 둘이, 완전히 다른 의미는 아닌듯 보여진다. 어디에 초점을 두고 있는 지에 따라 다르지 않을까

- non-blocking vs asynchronous

  - non-blocking의 경우, 대상의 상태가 멈춰 있지 않음. 결과를 기다리지 않고 제어권이 바로 반환되며, 다음 동작을 지속한다.

    - polling?

      - > 애플리케이션은 디바이스 드라이버에게 “A, B, C 사건이 일어날 때 알려줘. 그 때까지 좀 잘게” 라고 말하는 것이 `poll()` 이고...

      - 결과값을 알기 위해서 시스템 레벨에서 사용하는 방법인듯.

  - asynchronous의 경우, 시간이 연결되어 있지 않음.

    - A,B가 asynchronous하다는 의미는, A -> B를 콜하였을 경우, B가 리턴하는 시간과(A가 동작을 시작하는 시간), B에서 결과값을 가져오는 시간이 일치하지 않는다.
    - 즉 B에서 결과값을 가져오지 않더라도, A의 시간은 흐른다.





# 예시

## synchronous + blocking

```javascript
device = IO.open()
data = device.read()
print(data)
```

![](https://media.vlpt.us/post-images/codemcd/8c51a790-4a1d-11ea-a77e-bff20a601eb8/synchronous-blocking-IO.png)



- kernel에서 read response하는 시간과, application에서 read() 마무리 하는 시간이 맞물린다 (동기)
- application thread는 read()를 실행한 순간, kernel 에서 하는 작업이 마무리 될 때 까지 가디렸다 (블로킹)



## sychronous + non-blocking

```javascript
device = IO.open()
ready = False
while not ready:
    print("There is no data to read!")
    ready = IO.poll(device, IO.INPUT, 5) 
data = device.read()
print(data)
```



![](https://media.vlpt.us/post-images/codemcd/982dd930-4a1d-11ea-a77e-bff20a601eb8/Synchronous-non-blocking-IO.png)

- while 문은 IO.poll() 작업 결과 여부와 상관 없이 계속 돈다 (non-blocking)
  - 반면 asychronous의 경우, poll하는 작업이 없다 (즉 Kernel에서 작업이 끝나면 application에 알려준다)
- read() 메서드가 실행되는 시간과, Kernel 의 작업이 완료되는 시간은 동일하다. (synchronous)



## asynchronous + non-blocking

![](https://media.vlpt.us/post-images/codemcd/9dd91ca0-4a1d-11ea-a77e-bff20a601eb8/Asynchronous-non-blocking-IO.png)

- read () 하는 순간, Kernel로 콜이 감. read I/O 에 대한 결과값 상관 없이, 제어권이 리턴됨 (non-blocking)
- Kernel에서 실행되고 있는 작업이 끝나는 순간, 해당 결과값이 전달된다.
  - 이 시간은 현재 application의 흐르고 있는 시간과 일치하지 않는다 (asynchronous)



## asynchronous + blocking

![](https://media.vlpt.us/images/wonhee010/post/bc20459f-02fe-431a-a1d0-8d5016a964f9/zKF0CgK.png)

- Node + MySQL



# 참고

- https://stackoverflow.com/questions/2625493/asynchronous-and-non-blocking-calls-also-between-blocking-and-synchronous
- https://velog.io/@codemcd/Sync-VS-Async-Blocking-VS-Non-Blocking-sak6d01fhx
- https://velog.io/@wonhee010/%EB%8F%99%EA%B8%B0vs%EB%B9%84%EB%8F%99%EA%B8%B0-feat.-blocking-vs-non-blocking