# 사용자 수준 쓰레드 vs 커널 수준 쓰레드
![](https://i.imgur.com/4rFNHLc.png)
## Kernel Thread
* The kernel knows about and manages all threads.
* One process control block (PCP) per process.
* One thread control block (TCB) per thread in the system.
* Provide system calls to create and manage threads from user space.
* 대표적으로 입출력 I/O 작업 등을 꼽아볼 수 있다.
* 스케줄러가 컨텍스트 스위칭 하는 단위는 커널 스레드이고 컨텍스트 스위칭으로 저장되는 정보가 Process Control Block(PCB)이다.
## User level threads
* User level threads are supported above the kernel in user space and are managed without kernel support.
* Threads managed entirely by the run-time system (user-level library).
* Ideally, thread operations should be as fast as a function call.
* The kernel knows nothing about user-level threads and manage them as if they where single-threaded processes.
## Case Study
![](https://i.imgur.com/M9kfk74.png)
* 프로세스에 현재 스레드 4개를 생성하였다. 유저 레벨 스레드이다.
![](https://i.imgur.com/zzKN5Q1.png)
* 프로세스가 CPU 사용을 위해 OS에게 스레드 2개를 달라고 요청한다.
![](https://i.imgur.com/2URJg2V.png)
* 2개의 스레드를 현재 프로세스에서 쓸 수 있도록 할당해준다. 커널 레벨 스레드이다.
* OS의 스케줄러에 의해 현재 프로세스를 보고 있는 커널 레벨 스레드를 CPU에 할당한다. 해당 프로세스를 보고 있는 커널 레벨 스레드가 동작하기 시작한다. 사용자 레벨 스레드에 있는 스레드 중 1개가 커널 레벨 스레드와 연결되어 프로세스에서 작업을 진행할 수 있다.
![](https://i.imgur.com/8iWkBJJ.png)
* 위의 그림은 커널 레벨 스레드는 그대로 컨텍스트 스위칭을 당하지 않았지만 사용자 레벨 스레드는 컨텍스트 스위칭을 당해 작업하는 스레드가 바뀜을 알 수 있다.
![](https://i.imgur.com/luUQZut.png)
* 커널 레벨 스레드가 OS의 스케줄러에 의해 컨텍스트 스위칭을 당하면서 TCB가 저장, 스왑되고 어떤 인터럽트가 들어와서 프로세스 자체가 컨텍스트 스위칭될때 PCB가 저장, 스왑된다.

**Q: 사용자 스레드 방식이 커널 스레드 방식보다 오버헤드가 적은 이유는?**
* A: 스레드간 전환할 때마다 커널 스케줄러를 호출할 필요가 없기 때문이다.
## Multithreading Model
### N:1 사용자 모델
![](https://i.imgur.com/pR4M13E.png)
* 프로세스 1개(사용자 스레드 N개) 당 커널 스레드 1개가 할당된다.
* 프로세스 내에 스레드 라이브러리가 있어서 커널의 도움없이 스레드의 스케줄링을 할 수 있다.
* 커널은 프로세스 내의 스레드의 존재를 모른다.
* 스레드 정보(TCB, Thead Control Block)는 프로세스 내에서, 프로세스 정보(PCB, Process Control Block)는 커널에서 관리한다.
* 장점으로는, 스레드의 스케줄링 및 동기화에 시스템콜(커널호출)이 필요없기 때문에 오버헤드가 적다.
* 단점으로는,
    * 동작 중인 스레드가 시스템콜을 하면 해당 프로세스 내의 모든 스레드가 멈춘다. (Blocking system call)
    * 프로세스 단위로 CPU가 할당되므로, 다중CPU환경에서 한 프로세스 내 스레드들을 동시에 실행할 수 없다. (Hardware Parallelism 지원안함)
    * 다중 처리 시스템에서는 사용할 수 없다.
    * 커널이 스레드를 관리하지 않아, 프로세스에서 스레드 간 보호를 해줘야 한다.
### 1:1 커널 모델
![](https://i.imgur.com/iULY4j0.png)

* 프로세스 내의 사용자 스레드 1개 당 커널 스레드 1개가 할당된다.
* 사용자 스레드를 생성하면 할당할 커널 스레드를 1개 생성한다.
* 프로세스 내에 스레드 라이브러리가 없어서 커널 스레드를 스케줄하여 매핑된 사용자 스레드를 동작시킨다.
* 커널이 전체 TCB와 PCB를 관리한다.
* 장점으로는,
    * 동작 중인 스레드가 시스템콜을 해도 해당 프로세스 내의 다른 스레드가 동작할 수 있다.
    * 스레드 단위로 커널 스레드가 할당되므로, 다중CPU환경에서 한 프로세스 내 스레드들을 동시에 실행할 수 있다. (즉, 시스템 동시성을 지원한다고 말할 수 있다)
* 단점으로는,
    * 스레드 문맥교환할 때도 시스템콜이 필요하고 커널이 모든 TCB와 PCB를 관리하여 오버헤드가 크다.
    * CPU, RAM 성능에 따라 생성 가능한 커널 스레드 및 사용자 스레드가 한정되어 있다.

### 혼합된 스레드 모델 (N:M)
![](https://i.imgur.com/Vh2V3gc.png)
[링크](https://www.geeksforgeeks.org/relationship-between-user-level-thread-and-kernel-level-thread/)
* 커널 스레드 마다 1개씩 경량 프로세스(LWP, Light Weight Process)가 매핑되어 있다.
* LWP는 가상 처리기로써, 1:1 모델처럼 한 프로세스에 여러 개가 할당될 수 있고, 프로세스에 할당된 LWP는 N:1 모델처럼 여러 개의 사용자 스레드를 관리한다. (커널과 프로세스 간의 중간자 역할을 한다.)
* 따라서 프로세스 1개(사용자 스레드 N개)에 M 개의 커널 스레드를 할당할 수 있다. (N ≥ M)
* 장점으로는,
    * 사용자 스레드 생성 개수가 제한되지 않는다. (N:1)
    * 시스템콜을 해도 해당 프로세스 내 (다른 LWP에 연결된) 스레드는 멈추지 않는다. (1:1)
* 단점으로는,
    * LWP가 블록되면 LWP에 연결된 모든 사용자 스레드가 멈춘다. (N:1)
    * LWP의 사용자 스레드 문맥교환도 1:1 모델만큼 오버헤드가 크다. (1:1)
    * 각 LWP에 한 개의 사용자 스레드가 실행되지 않으면, 1:1 모델에 비해 다중CPU환경에서 효율적이지 않다. (N:1)

**Q: 디스크 입력의 경우 사용자 수준 쓰레드 모델과 커널 수준 쓰레드 모델 중 어느 것이 유리할까?**
A: Kernel-level Thread 모델이 유리하다.
* User-level Thread 모델에서는 스레드가 디스크 입출력을 할 때마다 해당 프로세스 내의 모든 스레드가 중단된다. 반면에, Kernel-level Thread 모델은 커널 스레드가 사용자 스레드에 1:1로 할당되므로 한 스레드에서 시스템콜을 해도 해당 프로세스의 다른 스레드가 중단되지 않는다.
* 유저 레벨 스레드를 이용하면 컨텍스트 스위칭을 프로세스 내부에서 진행하면 되고, 커널로 진입하지 않아도 되어 비용이 적게 든다.
    * 즉, 스레드의 스케줄링 및 동기화에 시스템콜(커널호출)이 필요 없기 때문에 오버헤드가 적다.
* 그리고 커널 레벨 스레드는 커널이 직접 스레드를 관리해주는 것이기 때문에 유저 스레드 1개당 커널 1개가 맡아준다. 따라서 하나의 프로세스에 여러 유저 스레드가 있어도 동시에 실행이 가능하다.
* 하지만 여기서 유저 레벨 스레드에 I/O를 이용하면 치명적인 단점이 존재한다.
* 유저 레벨 스레드를 이용하면 커널은 프로세스 내의 스레드 존재를 모르기 때문에 하나의 유저 레벨 스레드가 I/O를 하면 나머지 모든 스레드가 멈추게 된다.
* 따라서 I/O를 하기 위해서라면 커널 레벨 스레드를 이용하는게 더 유리하다.


### Reference
https://www.crocus.co.kr/1404
http://www.it.uu.se/education/course/homepage/os/vt18/module-4/implementing-threads/
https://www.geeksforgeeks.org/relationship-between-user-level-thread-and-kernel-level-thread/
