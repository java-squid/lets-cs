# 인덱스

### 인덱싱이 무엇이고, 어떻게 구성되어 있는가?

인덱싱은 데이터베이스 파일에서 레코드를 빠르게 검색 할 수 있는 자료구조 기술이다. 인덱스는 열이 2 개 뿐인 작은 테이블이다. 첫 번째 열은 테이블의 기본 또는 후보 키 사본으로 구성된다. 두 번째 열에는 특정 키 값이 저장된 디스크 블록의 주소를 보유하기 위한 포인터 세트가 있다.

### 인덱싱 왜 사용해야할까?

**인덱스란 추가적인 쓰기 작업과 저장 공간을 활용하여 데이터베이스 테이블의 검색 속도를 향상시키기 위한 자료구조다.** 만약 우리가 책에서 원하는 내용을 찾는다고 하면, 책의 모든 페이지를 찾아 보는 것은 오랜 시간이 걸린다. 그렇기 때문에 책의 저자들은 책의 맨 앞 또는 맨 뒤에 색인을 추가하는데, 데이터베이스의 index는 책의 색인과 같다.

데이터베이스에서도 테이블의 모든 데이터를 검색하면 시간이 오래 걸리기 때문에 데이터와 데이터의 위치를 포함한 자료구조를 생성하여 빠르게 조회 할 수 있도록 돕고 있다.

### 인덱스를 사용하면 좋은 케이스

- 규모가 작지 않은 테이블
- INSERT, UPDATE, DELETE가 자주 발생하지 않는 컬럼
- JOIN이나 WHERE 또는 ORDER BY에 자주 사용되는 컬럼
- 데이터의 중복도가 낮은 컬럼

### **인덱스는 관리해줘야 한다!**

DBMS는 index를 항상 최신의 정렬된 상태로 유지해야 원하는 값을 빠르게 탐색할 수 있다. 그렇기 때문에 인덱스가 적용된 컬럼에 INSERT, UPDATE, DELETE가 수행된다면 연산을 추가적으로 해주어야 하며 그에 따른 오버헤드가 발생한다.

인덱스를 사용하는 것 만큼이나 생성된 인덱스를 관리해주는 것도 중요하다. 그렇기 때문에 사용하지 않는 인덱스는 바로 제거를 해주어야 한다.

### 인덱스의 자료구조

인덱스를 구현하기 위해서 여러 가지 자료구조를 사용 할 수 있다. 

### 인덱싱 방법의 유형 (types of Indexing methods)

![](https://www.guru99.com/images/1/070119_0833_IndexinginD1.png)

**Index**

- Primary Index
    - Dense Index
    - Sparse Index
- Secondary Index (= non-clustering Index)
- Clustering Index
- Multilevel Index
    - B-tree Index

### 인덱싱의 장점

- 인덱싱의 가장 큰 이점은 해당 데이터를 검색하는 데 필요한 총 I/O 작업 수를 줄이는 데 도움이된다. 그래서 사용자에게 더 빠른 데이터 검색(search) 및 검색(retrieval)을 제공한다.
    - search : 모르는 것을 검색하여 찾아냄
    - retrieval : 이미 아는 것, 저장된 것을 검색하여 찾아냄

### 인덱싱의 단점

- 인덱싱 데이터베이스 관리 시스템을 수행하는 가장 큰 단점은 테이블에 고유한 값을 가진 기본 키가 필요하다.
- 인덱스는 추가 디스크 공간을 차지한다.
- 인덱스는 INSERT, UPDATE 및 DELETE를 느리게하지만 WHERE 조건에 인덱싱 된 필드가 있는 경우 UPDATE 속도를 높입니다. 각 작업에서 인덱스도 업데이트해야하므로 INSERT, UPDATE 및 DELETE가 느려집니다.
- 인덱스를 잘못 사용 할 경우 오히려 성능이 저하되는 역효과가 발생할 수 있다.

### 출처

- [https://mangkyu.tistory.com/96](https://mangkyu.tistory.com/96)
- [https://www.guru99.com/indexing-in-database.html](https://www.guru99.com/indexing-in-database.html)