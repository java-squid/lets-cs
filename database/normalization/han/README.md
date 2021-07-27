# 정규화란?
- 보통 데이터베이스 설계 이론을 의미하는 듯. (Normalization Theory)
- 관계형 데이터 베이스를 잘 다루고 이해하는 데 필요한 기술

# 왜 정규화를 하는가?

1. 변칙을 방지할 수 있음.
- 변칙이란?
  - 데이터가 논리적으로 불일치가 일어나는 상태
  - 모순이 발생한 상태
  - ![](https://www.guru99.com/database-normalization.html)
    - Robet Phill 의 주소는 3 street 34 일수도.. 혹은 5 avenue 일 수도 있음 --> 이러한 데이터간 관계가 엇갈리는 경우를 모순이라 말하는 듯.

2. 변칙을 발생 시키는 원인은?
- 중복
    - 중복되면, 데이터베이스에 낭비가 발생함.
- 즉 위 데이터로만 봤을 때, Robet Phill의 주소 데이터가 중복되기 때문에, 명확하지 않은 관계를 가지고 있게 되고 결국 변칙이 발생함.
- 이러한 중복을 제거하는 데 도움이 되는 것이 정규화

# 졍규형
- 종류
    - 1NF (First Normal Form)
    - 2NF (Second Normal Form)
    - 3NF (Third Normal Form)
    - BCNF (Boyce-Codd Normal Form)
    - 4NF (Fourth Normal Form) --추후
    - 5NF (Fifth Normal Form) --추후 
    - 6NF (Sixth Normal Form) --추후
- 특징
    - 높은 단계의 정규형은 그 이전의 정규형 조건을 만족한다.
    - DB 설계에서 가장 중요한 정규형은 BCNF 와 5NF

## 제 1정규형
- 조건
    - 릴레이션이어야 할 것.
    - 즉 테이블에서 릴레이션이란 무엇인가, 어떤 것인가가 해당 유형의 주제
    - 테이블이 1NF 을 만족하기 위해 요건
        1. 테이블의 셀은 반드시 **하나의 값**만 가지고 있어야함
        2. 각각의 레코드(행)는 **유니크**해야함.

- 만족하지 않는 형태
![image](https://user-images.githubusercontent.com/22140570/126066503-8e56f2ca-eb3a-492e-9909-96662fe5b02e.png)
    - COURS ID, GRADE에 여러개의 값을 가지고 있음.

- 위 조건을 만족하는 형태
![image](https://user-images.githubusercontent.com/22140570/126066539-533b53be-98c6-47be-a615-1ed96f6cdf4e.png)

	
- 관계 있는 개념들
    - Primary key, Composite key, 함수 종속성 FD(Functional Dependency)


## 제 2정규형
- 조건
    - 제 1정규형을 만족하면서..
    - 후보키의 집합에서, 키가 아닌 속성(Non-prime Attribute) 에 연결되어 있는 함수 종속성이 제거된 상태
    - 후보키는..?
        - 그 릴레이션에 포함된 튜플의 값을 고유하게 하는 속성의 집합
        - 추가 속성이 없는 집합
        - 반면 슈퍼키는..
            - 슈퍼키도 튜플의 값을 고유하다는 점에서 후보키와 동일하지만,
            - 추가 속성(고유한 값을 구하는데 도움되지 않는 값)을 가지고 있음.
        - 그래서 후보키는 수퍼키의 일종이고, 다만 속성의 수가 최소한임.

- 만족하지 않는 형태
![image](https://user-images.githubusercontent.com/22140570/126066572-f2aa8568-55aa-41d1-9ceb-1c7c1f671f4f.png)
    - Non trivial FD, 즉 자명하지 않는 함수 종속성이 존재한다.
    1. {학번, 과목코드} : 키, {성적}, {학부}, {등록금} : 속성
        - 키 -> 속성 종속성이 있음.
    2. 그런데 부분 함수 종속성이 존재한다.
        - {학번} -> {학부}
        - {학번} -> {등록금}
        - 중복이 존재한다. 학부, 등록금이 중복되어 들어가 있다 
    3. 제 2정규형에서는 부분 함수종속성 없애는 것이 핵심이다. 어떻게 없앨 것인가?
    4. 무손실 분해를 생각하면서 + 어떤 부분 함수 종속성이 있는지 생각하면서
    5. 릴레이션을 나눈다.
        - {학번, 과목코드} -> {성적}
        - {학번} -> {학부}, {등록금}

- 만족하는 형태
![image](https://user-images.githubusercontent.com/22140570/126067334-1adbf269-72ed-486c-8455-fdb703787a1b.png)


## 제 3정규형
- 조건
    - 제 2정규형을 만족하면서..
    - 추이 함수 종속성 (Transitive Dependency) 이 제거된 상태
- 추이 함수 종속성이란?
    - 학번 -> 학부 -> 등록금
    - 즉 학번을 알면, 학부를 알 수 있고, 학부를 알면 등록금을 알 수 있는 상태를 추이 함수 종속성을 가지고 있다고 한다.
- 이 관계에서도.. 중복이 발생한다.
    - 찾아보면, 같은 학부이면 등록금이 같을 것이다..

- 만족하는 형태
![image](https://user-images.githubusercontent.com/22140570/126067720-ad5dbfb5-f697-4b48-ad9f-71871454cfa6.png)
    - 무손실 분해를 통해, 릴레이션을 나누고, 중복을 줄여주자

## BCNF (보이스코드 정규형)
- 조건
    - 1, 2, 3 정규형 형태를 만족하면서..
    - 슈퍼키만 함수 종속하는 상태
- 슈퍼키란?
    - 각 행을 유일하게 식별할 수 있는 하나 또는 그 이상의 속성들의 집합

- 만족하지 않는 형태
![](https://i.imgur.com/qJamHf6.png)
  - A, B 라는 후보키 외에, C 라는 속성을 통해, B를 찾을 수 있음.
  - 슈퍼키로만 함수 종속이 유일하지 않다.

# 참고
- 관계형 데이터베이스 실전 입문 3장
- https://www.guru99.com/database-normalization.html
- https://lee-mandu.tistory.com/478?category=633572
- https://yaboong.github.io/database/2018/03/09/database-normalization-1/

--- 

# QnA
![image](https://user-images.githubusercontent.com/22140570/127156592-c36bc217-08d7-46c1-8aaa-556d131862df.png)
- Trivial FD (Trivial Functional Dependecy)
    - 자명한 함수 종속성
    - 자명한 함수 종속성은 릴레이션에서 제외될 수 없다는 특징을 가지고 있음.
    - A -> B
        - A 값을 알고 있으면 B를 알 수 있다. (B는 중복이 허용된다. 즉 C -> B 라는 릴레이션이 있다면, C를 통해 B 를 알아도 문제가 없다는 뜻)
- Non trivial FD
    - 자명하지 않음 함수 종속성
    - A -> B 가 자명하지 않은 함수 종속성이면, A를 통해 B만 알 수 있는 것이 아닌, C도 D도 알 수 있다.
    - 즉 A가 반복해서 나타난다.

![image](https://user-images.githubusercontent.com/22140570/127156486-e78fdde3-0bdf-4b3e-9b20-b8fcc624739b.png)
- 부분 함수 종속성
    - 중복이 발생하고 있는 것.
    - {이름, 학과} 라는 키가 있을 경우, {학년} 이라는 속성에 함수 종속성을 가지고 있음.
    - 또한 {이름} -> {학년} 함수 종속이 함을 알 수 있고, 이름이 중복될 수 있기에, 여러 속성이 등록될 것임 (중복)
    - 이 관계를 부분 함수 종속성을 가지고 있다고 말하는 듯.
    - 제 2 정규형에서는 이러한 부분 함수 종속성을 없애는 (무손실 분해) 과정임.