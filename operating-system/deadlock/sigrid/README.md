# 데드락이란 무엇인가
## 정의
* 운영체제에서 데드락이란, 시스템 자원에 대한 요구가 엇갈린 상태를 의미한다.
* 둘 이상의 프로세스가 다른 자원의 프로세스를 점유하다보니, 무한 대기(루프) 상태에 빠지게 되는 것을 의미한다.
* 즉, Deadlock은 다른 프로세스 또는 쓰레드가 소유한 자원을 요청한 행위가 서로에게 행해져 모든 프로세스 또는 쓰레드가 block이 되는 상태를 의미한다.
* A lock occurs when multiple processes try to access the same resource at the same time.
    * One process loses out and must wait for the other to finish.
* A deadlock occurs when the waiting process is still holding on to another resource that the first needs before it can finish.
## 예시 1
![](https://i.imgur.com/MEXzKoF.png)
* 쓰레드 T0이 리소스 1에 대한 Lock이 걸려 있는 상태이지만, 해당 쓰레드가 모든 일을 끝마치려면 리소스 2에 대한 자원이 필요한 상태이다.
* 쓰레드 T1이 리소스 2에 대한 Lock이 걸려 있는 상태이지만, 해당 쓰레드가 모든 일을 끝마치려면 리소스 1에 대한 자원이 필요한 상태이다.
## 예시 2
### 서로가 사용하고 있는 Semaphore를 요청하여 Block에 걸리게 되는 경우
* 다음과 같이 두 개의 코드가 주어졌다고 생각하자.
```
Process1
owns lock A
requests lock B
```
```
Process2
owns lock B
requests lock A
```
* 여기서 프로세스 1은 lock A를 갖고 있는데, 이는 A에 대한 세마포어(semaphore)를 갖고 있다는 뜻이다. 이와 달리, 프로세스 2는 lock B를 갖고 있는데, 이는 B에 대한 세마포어(semaphore)를 갖고 있다는 뜻이다.
* 세마포어는 한 곳에서 사용 중이라면, 다른 프로세스를 차단하는 목적으로 사용된다. 프로세스 1이 lock B를 요청할 경우, lock B는 현재 사용 중이므로 프로세스 1은 lock B가 release되기 전까지 Block 상태에서 대기하게 된다.
* 프로세스 2도 마찬가지이다. lock A를 요청할 경우, 현재 lock A는 사용 중이므로 프로세스 2는 lock A가 release되기 전까지 Block 상태에서 대기하게 된다.
![](https://i.imgur.com/n86eluU.png)
## 예시 3
### Recursive Lock
* 프로세스 1이 한 semaphore를 갖고 있다. 해당 semaphore는 다른 프로세스가 접근할 수 없게 된다. 이 떄, 프로세스 1은 똑같은 semaphore에 접근을 요청한다. 이러한 경우, 해당 semaphore는 접근할 수 없음으로 데드락에 빠지게 된다.
![](https://i.imgur.com/a6f89wO.png)
## 예시 4
### 범죄자와 경찰
![](https://i.imgur.com/lWTXmAi.png)
* Imagine a criminal holds an hostage and against that, a cop also holds an hostage who is a friend of the criminal. In this case, criminal is not going to let the hostage go if cop won't let his friend to let go. Also the cop is not going to let the friend of criminal let go, unless the criminal releases the hostage. This is an endless untrustworthy situation, because both sides are insisting the first step from each other.
* So simply, when two threads needs two different resources and each of them has the lock of the resource that the other need, it is a deadlock.

## Deadlock의 상태
* 일반적으로 프로세스는 리소스를 사용하기 위해서는 사전에 요청을 거쳐야 하고, 다음과 같은 과정을 거친다.
    * Request: 리소스 사용을 요청할 때 즉각 받아들여지지 않는다면, 프로세스는 리소스 사용이 가능해질 때까지 대기해야 한다. 주된 함수 연산으로는 ```call(), malloc()``` 등이 있다.
    * Use: 쓰레드가 자원을 사용하는 단계이다. 프린터를 사용하고 있거나, 파일을 읽고 있다.
    * Release: 프로세스는 리소스 사용을 중지(relinquish)한다. 따라서 해당 자원은 다른 프로세스가 사용 가능해진다. 주된 함수 연산으로는 ```close(), free(), delete()``` 등이 있다.
* 커널이 매니징하느냐? 애플리케이션이 매니징하느냐? 에 따라 차이가 있는데 아무래도 커널이 관리해주는 것이 개발자 입장에서는 불필요한 소요를 줄이는 일이지 않을까 싶다.
    * 커널이 매니징한다는 것은, 커널이 어떤 리소스가 free이고 어디에 allocate 되어 있고를 모두 파악한다는 소리다. 현재 process의 queue를 관리해서 어느 프로세스가 다음 리소스의 사용을 원하는 지를 확인한다.
    * Application이 매니징한다는 것은, mutex나 wait(), signal() 함수 호출로 인하여 컨트롤 되는 것을 의미한다.

## Deadlock의 4가지 조건
* Mutual Exclusion: 최소 한 개의 리소스가 non-sharable한 상태로 특정한 프로세스나 쓰레드에 의해 사용되고 있어야 한다. 만약 다른 프로세스가 해당 리소스 사용 요청을 보내면, 해당 프로세스는 리소스가 사용 가능할 때까지 기다려야 한다.
* Hold and Wait: 프로세스는 동시에 하나의 리소스를 갖고 있으면서 또 다른 프로세스나 쓰레드가 점유하고 있는 리소스 사용을 요청할 수 있다.
* No Preemption: 임의의 프로세스가 리소스를 소유한다고 가정하자. 그렇다면 다른 프로세스나 쓰레드는 해당 프로세스나 쓰레드가 스스로 리소스를 내려 놓기 전까지는 강제로 끌어내릴 수 없다.
* Circular wait:  A set of processes { P0, P1, P2, . . ., PN } must exist such that every P[ i ] is waiting for P[ ( i + 1 ) % ( N + 1 ) ]. (해당 조건은 Hold and Wait 조건을 support하는 것 같다)

## 데드락, 어떻게 해결할 것인가
## 데드락을 예방 Prevention 하기
* Not allowing the system to get into a deadlocked state. 위의 4가지 조건을 모두 충족시키지 않으면서 가능하다.
* 자원의 상호 배제 조건 방지 : 한 번에 여러 프로세스가 공유 자원을 사용할 수 있게 한다. 다만, 추후 동기화 문제가 생길 수 있다.
* 점유 대기 조건 방지 : 프로세스 실행에 필요한 모든 자원을 한꺼번에 요구하고 허용할 때까지 작업을 보류해서, 나중에 또다른 자원을 점유하기 위한 대기 조건을 성립하지 않도록 한다.
* 비선점 조건 방지 : 이미 다른 프로세스에게 할당된 자원이 선점권이 없다고 가정할 때, 높은 우선순위의 프로세스가 해당 자원을 선점할 수 있도록 합니다.
* 순환 대기 조건 방지 : 자원을 순환 형태로 대기하지 않도록 일정한 한 쪽 방향으로만 자원을 요구할 수 있도록 합니다.
* 데드락 예방(Prevention)은 시스템의 처리량이나 효율성을 떨어트리는 단점이 있다.

### 데드락 회피(avoidance)
* 시스템의 프로세스들이 요청하는 모든 자원을, 데드락을 발생시키지 않으면서도 차례로 모두에게 할당해 줄 수 있다면 안정 상태(safe state)에 있다고 한다.
* 그리고 이처럼 특정한 순서로 프로세스들에게 자원을 할당, 실행 및 종료 등의 작업을 할 때 데드락이 발생하지 않는 순서를 찾을 수 있다면, 그것을 안전 순서(safe sequence)라고 부른다.
* 불안정 상태는 안정 상태가 아닌 상황을 말한다.  데드락 발생 가능성이 있는 상황이며, 교착 상태(데드락)는 불안정 상태일 때 발생할 수 있다.
* 회피 알고리즘은 자원을 할당한 후에도 시스템이 항상 Safe state에 있을 수 있도록 할당을 허용하자는 것이 특징이다.
* 은행원 알고리즘: 은행원 알고리즘의 경우, 이처럼 미리 최대 자원 요구량을 알아야 하고, 할당할 수 있는 자원 수가 일정해야 하는 등 사용에 있어 제약조건이 많고, 그에 따른 자원 이용도 하락 등의 문제가 있을 수 있다.

### 데드락 탐지 및 회복(Detection)
* 탐지
    * Allocation, Request, Available 등으로 시스템에 데드락이 발생했는지 여부를 탐색
    * 현재 시스템의 자원 할당 상태를 파악
* 회복
    * 순환 대기에서 벗어나 데드락으로부터 회복하기 위한 방법을 사용
    * 단순히 프로세스를 1개 이상 중단시키기: 상태에 빠진 모든 프로세스를 중단하거나, 프로세스를 하나씩 중단 시킬 때마다 탐지 알고리즘으로 데드락을 탐지하면서 회복시키는 방법: 매번 탐지 알고리즘을 호출 및 수행해야 하므로 부담이 되는 작업일 수 있음
    * 자원 선점하기: 프로세스에 할당된 자원을 선점해서, 교착 상태를 해결할 때까지 그 자원을 다른 프로세스에 할당해 주는 방법

#### Reference
* 더 알아보기 좋은 자료(영문): https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/7_Deadlocks.html
* https://stackoverflow.com/questions/34512/what-is-a-deadlock
* https://chanhuiseok.github.io/posts/cs-2/
