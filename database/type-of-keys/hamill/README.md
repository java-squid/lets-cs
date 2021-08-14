# 데이터베이스에서 사용되는 키의 개념과 종류

### 키(Key)란?

키는 데이터베이스에서 조건에 만족하는 튜플을 찾거나 순서대로 정렬할 때 튜플들을 서로 구분 할 수 있는 기준이 되는 속성(attribute)를 말한다.

### 키의 종류

#### 후보키(Candidate Key)

1. 후보 키는 릴레이션을 구성하는 속성들 중에서 튜플을 유일하게 식별하기 위해 사용하는 속성들의 부분집합, 즉, 기본키로 사용 할 수 있는 속성들을 말한다.
2. 하나의 릴레이션 내에서는 중복된 튜플들이 있을 수 없으므로 모든 릴레이션에는 반드시 하나 이상의 후보키가 존재한다.
3. 후보키는 릴레이션에 있는 모든 튜플에 대해서 유일성과 최소성을 만족시켜야 한다.

```jsx
유일성: 하나의 키값으로 하나의 튜플만을 유일하게 식별 할 수 있어야한다.
최소성: 모든 레코드들을 유일하게 식별하는데 꼭 필요한 속성만으로 구성되어 있어야한다.

릴레이션: 테이블
속성: 릴레이션 최상단에 위치한 이름, 전화번호, 학과 등과 같은 것
튜플 또는 레코드: 릴레이션의 각 행
```

#### 기본키(Primary Key)

1. 기본키는 후보키 중에서 선택한 메인 키이다.
2. 하나의 릴레이션에서 특정 튜플을 유일하게 구별 할 수 있는 속성이다.
3. Null 값을 가질 수 없다.
4. 기본키로 정의된 속성에는 동일한 값이 중복되어 저장 될 수 없다.

#### 대체키(Alternate Key)

1. 후보키가 둘 이상일 때 기본키를 제외한 나머지 후보키들을 말한다.
2. 보조키라고도 한다.

#### 슈퍼키(Super Key)

1. 슈퍼키는 하나의 릴레이션 내에 있는 속성들의 집합으로 구성된 키
2. 슈퍼키는 릴레이션을 구성하는 모든 튜플에 대해 유일성을 만족시키지만, 최소성은 만족시키지 못한다.

```text
최소성: 쉽게 설명하면 키를 구성하는 속성들이 진짜 각 튜플을 구분하는데 꼭 필요한 속성들로만 
구성되어 있냐?를 의미합니다. 굳이 없어도 될 속성들을 넣지 말자는 말입니다. 예를 들어 다음과 
같은 키(주민번호, 이름, 나이)가 있다면, 물론 현재의 키는 각 튜플을 구분할 수 있습니다. 
주민번호, 이름, 나이가 모두 같은 사람을 없을 테니깐요. 근데 생각해보면 이름, 나이를 빼고도 
주민번호만으로 각 튜플을 유일하게 식별할 수 있습니다. 이때 이름, 나이를 빼면 해당 키는 최소성을 만족합니다.
```

#### 외래키(Foregin Key)

1. 관계를 맺고 있는 릴레이션 R1, R2에서 릴레이션 R1이 참조하고 있는 릴레이션 R2의 기본키와 같은 R1 릴레이션의 속성을 외래키라고 한다.
2. 외래키는 참조되는 릴레이션의 기본키와 대응되어 릴레이션 간에 참조관계를 표현하는데 중요한 도구이다.


#### 출처
- [https://coding-factory.tistory.com/220](https://coding-factory.tistory.com/220)
- [https://kosaf04pyh.tistory.com/201](https://kosaf04pyh.tistory.com/201)