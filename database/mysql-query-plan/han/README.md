# SELECT 쿼리 실행 순서
- 작성하는 쿼리문의 순서와, 실제 DB에서 실행되는 순서가 다름.
- 보통 Select query 문법 순서
    1. SELECT
    2. FROM
    3. WHERE
    4. GROUP BY
    5. ORDER BY
- 실제 실행 순서
    1. FROM
        - Sub Query.. 있으면 임시테이블 생성됨.
    2. ON
    3. JOIN
    4. WHERE
    5. GROUP BY
    6. HAVING
    7. SELECT
    8. DISTINCT
    9. ORDER BY
    10. LIMIT, OFFSET

- 참고
    - https://dion-ko.tistory.com/85
    - https://daeuungcode.tistory.com/144


# MySQL Explain
- 기본적으로 SELECT, INSERT, DELETE, REPLACE, UPDATE 쿼리문의 실행플랜을 분석하는 데 사용하는 키워드.
- 해당 쿼리를 실제 실행하는 것은 아니고, 데이터베이스에게 어떻게 실행할 건지 계획을 받아보는 방법임.
- 아래와 같이 사용함. 
```sql
EXPLAIN 
SELECT * 
FROM foo 
WHERE foo.bar = 'infrastructure as a service' OR foo.bar = 'iaas';
```
- `DESCRIBE` 라는 방법도 있는 데 이는 `Explain`이란 동일한 기능인듯.
    - 참고
        - https://dev.mysql.com/doc/refman/5.7/en/describe.html


## Explain 결과 이해하기

### table
- 어떤 테이블에 접근하고 있는지 알려줌.

### id
- SELECT를 몇번이나 실행하는 지의 숫자를 나타낸 값인듯.
- 해당 쿼리가 Subquery 또는 Union query를 포함하고 있지 않으면 항상 1의 값을 가짐.

### select_type
- SELECT Query에 대한 타입
- SIMPLE (서브쿼리, 유니온 쿼리 없이 실행된 쿼리)
- SUBQUERY
- UNION
- DERIVED
- 참고
    - https://dev.mysql.com/doc/refman/5.7/en/explain-output.html#explain_select_type

### partitions
- 해당 테이블이 파티셔닝 되어있을 경우, 사용되는 필드
- `NULL` 은 해당 쿼리에서 사용되는 테이블이 파티셔닝 되지 않았을을 의미


### type
- 어떻게 해당 테이블에 접근하고 있는가를 나타낸 필드
- 이 필드는 실행될 쿼리의 **효율성**을 판단하는 데 가장 중요한 필드 임
- `system`
    - 테이블에 하나의 row만 있을 경우
- `const`
    - 테이블에 하나의 매칭된 row만 있을 경우
    - 이 타입은 해당 쿼리가 굉장히 빠르게 실행될 수 있음을 의미
- `eq_ref`
    - 조인을 사용할 때 나타나는 타입
    - 이전 테이블과 조인을 할 때, 지금 테이블에서 하나의 로우만이 매칭된다는 의미인듯.
    - **Join할 때의 best practice**
    - 해당 row가 Prmiary key 혹은 Unique key이면서 NOT NULL인 인덱스 일 경우에 해당 타입이 사용됨.
- `ref`
    - 조인을 사용할 때 나타나는 타입
    - Primary key, Unique key가 아닌 Key와 매칭되는 경우
    - 즉 조인할 때, 하나의 row만으로 되지 않는 경우
    - 만약에 매칭되는 row의 수가 적다면, 이 또한 조인할 경우 좋은 타입이라 말할 수 있음.
- `ref_or_null`
    - `ref` 타입과 비슷
    - 그렇지만 해당 row `null` 이 포함된 row까지 탐색하고 있다는 뜻임.
    ```sql
    SELECT * FROM ref_table
    WHERE key_column=expr OR key_column IS NULL;
    ```
    - 아무래도 `null` 까지 탐색 조건에 들어가니, `ref` 타입보다는 별로이지 않을까.

- `range`
    - 인덱스를 사용해서, 특정 row 에 범위 접근할 때 사용되는 타입
    - Explain 결과 colum 중 `key` 는 해당 인덱스가 가리키고 있는 column임
    - `=`, `<>`, `>`, `>=`, `<`, `<=`, `IS NULL`, `<=>`, `BETWEEN`, `LIKE`, `IN()`, 연산자를 사용할 때 보통 나타나는 타입임
    ```sql
    SELECT * FROM tbl_name
    WHERE key_column = 10;

    SELECT * FROM tbl_name
    WHERE key_column BETWEEN 10 and 20;

    SELECT * FROM tbl_name
    WHERE key_column IN (10,20,30);

    SELECT * FROM tbl_name
    WHERE key_part1 = 10 AND key_part2 IN (10,20,30);
    ```
