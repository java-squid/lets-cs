# CPU Scheduling

![](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_01_CPU_BurstCycle.jpg)

- 어떤 프로그램이든, CPU Bursts 와 I/O Burst가 반복되면서 실행된다
- CPU Bursts?
    - load store, add store, read from file 을 의미.
    - 아마도 I/O 작업을 제외한, CPU 작업을 의미하는듯

- 그런데 CPU가 행하는 job은 여러가지가 있다.

![](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_02_CPU_Histogram.jpg)
- 위 자료는 CPU burst time 분포도
    - 좌측은 I/O bound job, 우측 그래프는 CPU bound Job이다.
    - CPU를 짦게 쓰면서, 빈도(frequency) 가 잦으며, 중간에 I/O 가 끼어드는 잡이 있고,
    - CPU를 길게 쓰는 CPU bound job이 있음.
- 다시 말해, 여러 종류의 Job이 깄기에 CPU와 I/O 장치에서 사용되는 시스템 자원을 효율적으로 분배할 필요성이 생긴다. 

> *I/O bound job(process)*
- 사람과 작업하는,
- CPU를 길게 잡고 있기 보다는 짦게, 빈도수를 많이 잡으면서 I/O에 더 많은 시간을 투자하는 잡

> *CPU bound job(process)*
- 계산 위주의 job

## CPU Scheduler & Dispatcher
- 스케쥴러는 `Ready` 상태의 프로세스 중에 CPU를 할당해줄 프로세스를 고르는 역할을 담당.
    - 즉 운영체제 안에서 CPU의 스케쥴을 하는 기능.
- 디스페쳐는 실제로 CPU에게 프로세스를 할당.
    - `Context switch` (문맥 교환)을 통해 CPU 제어권을 스케쥴러가 선택한 프로세스에게 넘기는 기능.

> *CPU 스케쥴링은 어떨때 일어날까?*
- 프로세스가..
    1. Running -> Blocked (ex) I/O작업으로 넘어간 경우)
    2. Running -> Ready (할당시간 만료 될 경우, timer interrrupt)
    3. Blocked -> Ready (I/O작업이 끝난 경우, 인터럽트를 걸어서 CPU를 넘겨줌)
    4. Terminate

# CPU Scheduling Algorithms

## First-Come, First-Served (FCFS) 
- 들어온 순서대로 처리하는 방법.
- 가장 많이 사용됨.
- 비효율적일 수도 있음. (waiting time 이 크다.)
    - 왜? 들어오는 순서대로 처리되니 오래 걸리는 job을 잡고 있을 수도 있고, 그 뒤에 금방 끝나는 잡들이 있을 수 있음.

## Shortest-Job-First (SJF)
- FCFS 를 개선하고자 나온 알고리즘
- CPU burst가 가장 짧은 프로세스를 먼저 실행 (waiting time이 작다)
- 이 알고리즘에는 2가지 타입이 있음.
    - preemptive(선점), non-preemptive(비선점)
    - 작업 실행 중에, 새로 들어온 잡에 처리하는 시간이, 지금 남은 시간보다 짦다? (마치기 까지 남은 시간 2초, 새로 들어온 잡이 완료되는 시간 1초)
        - preemptive의 경우, 새로 들어온 잡을 실행 
        - non-preemptive의 경우, 현재 실행 중인 잡을 계속 실행
- 그런데 실행 시간을, 실행해보지도 않고 어떻게 예측하지?
    - 이 전에 실행되었던 잡들을 기반으로 판단?
    - 이게 정확할 것이라는 보장이 없음.

## Priority
- 우선 순위가 높은 프로세스를 먼저 스케쥴링 하는 기법
- 어떻게 우선 순위를 할당할까?
    - FCFS 방법으로 혹은 SJF 방법으로 할당 가능하다.
- 기본적으로 우선 순위 할당은, 그 프로세스의 **중요 정도**에 따라 할당된다.
    - 이러한 중요도는, 운영체제에 의해 결정될 수도 혹은 외부적으로 결정될 수도 있음.
- 문제점은 starvation
    - 기아 상태
    - 다시 말해, 우선 순위가 낮은 프로세스는 만약에 상대적으로 우선 순위가 높은 프로세스가 계속 들어온다면 CPU를 할당받을 수 없다는 문제가 있음.
    - 즉 우선 순위가 낮은 프로세스가 무한정 대기하는 일이 벌어질 수 있음.
    - `aging` 이라는 기법을 통해 이를 해결할 수 있는듯.
        - 프로세스에 나이를 정하고, 일정 나이가 찬 프로세스는 우선 순위에 상관없이 무조건 CPU를 할당 받을 수 있도록 설정..

## Round-Robin (RR) 
- 한 라운드에 조금씩 전부 CPU를 쓸 수 있도록 분배하고, 이를 반복하는 스케쥴링 기법.
- 모두(프로세스) 에게 공평하게 CPU를 사용할 수 있도록 돌아간다.
- 문제점은 Context swtiching 에서 발생하는 오버 헤드
    - 매번 스위칭 하면서 발생하는 오버헤드가 비효율적일 수 있음.

## Multilevel Queue
- 여러개의 큐를 이용하여, 프로세스를 나누고,큐에 서로 다른 스케쥴링 기법을 적용하는 방법
- 운영체제가 만들어낸 프로세스가 우선 순위가 높고, 그 다음이 사용자와 상호작용하는 프로세스.
- 사용자와 상호작용하는 프로세스가 관리되는 큐에는 빠른 응답 타입을 갖기 위해서..
    - RR 알고리즘 사용하고,
- 운영체제가 만들어낸 프로세스를 관리하는 큐에는 
    - FCFS 알고리즘이 사용될 가능성이 큰 듯.

# 참고
- https://zzsza.github.io/development/2018/07/29/cpu-scheduling-and-process/
- https://velog.io/@ssseungzz7/OS-CPU-Scheduling
- https://jhnyang.tistory.com/155
- https://jhnyang.tistory.com/158?category=815411
- https://jhnyang.tistory.com/28
- http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791158903589