# MySQL Query Plan
## MySQL Optimizer
* MySQL은 사용자가 요청한 쿼리 문장을 잘게 쪼개서 MySQL 서버가 이해할 수 있는 수준으로 나눈다.
* 이를 파싱이라고 하는데, 파싱 트리를 확인하면서 어느 테이블과 인덱스부터 읽는 것이 효율적인지 계산한다.
    * 그래서 Client는 SQL 문장을 Parser에 전달하고 (1단계),
    * Parser는 파싱 작업을 거쳐 Optimizer에게 전달한다. (2단계)
    * Optimizer는 Query Plan을 작성하여 Executor에게 전달하고, Executor는 스토리지 엔진에서 결과 Set을 가져온다. (3단계)
![](https://i.imgur.com/ry2IEwt.png)

* 만약 SQL 문법이 잘못되었다면 누가 에러를 반환할까?
    * 파서가 반환한다. 파서가 문장을 분석하고 잘게 쪼개기 때문이다.
* 옵티마이저는 쿼리 플랜을 작성하는 데 있어 다음과 같은 사항을 고려한다.
    * 불필요한 조건의 제거 및 복잡한 연산의 단순화
    * 여러 테이블의 조인이 있는 경우 어떤 순서로 테이블을 읽을지 결정
    * 각 테이블에 사용된 조건과 인덱스 통계 정보를 이용해 사용할 인덱스 결정
    * 가져온 레코드들을 임시 테이블에 넣고 다시 한번 가공해야 하는지 결정
* 옵티마이저의 종류에는 무엇이 있을까?
    * 1. 비용 기반 최적화(Cost-based optimizer, CBO)
        * 쿼리를 처리할 수 있는 여러 가지 방법을 만든다.
        * 각 단위 작업의 비용 정보 및 테이블의 통계 정보를 이용해 계획 별 비용을 산출한다.
    * 2. 규칙 기반 최적화 방법(Rule-based optimizer, RBO)
        * 거의 쓰이지 않는다.
* 대체 무슨 통계 정보(?)를 보고 쓴다는 것일까?
    * 대략의 레코드 건수와 인덱스의 유니크한 값의 개수를 주로 참고한다.
    * MySQL은 통계정보를 동적으로 관리하는 편이다.
        * 오라클과 같은 전통적인 RDBMS는 통계정보만 따로 백업한다.
    * 명령 중 'ANALYZE' 를 이용하면 통계 정보를 강제로 갱신할 수 있다.
        * 인덱스 키값의 분포도(선택도)만 업데이트하며, 전체 테이블의 건수는 테이블의 전체 페이지 수를 이용해 예측한다.
        * 예를 들어, InnoDB 테이블은 인덱스 페이지 중에서 8개 정도만 랜덤하게 선택해서 분석하고 그 결과를 인덱스의 통계 정보로 갱신한다.

### EXPLAIN 명령
* MySQL에서는 'EXPLAIN' 키워드를 이용해 실행계획에 대한 정보를 살펴 볼 수 있다.
* 테이블 구조가 다음과 같이 있다고 말해보자.
    * member는 회원 정보이고, orders는 주문 목록, transaction은 해당 주문의 거래 정보이다.

![](https://i.imgur.com/V3UwUaW.png)
* EXPLAIN 결과는 다음과 같이 나올 것이다. 여기서는 JSON 형식으로 출력된 결과에 대해 알아보자.
```
explain
select m.*, o.*, t.* from member  m
inner join orders o on m.id = o.member_id
inner join transaction t on o.transaction_id = t.id
where m.id in (1, 2, 33)
```

```
{
  "query_block": {
    "select_id": 1,
    "cost_info": {
      "query_cost": "449.03"
    },
    "nested_loop": [
      {
        "table": {
          "table_name": "m",
          "access_type": "range",
          "possible_keys": [
            "PRIMARY"
          ],
          "key": "PRIMARY",
          "used_key_parts": [
            "id"
          ],
          "key_length": "8",
          "rows_examined_per_scan": 3,
          "rows_produced_per_join": 3,
          "filtered": "100.00",
          "cost_info": {
            "read_cost": "3.61",
            "eval_cost": "0.60",
            "prefix_cost": "4.21",
            "data_read_per_join": "6K"
          },
          "used_columns": [
            "id",
            "email",
            "name"
          ],
          "attached_condition": "(`sample`.`m`.`id` in (1,2,33))"
        }
      },
      {
        "table": {
          "table_name": "o",
          "access_type": "ref",
          "possible_keys": [
            "FKpktxwhj3x9m4gth5ff6bkqgeb",
            "FKrylnffj7sn97iepyqadlfnsg0"
          ],
          "key": "FKpktxwhj3x9m4gth5ff6bkqgeb",
          "used_key_parts": [
            "member_id"
          ],
          "key_length": "8",
          "ref": [
            "sample.m.id"
          ],
          "rows_examined_per_scan": 90,
          "rows_produced_per_join": 272,
          "filtered": "100.00",
          "cost_info": {
            "read_cost": "63.00",
            "eval_cost": "54.55",
            "prefix_cost": "121.76",
            "data_read_per_join": "279K"
          },
          "used_columns": [
            "id",
            "order_number",
            "member_id",
            "transaction_id"
          ]
        }
      },
      {
        "table": {
          "table_name": "t",
          "access_type": "eq_ref",
          "possible_keys": [
            "PRIMARY"
          ],
          "key": "PRIMARY",
          "used_key_parts": [
            "id"
          ],
          "key_length": "8",
          "ref": [
            "sample.o.transaction_id"
          ],
          "rows_examined_per_scan": 1,
          "rows_produced_per_join": 272,
          "filtered": "100.00",
          "cost_info": {
            "read_cost": "272.73",
            "eval_cost": "54.55",
            "prefix_cost": "449.03",
            "data_read_per_join": "820K"
          },
          "used_columns": [
            "id",
            "code",
            "partner_transaction_id",
            "payment_method_type"
          ]
        }
      }
    ]
  }
}
```

#### Table
* 어느 테이블에 대한 접근을 하고 있는가?
* 여기서 m은 members, o는 orders, t는 transactions이다.

#### ID
* id는 select 명령에 대한 순차적인 실행번호이다.
* Join은 개별적인 하나의 연산 단위로 취급하므로 모두 id가 1이라고 한다. (잘 이해가 안감.)
    * 대신 서브쿼리가 있거나 UNION 연산이 있으면 id 안에서 2, 3...으로 내려갈 것이다.

#### SELECT_TYPE
* select 문의 유형을 말한다. 각 유형은 아래와 같다
    * SIMPLE : 서브쿼리나 'union'이 없는 가장 단순한 select문을 말한다
        * ```SELECT * FROM USER;```
    * PRIMARY : 가장 바깥에 있는 select 문을 말한다
        * ```SELECT (요기) * FROM (SELECT * FROM USER) t;```
    * DERIVED : from 문 안에있는 서브쿼리의 select 문이다.
        * ```SELECT * FROM (SELECT (요기) * FROM USER) t;```
    * SUBQUERY : 가장 바깥의 select 문에 있는 서브쿼리이다.
    * DEPENDENT SUBQUERY : 기본적으로 SUBQUERY와 같은 유형이며, 가장 바깥의 select문에 '의존성'을 가진 서브쿼리의 select문이다.
        ```
        SELECT * FROM user u1 WHERE EXISTS ( 
        SELECT * FROM user u2 WHERE u1.user_id = u2.user_id (요기)
        );
        ```
    * UNCACHEABLE SUBQUERY
    * UNION : union 문의 두번째 select 문을 말한다
    * DEPENDENT UNION : 바깥 쿼리에 의존성을 가진 union문의 두번째 select문을 말한다

#### type
* MySQL이 어떤식으로 테이블들을 조인하는지를 나타내는 항목이다.
* Type을 분석함으로써 어떤 인덱스가 사용되고 사용되지 않았는지를 알 수 있고, 이를 통해 어떤식으로 쿼리가 튜닝되어야 하는 지 분석할 수 있다. [출처](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html) [출처 2](https://gradle.tistory.com/4)
    * const: 하나의 매치되는 행만 존재하는 경우. 하나의 행이기 때문에 상수로 간주되며, 한번만 읽어들이기 때문에 무척 빠르다.
    * eq_ref: 조인수행을 위해 각 테이블에서 하나의 행만이 읽혀지는 형태. const 타입 외에 가장 훌륭한 조인타입이다.
        * It is used when all parts of an index are used by the join and the index is a PRIMARY KEY or UNIQUE NOT NULL index.
    * ref: ref 조인에서 키의 가장 왼쪽 접두사 만 사용하거나 키가  PRIMARY KEY또는 UNIQUE 인덱스 가 아닌 경우 (즉, 조인이 키 값을 기반으로 단일 행을 선택할 수없는 경우) 사용된다. 사용되는 키가 몇 개의 행과 만 일치하는 경우 이는 좋은 조인 유형이다.
    * index: 이 타입은 인덱스가 스캔되는걸 제외하면 ALL과 같다. 보통 인덱스 파일이 데이터 파일보다 작기 때문에 ALL보다 빠르다. 인덱스 스캔, 테이블의 특정 인덱스의 전체 엔트리에 접근한다.
    * ALL: 이전 테이블과의 조인을 위해 풀스캔이 된다. 만약 조인에 쓰인 첫 테이블이 고정이 아니라면 비효율적이다. 그리고 대부분의 경우 아주 느리며, 보통 상수값이나 상수인 컬럼값으로 row를 추출하도록 인덱스를 추가하여 ALL 타입을 피할 수 있다.
    * ref_or_null: 이 조인 유형은 ref와 비슷하지만 MySQL이 NULL값 을 포함하는 행을 추가로 검색한다는 점이 다르다. 이 조인 유형 최적화는 하위 쿼리를 해결하는 데 가장 자주 사용된다.

* ALL, index 두 가지는 테이블 또는 특정 인덱스가 전체 행에 접근하기 때문에 테이블 크기가 크면 효율이 떨어진다. ref_or_null의 경우 NULL이 들어있는 행은 인덱스의 맨 앞에 모아서 저장하지만 그 건수가 많으면 MySQL 서버의 작업량이 방대해진다. 다시 말해서 ALL 이외의 접근 방식은 모두 인덱스를 사용한다. [출처](https://cheese10yun.github.io/mysql-explian/#explain-2)

* 접근 방식이 ALL 또는 index인 경우는 그 쿼리로 사용할 수 있는 적절한 인덱스가 없다는 의미일 수도 있다. 위 쿼리에서 Country 테이블에 대한 접근은 ALL이지만 이는 WHERE 구의 조건을 지정하지 않았기 때문이다. 그러한 쿼리에서 드라이빙 테이블에 접근한다면 전체 행을 스캔 할수 밖에 없다. [출처](https://cheese10yun.github.io/mysql-explian/#explain-2)

* 결론
성능상 문제가 되는 부분은 index, all이 두가지 타입이 문제라는 것이다.

#### key
possible_keys 필드에서 실제로 옵티마이저가 선택한 인덱스가 key가 된다. 위 EXPLAIN 에서는 County 테이블(첫 번째 행)의 Key는 NULL 인데 이는 행 데이터를 가져오기 위해 인덱스를 사용할 수 없다는 의미이다.

#### extra
* EXPLAIN을 사용해 옵티마이저의 행동을 파악할 때 아주 중요하다.
* 특히 FileSort와 Using Temporary의 경우에는 쿼리 튜닝이 필요한 상태라는 의미이다.

[출처](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=websearch&logNo=221566208749)
* Using where
    * 접근 방식을 설명한 것으로, 테이블에서 행을 가져온 후 추가적으로 검색조건을 적용해 행의 범위를 축소한 것을 표시한다.
    * 인덱스에 포함된 컬럼들이 사용된 WHERE 조건들은 대부분 스토리지 엔진에서 인덱스를 읽을 때 체크되기 때문에 WHERE 절을 가진 쿼리가 모두 실행 계획상에 Using where 를 표시하는 것은 아니다.
    * 때로는 Using where 가 있다는 사실이 다른 인덱스를 사용하면 쿼리를 더 효율적으로 만들 수 있다는 의미가 되기도 한다.
* Using index
    * 테이블에는 접근하지 않고 인덱스에서만 접근해서 쿼티를 해결하는 것을 의미한다. 커버링 인덱스로 처리됨 index only scan이라고도 부른다
* Using index for group-by
    * Using index와 유사하지만 GROUP BY가 포함되어 있는 쿼리를 커버링 인덱스로 해결할 수 있음을 나타낸다
* Using filesort
    * ORDER BY 인덱스로 해결하지 못하고, filesort(MySQL의 quick sort)로 행을 정렬한 것을 나타낸다. MySQL 이 결과의 순서를 맞추기 위해 인덱스 순서로 테이블을 읽는 것이 아니라 외부 정렬을 사용해야 한다는 것을 의미한다.
* Using temporary
    * MySQL 이 쿼리 결과를 정렬하기 위해 임시 테이블을 사용한다는 것을 의미한다.
* Using where with pushed
    * 엔진 컨디션 pushdown 최적화가 일어난 것을 표시한다. 현재는 NDB만 유효
* Using index condition
    * 인덱스 컨디션 pushdown(ICP) 최적화가 일어났음을 표시한다. ICP는 멀티 칼럼 인덱스에서 왼쪽부터 순서대로 칼럼을 지정하지 않는 경우에도 인덱스를 이용하는 실행 계획이다.
* Using MRR
    * 멀티 레인지 리드(MRR) 최적화가 사용되었음을 표시한다.
* Using join buffer(Block Nested Loop)
    * 조인에 적절한 인덱스가 없어 조인 버퍼를 이용했음을 표시한다.
* Using join buffer(Batched Key Access)
    * Batched Key Access(BKAJ) 알고리즘을 위한 조인 버퍼를 사용했음을 표시한다.

### 기타 JOIN을 최적화하는 포인트?
* MySQL은 Nested Loop JOIN으로 되어있기 때문에 기준 테이블에서 조회되는 데이터양에 따라 연관 테이블의 데이터양이 결정되기 때문에 기준 테이블(왼쪽)의 데이터양을 줄이는 것이 관건이다.
* Outer Join은 지양한다. 꼭 필요한 경우만 사용한다.
* join시 조합 경우의 수를 줄이기 위해 복합 컬럼 index를 사용하는 것을 고려한다.

## 기타 참고 사이트
* https://wedul.site/452
* https://cheese10yun.github.io/mysql-explian/#null
