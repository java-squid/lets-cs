# Kernel 이란?
![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8f/Kernel_Layout.svg/400px-Kernel_Layout.svg.png)

- OS에 핵심 부분인, 컴퓨터 프로그램을 의미.
- 시스템의 모든 것을 제어할 수 있도록 도와주는 부분.
- 항상 메모리에 상주하고 있으며, 하드웨어와 소프트웨어의 상호작용을 할 수 있도록 도와주는 핵심적인 인터페이스를 의미.

# Kernel level thread
- 커널이 모든 프로세스, 스레드를 관리하는 방식을 의미 (스케쥴링등.. 모두 커널에서 관리)
- 1개의 Process table, Thread table을 통해서 관리.
- Context swiching이 자주 일어남.

## Advantages
- 커널이 존재하는 모든 스레드의 정보를 알고 있기 때문에, 스케쥴러에서 적절하게 각 스레드별로 CPU 자원을 배분할 수 있음
- non-blocking system call이 필요하지 않음.
    - 즉 만약 하나의 프로세스에 여러개의 스레드가 있다고 가정 하고 어떤 스레드(프로세스를 잡고 있는)가 Blocking 상태에 들어간다고 하여도, 다른 스레드에 작동에 영향을 주지 않음.
    - 커널이 그 프로세스에, 다른 스레드가 runnable한 상태임을 알고 있으므로, 스케쥴러에게 다른 스레드를 실행하라고 이야기 해줄듯.

## Disadbantages
- 느리고 비율적임.
    - 모든 스레드가 커널에 의해 관리되므로 (System call), 이러한 관리는 비용이 큼.
- 복잡도가 높음.
    - 커널이 모든 스레드의 정보를 알고 있어야 하므로,
    - 이러한 정보는 TCB(Thread Control Block) 에서 관리되는, 이러한 정보를 유지시키는 것이 상당한 오버헤드를 초래함.

# User level thread
- 커널은 쓰레드의 존재를 알지 못함 (즉 관리하지 않는다.)
- 커널은 단지 프로세스를 관리하는 테이블 (Process table) 만 가지고 있을뿐, 각각의 프로세스들이 스레드를 관리하는 테이블을 가지고 있음 (Thread table)
    - 즉 여러개의 스레드는 하나의 프로세스 (Many to One) 관리한다고 보면 됨.
- Context swiching 이 커널 레벨 스레드 보다 적게 일어남.
- 커널에 진입하지 않아도, 라이브러리 지원을 통해 스레드를 사용할 수 있게 함.
- Ex) Jvm with thread 

## Advantages
- 하나의 프로세스가 여러개의 스레드를 관리하는 만큼, 스레드에 수행에 필요한 정보가 해당 프로세스에 모두 저장되어있음.
- 그래서 빠르고 효율적임. (즉 context switching이 적어서)
- 각각의 프로세스가 다른 스케쥴링 알고리즘을 사용하도록 커스텀 할 수 있음.

## Disadbantages
- 어떤 프로세스에 여러 개의 스레드가 있다고 가정할 때, 
    - 하나의 스레드가 커널을 호출 한다면 (System call)
    - 해당 프로세스 내, 모든 스레드가 중단될 것 (Blocking System)
    - 그래서 user level thread를 사용하기 위해서는 non-blocking system call이 필요함. (커널을 호출하더라도, blocking 되지 않도록..?)

# 정리
![](https://examradar.com/wp-content/uploads/2019/02/threads-types.png)
- (좌측) User level thread , (우측) Kernel level thread

- 유저 레벨에서 스레드를 관리하는 것이라는 뜻은, 
    - 라이브러리등을 통해 하나의 프로세스에서 여러개의 스레드를 사용하게 만드는 것인듯.
    - 커널은 어떤 스레드가 있는지 모르고, 프로세스의 존재만 알고 있음.
    - 그래서 상대적으로 관리할 비용이 줄어들고 (프로세스만 관리하면 되니), 효율적으로 프로세스-스레드를 관리할 수 있도록 도와주는 측면이 있는듯.
    - 다만, 커널이 스레드를 관리하지 않는다는 건, 스레드의 상태를 잘못할 경우, 해당 프로세스는 blocked 상태로 변경되고, 다른 스레드들에 영향이 가는 결과를 초래할 수 있을듯.

- 커널 레벨에서 스레드를 관리하는 것은..
    - 커널이라는 프로그램이 모든 스레드의 정보를 알고 있어야 함.
    - 이 점이 굉장히 비용이 큰 사안일듯.
    - 다만, 커널이 관리해주므로, 프로세스가 죽는 block되는 사태는 벌어지지 않을듯.

# 참고
- https://en.wikipedia.org/wiki/Kernel_(operating_system)
- https://happy-chipmunk.tistory.com/entry/11-Multithreading2-Thread-%EC%9D%98-%EC%A0%81%EC%9A%A9-Userlevel-Threading-%EA%B3%BC-Kernellevel-Threading
- https://www.geeksforgeeks.org/difference-between-user-level-thread-and-kernel-level-thread/
- https://examradar.com/os-threads-different-types-thread-questions-answers/
- https://colinch4.github.io/2020-02-02/%EC%BB%A4%EB%84%90%EB%A0%88%EB%B2%A8%EC%8A%A4%EB%A0%88%EB%93%9C-vs-%EC%9C%A0%EC%A0%80%EB%A0%88%EB%B2%A8%EC%8A%A4%EB%A0%88%EB%93%9C/