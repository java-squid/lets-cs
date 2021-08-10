# 데이터베이스에서 Key란?
- 데이터베이스 관리시스템에서 보통 키란, table (relation) 중 하나의 row (tuple) 가 유니크함을 나타내는 데 사용되는 속성을 의미하는 듯.
- 즉 해당 릴레이션에 똑같은 row (tuple) 이 존재하지 않음을 보장한다는 것.

# 키의 종류
- Primary Key, Candidate Key, Super Key, ForeignKey, Composite Key, Alternate Key, Unique Key, Natural Key..

# Primary key
- 보통 PK라 부른다.
- PK는 기본적으로 null 을 허용하거나, 중복을 허용하지 않는다.
- 즉 하나의 테이블에는, 그 PK가 유일함이 보장된다.
- 관계형 모델에는 기본키라는 개념이 없음.
    - 릴레이션에 여러개의 후보키가 있는 경우, 이 키들은 의미적, 기능적으로 차이가 없고, 어느 키던지 아래 기본키로 설정할 수 있기 때문에

# Super key
- 슈퍼키
- 슈퍼키는 모든 키들의 집합
- 또한, 슈퍼키는 해당 row가 테이블에서 unique 함을 나타내는데 도와줌.
- 슈퍼키는 여러 가지 추가속성을 가지고 있음.
    - 그리고 이 추가속성은 고유한 row를 나타냄에 있어 도움이 되지 않을 수도 있다.
- **참고 이미지**
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F74FZR%2FbtqClyMJ0sx%2Fay7x7W2V3cWukTMoyYHp51%2Fimg.png)
    - 후보키는 슈퍼키의 일종이며, 가지고 있는 추가속성의 수가 최소한 이다.

# Candidate key
- 후보키
- 릴레이션의 해당 row를 unique함을 나타낼 수 있는 속성들을 의미함.
- PK의 경우 , Candidate key 중 하나임.
- 후보키는 릴레이션에 한 개 이상 존재할 수 있다.

# Alternate key
- 대체키
- 후보키 중에서, PK로 선택받지 못한 키를 의미.


# Foreign key
- 외래키
- 2개의 릴레이션(테이블) 의 관계를 정립하는 데 사용되는 키를 의미.
- 각각의 외래키는 보통 각 릴레이션의 PK에 해당할 것.

# Composite key
- 복합키
- 2개 혹은 더 많은 속성들로 이루어진 집합으로, 해당 row(tuple) 이 릴레이션에서 유니크함을 식별하는데 사용함.
- 각각의 속성을 나눠서 보면, 유니크하지 않을 수 있음 
    - 즉 같이 합쳐서 있을 때, 집합으로 볼 때 유니크함이 보장됨을 알 수 있음.

# Unique key
- 유니크 키
- 릴레이션의 해당 row가 unique함을 나타낼 수 있음.
- PK란 다른점이라면.. nullable 하다는 점..
    - 그리고 PK는 테이블에서 하나의 컬럼에만 해당하지만, Unique 는 여러개 컬럼이 가능할듯..?
- 참고
    - https://blog.shovelman.dev/777

# Natural key
- 자연키
- 이미 이 세상에 존재하는 어떤 단어나, 꼬리표를 키로 사용하는 것.
- 중복될 가능성이 있음. 예시 --> 사람 이름.
- 그럼 Natural key는 Database에서 PK로 사용될 수 없을까?
    - 현실과 DB가 1:1 로 대응하고 있는 상황이라면.. ID로 사용해도 괜찮음.

# Surrogate key
- 대체키
- 릴레이션에 마땅한 후보키가 없을 경우, Database에서 식별을 위해 생성한, 선언된 키를 의미.
- 관계형 모델이는 자연키, 대체키란 개념이 없음.
    - 관계형 모델이는 후보키, 슈퍼키 뿐이다.
- 참고
  - https://bunhere.tistory.com/45
  - https://www.toolbox.com/tech/data-management/question/the-difference-between-a-primary-key-and-a-surrogate-key-011407/

# 참고
- https://www.upgrad.com/blog/types-of-keys-in-dbms/
- https://kosaf04pyh.tistory.com/201

- 관계형 데이터 베이스 실전 입문
    - 3장, 6장 
