# Join 이란?
- 둘 이상의 테이블을 연결하여 데이터를 검색하는 데 사용하는 방법
- 보통 PK 혹은 FK 라는 공유되는 컬럼을 기준으로, 테이블을 연결함.

# 종류
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F26242F48569460C734)
- 크게 보면 3가지 정도 있는 듯
    - INNER (교집합)
    - LEFT/RIGHT (부분집합)
    - OUTER (합집합)

## INNER
![](https://i.stack.imgur.com/eJhrt.png)
- 동일한 조건 값에 있는 행만 반환
- 보통 Join이라 말하면 Inner 조인을 의미하는 듯
    - 참고 : https://stackoverflow.com/questions/565620/difference-between-join-and-inner-join
- 대부분 INNER조인을 통해, Join문제를 해결해나가는 듯.

## LEFT, RIGHT
![](https://i.stack.imgur.com/fZaT4.png)
![](https://i.stack.imgur.com/n9Bps.png)
- 어느쪽 테이블 위주로 합쳐지는가.
- LEFT는 왼쪽 테이블 기준으로, 매칭되는 컬럼이 있는 오른쪽 테이블 컬럼을 가져와서, 왼쪽 테이블에 붙인다. (왼쪽 + 공통 부분)
    -  매칭되는 컬럼이 없을 경우 `NULL` 로 합쳐짐
- RIGHT는 오른쪽 테이블 기준으로, 매칭되는 컬럼이 있는 왼쪽 테이블 컬럼을 가져와서 오른쪽 테이블에 붙인다.

## OUTER
![](https://i.stack.imgur.com/aFHpq.png)
- 합집합
- LEFT + RIGHT 조인의 결과

# 어떨떄 사용될까?
- 원하는 결과가, 하나의 테이블에 국한되지 않을 때
- Domain간 관계가 맺어져 있을 떄 (OneToMany..등등)

# 참고
- https://pearlluck.tistory.com/46
- https://dev.mysql.com/doc/refman/8.0/en/join.html
- https://stanleykou.tistory.com/entry/SQL-INNER-%EC%A1%B0%EC%9D%B8%EA%B3%BC-OUTER%EC%A1%B0%EC%9D%B8%EC%9D%B4-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%EC%9A%94
- https://edudeveloper.tistory.com/69
- https://stackoverflow.com/questions/17946221/sql-join-and-different-types-of-joins