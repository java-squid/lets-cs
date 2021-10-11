# Interrupt

- 프로세스가 하던일을 멈추고, *이미 정해진 코드*에서 요청에 대한 처리를 **수행**하는 것.
- 각 자원들이 능동적으로 자신의 상태 변화를 CPU에게 알리는 방식.

- `Polling`
  - *CPU*가 일정한 시간 간격을 두고, 각 장원들의 상태를 주기적으로 확인하는 방법
  - Interrupt는 자원들이 CPU에게 자신의 상태를 알리는 방법이고..
- 인터럽트는 하드웨어, 소프트웨어 인터럽트로 나눌수 있을듯.
  - 하드웨어는.. 모니터 마우스 등등이고..
  - 소프트웨어는 CPU 자신이 인터럽트를 사용하는 경우 인듯.



- Interrupt가 실행되는 과정

  ![image](https://user-images.githubusercontent.com/22140570/136755289-4b270209-cbc6-4250-8070-1e76658b5c5d.png)

  - PC ?
    - CPU가 실행하는 명령어
    - 레지스터에 있음

  2. 장치 (키보드) 의 인터럽트 발생

  3. 현재 실행 중인 프로세스 정보 저장
     1. 어디에? 시스템 스택에.
     2. 어떤 정보? PSW(Program status Word, 현재 상태 정보), PC 레지스터의 값 (CPU가 어떤 명령어를 실행하고 있었는지 )

  4. Interrupt Vector에 가서, 요청들어온 인터럽트에 대한 ISR을 찾음.

  5. 찾은 ISR에 대한 주소를 PC에 넣음 (인터럽트 처리를 위해서..)

  6. 인터럽트 처리

  7. 저장된 프로세스 정보를 가져와서, 이 전 실행 되었던 프로세스 실행으로 돌아감

- 참고 키워드
  - Interrupt Service Routine, ISR (Interrupt Handler)
  - Interrupt Vector
    - 여러가지 인터럽트들을 관리하는 테이블



# Context switching

- 위 이미지에서 (3,4,5)번 과정

- 하나의 프로세스가 cpu를 사용하는 상태인데, 다른 프로세스가 cpu를 사용하게 하기 위해서... 발생하는 것.

  - 즉 이전 프로세스의 상태를 **보관** 하고, 새롭게 실행된 프로세스의 상태를 **적재하도록 하는 과정**, 작업을 의미.

- 과정

  ![image](https://user-images.githubusercontent.com/22140570/136755834-7938b4ae-86bb-408e-8cb4-ee7fc909435a.png)

  - 인터럽트 과정과 같음

  1. P0 실행 중.. 그런데 P1 프로세스에서 인터럽트 or 시스템 콜 발생
  2. P0 프로세스에 대한 정보를 PCB에 저장. 그리고 P1에 대한 정보를 PCB에서 찾아서, 메모리에 올림
  3. P1 실행
  4. 위 반복.



- Context Switching이 왜 발생?
  - CPU는 한번에 하나의 프로세스만 처리할 수 있기에.
  - 여러 프로세스를 실행, 중단 하면서 작동하기에.
  - 이 비용이 비싸기에, Mutil Thread 환경이 나오지 않았을까



- 참고 키워드
  - idle : CPU가 아무일도 하지 않는 상태
    - idle이 겹칠 경우를 오버 헤드라 말함.
    - 왜? 
      - 필요한 정보들을 적재하느라, CPU가 아무일도 하지 않는 상태이기 때문에 (일을 안해서, CPU가 낭비되고 있음)
    - 즉 프로세스가 많아지면, 적재과정 때문에 CPU가 안하는 일이 많아져서 오버헤드가 증가할듯.
  - PCB (Process Control Block)
    - 레지스터에 있고, Queue (LIFO) 구조임.
    
    - process state : 프로세스 상태 값 (Create, Ready, Runinng..)
    
    - procuess counter : CPU가 다음 실행할 명령어의 주소 값.
    
    
      ![](https://nesoy.github.io/assets/posts/20181113/1.png)



# 참고

- https://www.youtube.com/watch?v=-4HKhwlH3FQ
- http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788993712476
- https://nesoy.github.io/articles/2018-11/Context-Switching
- https://jeong-pro.tistory.com/93

