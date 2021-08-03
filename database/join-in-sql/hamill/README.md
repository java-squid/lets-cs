# MySQL Join 은 무엇이고 어떤 것이 있을까?
### 개요

MySQL 조인에는 세 가지 유형이 있습니다.

- MySQL INNER JOIN(또는 단순히 JOIN 이라고도 함)
- MySQL OUTER JOIN(또는 LEFT JOIN 이라고도 함)
- MySQL RIGHT OUTER JOIN(또는 RIGHT JOIN 이라고도 함)

### MySQL INNER JOIN

INNER JOIN은 조인 조건이 충족되는 여러 테이블의 모든 행을 반환하는데 사용됩니다. 가장 일반적인 조인 유형입니다.

```sql
SELECT columns
FROM table1
INNER JOIN table2
ON table1.column = table2.column;
```

![https://static.javatpoint.com/mysql/images/image1.png](https://static.javatpoint.com/mysql/images/image1.png)

### MySQL LEFT OUTER JOIN

LEFT OUTER JOIN은 ON 조건에 지정된 왼쪽 테이블의 모든 행과 조인 조건이 충족되는 다른 테이블의 행만 반환합니다.

```sql
SELECT columns  
FROM table1  
LEFT [OUTER] JOIN table2  
ON table1.column = table2.column;
```

![https://static.javatpoint.com/mysql/images/image4.png](https://static.javatpoint.com/mysql/images/image4.png)

### MySQL RIGHT OUTER JOIN

RIGHT OUTER JOIN은 ON 조건에 지정된 오른쪽 테이블의 모든 행과 조인 조건이 충족되는 다른 테이블의 행만 반환합니다.

```sql
SELECT columns  
FROM table1  
RIGHT [OUTER] JOIN table2  
ON table1.column = table2.column;
```

![https://static.javatpoint.com/mysql/images/image7.png](https://static.javatpoint.com/mysql/images/image7.png)

### 어떤 경우에 사용할까?

조인은 일치하는 열을 기반으로 두 개 이상의 데이터베이스 테이블 간의 연결을 설정하여 테이블 간의 관계를 생성하기 위해 수행되는 SQL 작업입니다. 우리는 각각의 테이블에 대해서 조인을 사용하기도 하지만 정규화를 통해 분리한 테이블을 다시 합치는데 조인을 사용하기도 합니다.

### 출처

- [https://www.javatpoint.com/mysql-join](https://www.javatpoint.com/mysql-join)
- [https://www.techopedia.com/definition/1213/join](https://www.techopedia.com/definition/1213/join)