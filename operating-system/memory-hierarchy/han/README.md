# 메모리 계층 구조란?
![](https://www.elprocus.com/wp-content/uploads/Memory-Hierarchy.jpg)

- CPU 접근 속도에 따라 메모리의 계층을 나눠놓은 것.
- 4가지 특징이 있음

## Capacity
- 크기
- 상위 계층일수록 작음 (레지스터가 가지는 크기는 가장 작다는 의미)

## Access Time
- 접근 시간
- CPU가 메모리에 read/write 하는 데 걸리는 시간
- 상위 계층 일수록 빨리 접근할 수 있을듯.

## Performance
- 수행 능력
- 얼마나 CPU가 효율적으로 움직이는지 에 대한 이야기 인듯.
- 과거 메모리 계층 구조 없이, 메모리가 구성되었을 때는 메인 메모리든 레지스터든 접근 시간 때문에 수행 능력이 비슷하게 안 좋았음.
    - 시스템 수행 능력 강화를 위해, 해당 계층 구조가 등장했다고 보면 될듯.

## Cost per bit
- 1bit 당 가격을 생각하면, 상위 계층일수록 비쌀 것.



# Primary Memory
- CPU와 가장 접근성이 좋은 내부 메모리
- 보통 메인, 캐시, 레지스터 를 의미

# Secondary Memory
- 외부 메모리 (CPU 외부에 존재하는 듯 )
- I/O 작업이 필요하다
- Disk..등이 이에 해당됨.

# Summary
![](https://media.vlpt.us/images/ogs0518/post/992f6465-cf96-4bd7-8afb-85086bb245fb/Untitled.png)

# 참고
- https://ko.wikipedia.org/wiki/%EB%A9%94%EB%AA%A8%EB%A6%AC_%EA%B3%84%EC%B8%B5_%EA%B5%AC%EC%A1%B0
- https://www.geeksforgeeks.org/memory-hierarchy-design-and-its-characteristics/
- https://www.elprocus.com/memory-hierarchy-in-computer-architecture/