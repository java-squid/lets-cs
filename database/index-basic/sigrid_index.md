# Database 7월 1주차
### Index란 무엇인가?
#### 도입
* 내가 데이터베이스 서적을 보고 있다고 치자. 그런데 이 DB 책이 한 1만 페이지 정도 된다고 가정해보자고. 내가 여기서 Index에 대한 설명을 찾고 싶다 라고 치면은 이걸 1만 페이지 내내 넘겨보면서 찾겠냐? 그건 절대 불가능하다.
* 물론 목차라는 것이 있지. 목차는 주로 중요한 Theme별로 서술이 되어 있다라는 거지. 그런데 말이야, 내가 이 저자가 Index를 얼마나 중요하게 생각하는 지 어떻게 알깜? Index 라는 단.어.를 목차에서 찾는 것도 졸라 빡시겠지?
* 그래서 보통 두꺼운 책은 책 맨 뒤에 색인이라는 게 있지. ABC 색인, 가나다 색인, 뭐 성경 목차에 따른 색인(토라 5경이 먼저 나오고...) 이런 식으로. 여기서 색인은 정확히 데이터베이스의 인덱스와 일치하는 개념이다.
* 예를 들어 내가 다음의 서적에서 Index를 찾는다고 가정하자.
 ```
1 Intro
2 Intro
3 Intro
512 SQL
513 SQL
5544 Optimizer
5545 Transaction
5546 Transaction
5547 Transaction
5700 Transaction
5701 Transaction
5702 ConcurrenctControl
5703 ConcurrenctControl
```

* 여기서 Transaction은 5545~5700까지 저장되어 있다.
* 이걸 쿼리로 찾아서 뒤져보려면 푸하하 전부 다 뒤져보면 된다. 브루트 포스다 ㅋㅋㅋ
```
SELECT	page
FROM	db_book
WHERE	title = 'Transaction';
```
* DB는 일단 5545번에 당도하더라도 뒤에 Transaction 관련된 DB가 더 있을 것이라고 생각한다. 그리고 실제로도 더 있다. 인간이라면 5545번을 찾고, 아 여기서부터가 Transaction이겠거니 생각하는데 컴퓨터는 멍청해서 다 뒤져본다. (그리고 실제로 갑자기 8000번부터 또 Transaction 파트 II 이거면 어떡할래?)
* 우리는 이러한 멍청한 방식을 Full Scan이라고 부른다.
#### 인덱스 만들어보기
* 인덱스도 결국엔 하나의 테이블이다. index_title 이라는 이름의 테이블을 만들자.
* 우선 ‘Transaction’ 이라는 키워드를 쉽게 찾을 수 있도록 abc 순으로 정렬하면 좋을 것 같다.
```
ConcurrenctControl 5702
ConcurrenctControl 5703
Transaction 5545
Transaction 5546
Transaction 5547
Transaction 5700
Transaction 5701
```
* 중복된 내용은 해당 내용의 가장 첫 번째 페이지만 남기고 전부 지워보겠다.
```
ConcurrenctControl 5702
Intro ...
SQL ...
Optimizer ...
Transaction 5545
```
* 편의상 위의 title외에 다른 title은 없다고 가정하자.
* 이제 DB는 index_title 테이블에서 2개의 데이터만 찾아보면 되며, 심지어 문자열 순으로 정렬까지 되어있기 때문에 더욱 빠르게 ‘Transaction’ 이라는 문자열을 수 있다.
#### 코쿼 멤버스를 찾아보자.
* 코쿼가 잘 되어가지고 졸업생이 1만 명이라고 치자.
```
rowid name category location
1         Jane   2021      startup
2         Sigrid 2020      ROKAF
3         Han    2020      startup
4         Hamill 2020     NAVER
...
99         Dion   2020     KAKAO
...
101       노을   2021      startup
632         Solar  2020     startup
```
* 어떤 리쿠르터가 2020 멤버스만 다 찾아보려고 한다고 치자
```
SELECT	name, location
FROM	book_store
WHERE	category = '2020'
```
결과는?
```
name location
Sigrid ROKAF
Han   Startup
Hamill NAVER
Dion KAKAO
Solar startup
```
* 현재 인덱스가 없기 때문에, 10000개의 데이터를 모두 뒤져서 결과를 찾았을 것이다 (== full scan)
* 인덱스를 만들어보자!
	* category를 기준으로 데이터를 찾고있기 때문에, category를 기준으로 정렬해주자.
	* 그리고 DB가 쉽게 찾아갈 수 있도록 rowid를 같이 넣어주자. 이 때, 다른 컬럼까지 모두 인덱스에 넣어버리면 결국 원본 테이블과 내용이 똑같아져 공간 낭비이므로, rowid만 넣어주는 것이다.
```
category id
2020 2
2020 3
2020 632
...
2021 1
2021 101
```
* 인덱스는 문자열 순서대로 정렬되어있기 때문에, ‘2020’ 라는 문자열을 계속 검색하다가 ‘2021’ 라는 문자열을 만나는 순간 이제 더이상 ‘2020’ 라는 문자열을 존재하지 않는다고 단정짓고 탐색을 종료할 수 있다.
* rowid가 주어지면 DB는 해당 데이터의 위치가 어디있는지 일일이 찾지 않아도 rowid를 통해 바로 접근할 수 있다.
* 내부적으로 데이터를 B-Tree라는 구조에 저장하기 때문에, ‘java’라는 문자열을 찾아낼 때 맨 처음부터 순차적으로 조회하는 것 보다 훨씬 빠르다.
* 그렇게 인덱스에서 찾아낸 rowid를 기준으로 쿼리를 날리면 된다.
```
SELECT	name, location
FROM	codesquad_alumni
WHERE	rowid IN (2, 3, ..., 632)

```
### 실제 사용 시에는
실제 사용시에는 이러한 일련의 과정을 사용자가 직접 입력할 필요 없이 다음과 같이 인덱스를 생성해 놓으면 내부적으로 알아서 작동한다.
```
CREATE INDEX graduate_year ON codesquad_alumni(category)

```
위와 같이 book_store 테이블의 category 컬럼에 index_category라는 인덱스를 생성한 후, 기존과 똑같이 사용하면 된다.

```
SELECT	name, location
FROM	codesquad_alumni
WHERE	category = '2020'
```
## 참고

https://itholic.github.io/database-index/
