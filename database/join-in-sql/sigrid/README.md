# JOIN 한 방에 정리하기
![](https://i.imgur.com/qwGlsru.png)
* 깔끔하게 정리해보자.

## LEFT JOIN & RIGHT JOIN
![](https://i.imgur.com/vpvJynA.png)

#### A 테이블에 속한 것을 출력하는데, 이 때 A와 B의 교집합도 출력
* A의
```
SELECT ----
FROM -A- A LEFT JOIN -B- B
ON A.VALUE = B.VALUE
```

#### B 테이블에 속한 것을 출력하는데, 이 때 B와 A의 교집합도 출력
```
SELECT ----
FROM -A- A RIGHT JOIN -B- B
ON A.VALUE = B.VALUE
```

### A 테이블에 속한 것을 출력하는데, A와 B의 교집합은 출력하지 않음
![](https://i.imgur.com/Jwv3lJX.png)
```
SELECT ---
FROM -A- A LEFT JOIN -B- B
ON A.VALUE = B.VALUE
WHERE B.VALUE IS NULL
```

### B 테이블에 속한 것을 출력하는데, B와 A의 교집합은 출력하지 않음
```
SELECT ---
FROM -A- A RIGHT JOIN -B- B
ON A.VALUE = B.VALUE
WHERE A.VALUE IS NULL
```

## INNER JOIN

![](https://i.imgur.com/rb1x5Ah.png)

#### 교집합만 출력
```
SELECT ----
FROM -A- A INNER JOIN -B- B
ON A.VALUE = B.VALUE
```

## FULL OUTER JOIN
![](https://i.imgur.com/1GqOSTr.png)

### 모두 다 출력
```
SELECT ----
FROM -A- A FULL OUTER JOIN -B- B
ON A.VALUE = B.VALUE
```
### 두 테이블의 교집합을 제외하고 출력
![](https://i.imgur.com/BuzwD1h.png)
```
SELECT ----
FROM -A- A FULL OUTER JOIN -B- B
ON A.VALUE = B.VALUE
WHERE A.VALUE IS NULL OR B.VALUE IS NULL
```

## CROSS JOIN
#### Cartesian Product, 곧 곱집합이다.
* Cities 테이블
![](https://i.imgur.com/mNiBHzI.png)
* Transport 테이블
![](https://i.imgur.com/wsj8z8k.png)
* CROSS JOIN한 테이블
![](https://i.imgur.com/EHN56mc.png)

```
SELECT * FROM cities CROSS JOIN transport;
SELECT * FROM cities JOIN transport;
SELECT * FROM cities, transport;
```

## SELF JOIN
#### 자기 자신과의 CROSS JOIN을 의미한다.
![](https://i.imgur.com/E4ovxoO.png)
```
SELECT * FROM cities a (CROSS) JOIN cities b WHERE a.city <> b.city;
SELECT * FROM cities a, cities b WHERE a.city <> b.city;
```
#### 사용 예제
* SELF JOIN은 한 테이블 내의 row끼리 어떤 계산을 할 때 유용하게 쓰인다.
* 도시의 위,경도를 활용해 거리를 계산하는 예제이다. [링크](https://yahwang.github.io/posts/33)