- `ALL`
    - 전체 행 스캔, 테이블에 존재하는 모든 데이터 접근
    - 대부분의 경우, 이 타입은 좋지 않음
    - 그래서 보통 인덱스를 추가하여, 해당 타입의 사용을 피함
- `index` 
    - 인덱스 풀 스캔
    - 인덱스를 처음 부터 끝까지 검색하는 경우
    - 이 타입도 좋지 않음.

- 참고
    - https://dev.mysql.com/doc/refman/5.7/en/explain-output.html#explain-join-types

### possible_keys
- 사용 가능성이 있는 인덱스 목록을 말함.

### key
- `possible_keys` 항목 중에, 실제 옵티마이저가 사용하겠다고 선택한 인덱스를 의미.
- 만약 컬럼이 `null` 이면, 인덱스를 사용할 수 없다는 의미인듯.

### key_len
- `key` 필드 인덱스의 길이를 의미.
- 너무 길면 비효율적이다.

### rows
- 이 전 컬럼들의 접근방식들을 이용하여, 데이터베이스에서 몇개의 row를 가지고 왔는지 의미하는 컬럼
- 실제 행 수와는 반드시 일치하지는 않는다.

### filtered
- `rows` 컬럼의 값이, WHERE 조건을 거쳤을 경우, 몇행이나 **남아있는지** 알려주는 컬럼.
- 실제 행 수와는 반드시 일치하지는 않는다.

### extra
- 옵티마이저가 어떻게 동작할 지 알려주는 힌트.
- 이 컬럼이 `Using filesort` 나 `Using Temporary` 라는 값이면 해당 쿼리 실행에 성능적인 문제가 있을 수도 있음을 암시한다.

- `Using filesort` 
    - `ORDERB BY` 조건에 있는 index를 사용할 수 없을 때, 
    - 해당 데이터의 정렬을 위해서, MySQL이 추가적인 작업이 필요함을 의미함.
    - 모든 row를 읽고, sort key를 저장한다음에, 정렬하는 작업이 발생함. 
    - `sort_buffer_size` 만큼의 메모리 할당등이 필요하므로.. 엄청나게 많은 rows가 filesort 과정을 거친다면 데이터베이스에 부담이 될 수 있을듯.
    - 참고
        - https://dev.mysql.com/doc/refman/5.7/en/order-by-optimization.html

- `Using temporary`
    - 이 쿼리를 위해, 임시적인 테이블 생성이 필요함을 의미.
    - 보통 이 힌트는, 해당 쿼리의 `GROUP BY` 와 `ORDER BY`가 포함되어 있고, 다른 컬럼을 가르킬 경우 발생함.

- `Using where`
    - where 조건을 이용하여, `rows` 행을 `filterd` 하였음을 의미.
    - `type` 이 `ALL` 혹은 `index` 타입이라면, 해당 쿼리에 뭔가 문제가 있다는 의미

- `Using index`
    -  인덱스만을 가지고, 데이터를 추출했음을 의미함.
    - `InnoDB` 의 경우, user-defined clustered index 를 가지고 있으면 그 인덱스가 `Extra` 에 나타나있지 않더라도, 사용되어질 수 있음.
    - 단 그 type 은 `index` 이고, `key` 가 Primary일 경우


# MySQL의 index 사용
- 참고
    - https://stackoverflow.com/questions/586381/mysql-not-using-indexes-with-where-in-clause


# 참고
- https://www.exoscale.com/syslog/explaining-mysql-queries/#:~:text=In%20MySQL%2C%20EXPLAIN%20can%20be,as%20a%20service'%20OR%20foo.
- https://cheese10yun.github.io/mysql-explian/
- https://www.eversql.com/mysql-explain-example-explaining-mysql-explain-using-stackoverflow-data/
- https://nomadlee.com/mysql-explain-sql/
- https://www.sitepoint.com/using-explain-to-write-better-mysql-queries/#:~:text=Extra%20%E2%80%93%20contains%20additional%20information%20regarding,may%20indicate%20a%20troublesome%20query.