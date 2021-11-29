# 가상 메모리란?
- 메모리 관리 기법
- 실행하고자 하는 프로세스가 모두 메모리에 올라오지 않아도, 실행하도록 하는 기법임.
- 왜 사용할까?
    1. 물리 메모리의 제약에서 벗어남 (즉 정해져있는 메모리보다 더 많이 사용하는 것 처럼 보임)
    2. 각 프로그램이 더 작은 메모리를 통해, 실행 가능 하기 때문에 여러 프로그램이 동시에 실행가능함.
    3. I/O가 적게 일어남. (왜?)
        - 필요한 페이지만 올리기에, 페이지 사용하기 전보다 I/O가 적게 일어날 것.

## 왜 필요할까?
- 현실적으로 실행되어야할 모든 프로세스의 정보를 물리 메모리에 올려 놓을 순 없다.
    - 그러면 자주 사용되는 프로그램의 정보만 우선 물리 메모리에 올려 놓으면 어떨까?
- 즉 프로그램 실행에 필요한 메모리가 적재될 때, 연속적으로 저장될 필요 없음 (다른 프로그램의 메모리와 섞여 적재)
    - 그러면 메모리를 더 효율적으로 사용할 수 있을듯.

## 어떻게 필요한 정보만 물리 메모리에 올릴까?
> Demand Paging (요구 페이징 기법)

![image](https://user-images.githubusercontent.com/22140570/143865107-61ddee97-88af-45c8-9e7d-07c6d4e5621d.png)
- 주소 공간은 페이지로 구성되어 있음 (뭉쳐있음.)
- 필요한 페이지만 물리 메모리에 그때 그때, 물리 메모리에 올리는 방식
    - *유-무효 비트*를 통해, 페이지가 물리메모리에 있는 지 없는지 알 수 있음.
    - 만약에 필요한 페이지가 물리 메모리에 없으면? 
        - Page fault
        - 페이지 폴트 발생 시, 보조 저장 장치 (swap area..?)

> CPU 가 페이지를 찾는 과정

![image](https://user-images.githubusercontent.com/22140570/143865553-572ec7f4-c995-44f4-9c11-cbf6cce70525.png)

1. 논리적 메모리에서 특정 페이지를 찾음 (1)
2. 페이지 테이블 참고
     - Invalied bit (물리 메모리에 해당 페이지가 없다)
3. Page fault
    - Swap area에서 참고하여, 물리적 메모리에 올림
4. 다시 페이지 테이블에 접근하여, 물리 메모리 주소를 찾음.

- 페이지 테이블을 프로세스당 하나...
- 테이블 접근하기 전에, 찾는 TLB(Translation Lookaside Buffer) 라는 공간도 있음.
    - 여기서 찾고자하는 논리 주소의 값이 있으면 바로 물리 주소를 반환.
    - 없으면 페이지 테이블로 조회하러 감.

## CPU의 연산 이란?
![image](https://user-images.githubusercontent.com/22140570/143863331-7a8fc482-7353-4a67-80ae-fca2c413d434.png)
- CPU는 연산할 때, 메모리의 값을 참조한다.
    - 참조하는 메모리 값은 보통 레지스터에 있음 (아주 용량이 작은 메모리)
    - 레지스터에 원하는 정보가 없으면 Main memeory (보통 Physical Memeory라 부름) 에서 참조하게 됨.
    - CPU는 main memory의 값까지만 참조할 수 있게 됨.


## 프로그램이 실행되는 것이란?
![image](https://user-images.githubusercontent.com/22140570/143863461-c861d110-e1dd-4c71-bd70-b323744f634a.png)
- 프로그램 실행 이란, 실행에 필요한 정보들을 메모리에 올리는 걸 의미.

## 주소 바인딩이란?
![image](https://user-images.githubusercontent.com/22140570/143863654-b0247cb0-80cd-481c-987d-7ee3d2b7de0b.png)
- 논리 주소(Logical Address)가 물리 주소(Physical Address)로 맵핑되는 것
- 논리 주소는 물리 주소와 같을 수도, 다를 수도
- 물리적 주소가 결정되는 시점에 따라서, 주소 바인딩이 나뉜다.
    1. 컴파일
    2. 로드 타임
    3. 실행 시간

## MMU 메모리 관리 장치
![image](https://user-images.githubusercontent.com/22140570/143864193-540a994c-b658-4ac5-bbdd-5ade9ead399f.png)


## Swap Area
![image](https://user-images.githubusercontent.com/22140570/143864436-33723399-f0d3-418d-9619-70ef6bf66a3c.png)
- 외부 장치 (I/O 작업 발생!)
- 물리 메모리의 공간이 부족하기 때문에 등장.
- 실행 중인 프로세스의 메모리 주소를 일시적으로 Disk에 저장하는 것.
- 스왑 영역에 저장되는 주소들은, 프로세스가 동작 중에만 즉 **일시적**으로 저장됨.


# 참고
- https://www.youtube.com/watch?v=5pEDL6c--_k
- http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788993712476
- https://cyber0946.tistory.com/51