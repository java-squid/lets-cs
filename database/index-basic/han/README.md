# index란?
- 인덱스는 색인
- 색인은 키워드가 문자 순으로 정렬, 해당 키워드가 있는 페이지가 적혀있음.
  - 즉 정렬된 키워드를 탐색하여, 그 키워드에 대해 알아보기 위한 곳으로 인도해주는 역할을 하고 있다고 볼 수 있음.
- 데이터 베이스에서 인덱스는?
  - 정렬된 데이터임.
  - 즉 테이블에 본 데이터를 빠르게 찾기 위한, 특정한 형태로 생성되어 정렬된 형태의 데이터를 일컫는다고 생각하면 좋을듯.

# index 구성
![](https://t1.daumcdn.net/cfile/tistory/99240D3359FEEDA92A)
- key + pointer (실제 레코드를 가르키고 있는 페이지 id value)

# index의 종류
- B-tree, B+tree, Hash, Fractoal-Tree
- [B-tree vs B+tree 에 대해 더 알아보기..](https://zorba91.tistory.com/293)

## MySQL 
- 인덱스는 페이지 단위로 관리
- 기본적으로 페이지 크기는 16kb
- MySQL은 DB engine으로 InnoDB를 사용 중이고 이는 B-tree 로 
이루어져있다고 알려져있음. (확실하진 않다!)
  - 추가적으로 hash, full-text index등을 지원하는 등 (5.7 기준)
- B-tree의 핵심은 데이터가 정렬된 상태로 유지되는 것.
- 그리고 리프, 브랜치 노드에 모두 데이터 저장 가능하다.
- [참고](https://stackoverflow.com/questions/65353975/how-to-view-the-b-tree-index-structure-generated-by-innodb-of-mysql)

## Oracle
- B-tree, Bitmap, Partioned, Function-base, Domain.. 인덱스를 사용할 수 있다!
- [참고](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_5010.htm)

# index 는 어떤 컬럼에 거는 게 좋을까?
- 해당 컬럼의 Cardinality 가장 높은 것으로 잡아야함.
- Cardinality?
    - 해당 컬럼의 중복된 수치
    - 중복도가 낮으면, Cardinality는 높고 (주민등록번호)
    - 중복도가 높으면, Cardinality는 낮다 (성별..)
- 왜?
    - 인덱스를 사용하는 이유는 원하는 컬럼에 빠르게 접근하기 위해.
    - 중복도가 높은 값에 인덱스가 있으면.. 해당 인덱스는 효율이 떨어진다고 여겨질 수 있을 것이다.



# 참고
- 관계형 데이터 베이스 실전 입문 11장
- https://mangkyu.tistory.com/96?category=761304
- https://jojoldu.tistory.com/243
- https://joosjuliet.github.io/index/
- https://joosjuliet.github.io/index_structure/
- https://joosjuliet.github.io/index_guidline/
