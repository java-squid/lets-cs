# 프로세스란?

- 메모리에 올라와서, 독립적으로 실행 중인 프로그램을 의미한다.
![image](https://user-images.githubusercontent.com/22140570/132981855-c1124ab9-4342-462a-baec-9445e476559d.png)
- 프로세스는 운영체제로 부터 독립적인 메모리 영역을 할당받고,
    - 할당받는 메모리 영역은 `Code, Data, Stack, Heap`의 구조로 되어있음.
- 프로세스는 최소 한 개의 스레드(메인 스레드)를 가지고,
- 각 프로세스는 별도의 주소 공간에서 실행되고, 다른 프로세의 메모리에 접근할 수 없음.
    - 프로세스간 통신을 하려면 IPC(inter-process-communication) 을 사용해야함.
    - 파이프, 파일, 소켓등을 이용해야한다는 말.


# 스레드란?
- 프로세스가 할당받는 자원을 이용하는 실행의 단위.
- 프로세스보다 작은 의미.
- 프로레스 안에서의 흐름을 의미함.
![image](https://user-images.githubusercontent.com/22140570/132981870-6323ffb1-506a-49b7-bc98-adb9f40ecba2.png)
- 스레드는 프로세스 내에서 각각 `Stack` 를 **따로** 할당 받고, Code, Data, Heap 영역을 공유함.
- 프로세스 내의 주소 공간이나 자원을 스레드간 공유한다.
- 각각의 스레드는 별도의 **레지스터, 스택** 을 가지고 있고, 프로세스 안에 있는 힙 공간도 읽고 쓰는 것도 가능함. 즉 힙은 공유하는 공간임

# 자바 스레드 란?
- 일반 스레드와 차이 없음.
- JVM이 운영체제 역할을 할 뿐.
- 자바에서는 프로세스가 존재하지 않고, 스레드만 존재.
- 자바 스레드는 JVM에 의해 스케쥴되어 실행되는 코드 블럭이라고 할 수 있음.

# 멀티 프로세스란?
- 하나의 응용 프로그램을 여러 개의 프로세스로 구성하여, 각 프로세스가 하나의 작업을 처리하도록 하는 것.
    - **장점** : 여러 개의 자식 프로세스 중 하나에 문제가 발생하면 그 프로세스만 죽이면 되어서 다른 프로세스에는 영향을 주지 않음.
    - **단점** : Context Switching 오버헤드 발생, 즉 프로세스는 각각의 독립적인 메모리 공간을 할당 받았기에, 프로세스간 공유할 수 있는 정보가 없어서 Context Swithing이 발생하면 캐쉬에 있는 데이터를 리셋하고, 다시 캐쉬정보를 불러와야함. 이과정에서 오버헤드가 발생함.
- Context Switching?
    - CPU에서는 여러 프로세스가 돌아가면서 작업을 처리하는 데, 이렇게 공수교대 하는 과정을 문맥 교환이라 말한다.
    - 자세히 보면, 현재 CPU에서 실행 중인 프로세스가 대기상태로 들어가면서 현재 프로세스의 상태(context)를 보관하고, 다음 실행 대기 중인 프로세스가 동작하기 위해서, 이전 프로세스의 상태가 다리 load되는 작업을 의미한다.

# 멀티 스레드란?
- 하나의 응용프로그램(프로세스)를 여러개의 스레드로 구성하고, 각 스레드로 하여금 하나의 작업을 처리하게 하는 것.
- 웹 서버는 대표적인 멀티 스레드 응용 프로그램
    - 장점 → 시스템 소모 자원 감소 즉 자원의 효율성을 극대화, 프로세스를 생성하여 자원을 할당하려는 시스템 call이 줄어들어, 시스템 자원을 효율적으로 관리할 수 있음.
        - 그리고 시스템의 처리량이 증가한다. 왜? 스레드 간 데이터를 주고 받는 것이 간단하고, 스레드 사이의 작업량이 프로세스에 비해 상대적으로 작기 때문에 Context Switching이 빠르다.
        - 스레드는 프로세스의 영역 중 stack을 제외한 모든 영역을 공유하기에 스레드간 통신 부담이 적다는 것이 이유.
    - 단점 → 자원 공유에서 발생하는 동기화 문제. , 하나의 스레드에서 문제가 생길 경우 발생하는 문제

# 멀티 프로세스 대신 왜 멀티 스레드를 사용할까?
![image](https://user-images.githubusercontent.com/22140570/132981913-4aac8898-0020-42bb-bd8b-6bb2b2c224ed.png)
- 프로그램을 여러 개 실행 시키는 것 보다는, 하나의 프로그램에서 여러 작업을 해결토록 하는 것이 낫다.
- 자원의 효율성, 처리 비용도 감소, 심지어는 응답 시간도 단축되기 때문에
- 왜? 프로세스 간 문맥교환은 비용이 큰 작업.

# 참고
- https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html
- https://www.youtube.com/watch?v=1grtWKqTn50