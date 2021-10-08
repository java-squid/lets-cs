# 스케줄링 기법
* 스케줄링(scheduling)은 다중 프로그래밍을 가능하게 하는 운영 체제의 동작 기법이다.
* OS는 프로세스에게 CPU 등의 자원 배정을 적절히 하여 시스템 성능을 개선한다.
## 평가 기준
* CPU 사용률(CPU Utilization)
    * 전체 시스템 시간 중 CPU가 작업을 처리하는 시간의 비율
* 처리량(Throughput)
    * CPU가 단위 시간당 처리하는 프로세스의 개수
* 응답 시간(Response Time)
    * 대화식 시스템에서 요청 후 응답이 오기 시작할 때까지의 시간
* 대기 시간(Waiting Time)
    * 프로세스가 준비 큐 내에서 대기하는 시간의 총합
* 반환 시간(Turnaround Time)
    * 프로세스가 시작해서 끝날 때까지 걸리는 시간
## Preemptive vs Non-preemptive
* 비선점형 스케줄링(Non-preemptive Scheduling)
    * 프로세스가 종료되거나 프로세스가 스스로 자발적으로 중지될 때까지 계속 실행되도록 보장한다.
    * 순서대로 처리되고, 응답 시간을 예상할 수 있으며, 스케줄러 호출 빈도가 낮고, Context Switching overhead가 적다. 일괄 처리 시스템에 적합하다.
    * CPU 사용 시간이 긴 하나의 프로세스에 의해 처리율이 떨어질 수 있다는 단점이 있다.
    * 비선점형 스케쥴링은 수행시간이 긴 프로세스가 자원을 점유하게 되면 그 이후에 실행되어야 할 프로세스들이 기아 상태에 빠지게 된다.
* 선점형 스케줄링(Preemptive Scheduling)
    * 선점권은 실행 대기 중인 프로세스에 있다.
    * 다른 프로세스가 실행 중인 프로세스를 중지하고 CPU를 강제로 점유할 수 있다.
    * 모든 프로세스에게 CPU 사용 시간을 동일하게 부여할 수 있다. 빠른 응답시간에 적합하며 긴급한 프로세서를 제어할 수 있다.
    * 선점형 스케쥴링은 프로세스의 우선순위에 따라 스케쥴링을 하게됩니다. 따라서 우선순위가 낮은 프로세스는 자원을 할당받지 못하게 되는 기아 현상(starvation)이 발생할 수 있다.
## Static vs Dynamic
* 정적 스케줄링(Static Scheduling): 프로세스에 부여된 우선순위가 바뀌지 않는다.
* 동적 스케줄링(Dynamic Scheduling): 스케줄링 과정에서 프로세스의 우선순위를 변동시킨다.
## 종류
* 비선점 프로세스 스케줄링
    * FCFS 스케줄링(First Come First Served Scheduling)
        * 먼저 자원 사용을 요청한 프로세스에게 자원을 할당해 주는 방식의 스케줄링 알고리즘
    * SJF 스케줄링(Shortest Job First Scheduling)
        * 평균 대기 시간을 최소화하기 위해 CPU 점유 시간이 가장 짧은 프로세스에 CPU를 먼저 할당하는 방식.
        * 요구 시간이 긴 프로세스는 기아 상태가 발생할 수 있으며, 대기 상태에 있는 프로세스의 요구시간에 대한 정확한 자료를 얻기 어렵다는 문제점이 있다.
        * 단기 스케줄링 보다는 장기 스케줄링에 유리하다.
    * HRRN 스케줄링(Highest Response Ratio Next Scheduling)
        * 프로세스 처리의 우선 순위를 CPU 처리 기간과 해당 프로세스의 대기 시간을 동시에 고려해 선정하는 스케줄링 알고리즘이다. SJF 스케줄링의 문제점을 보완해 개발된 스케줄링이다.
        * ```우선순위 = (대기시간+처리시간) / 처리시간```
* 선점 프로세스 스케줄링
    * RR 스케줄링(Round Robin Scheduling)
        * 시분할 시스템을 위한 선점형 스케줄링 방식. 프로세스들 사이에 우선순위를 두지 않고, 순서대로 시간단위(Time Quantum)로 CPU를 할당하는 방식의 CPU 스케줄링 알고리즘이다.
        * 보통 시간 단위는 10 ms ~ 100 ms 정도이다. 시간 단위동안 수행한 프로세스는 준비 큐의 끝으로 밀려나게 된다. 문맥 전환의 오버헤드가 큰 반면, 응답시간이 짧아지는 장점이 있어 실시간 시스템에 유리하다.
    * SRTF 스케줄링(Shortest Remaining-Time First Scheduling)
        * SJF 스케줄링을 선점 형태로 수정한 방식. 현재 작업 중인 프로세스를 중단시키고 최단 잔여시간 프로세스의 처리를 시작하는 방식이다. 선점형 SJF 스케줄링 또는 SRT (Shortest Remaining Time) 스케줄링이라고도 한다.
    * 다단계 큐 스케줄링(Multilevel Queue Scheduling)
        * 커널 내의 준비 큐를 여러 개의 큐로 분리하여 큐 사이에도 우선순위를 부여하는 스케줄링 알고리즘이다. 또한, 각각의 큐에 대해 다른 스케줄링 알고리즘을 적용하기도 한다.
        * 프로세스들은 조건에 맞는 큐에 영구적으로 할당된다.
    * 다단계 피드백 큐 스케줄링(Multilevel Feedback Queue Scheduling)
        * 다단계 큐 스케줄링에서 한 단계 발전된 방식. 다단계 큐 스케줄링에서는 프로세스가 하나의 큐에 영구적으로 할당되지만, 다단계 피드백 큐 스케줄링에서는 프로세스들이 큐를 갈아탈 수 있다. Aging 등의 방식으로 대기 시간이 긴 프로세스를 높은 우선순위 큐에 올려 기아 상태를 막을 수 있다.
    * RM 스케줄링(Rate Monotonic Scheduling)
    * EDF 스케줄링(Earliest Deadline First Scheduling)
## 링크
* https://www.geeksforgeeks.org/preemptive-and-non-preemptive-scheduling/
* https://dhkdn9192.github.io/computer_science/cpu-scheduling-algorithm/
