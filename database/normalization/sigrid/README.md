# 데이터베이스 정규화
## 정규화란
#### 함수적 종속성을 이용해서 연관성 있는 속성들을 분류하고, 각 릴레이션들에서 이상현상이 생기지 않도록 하는 과정을 말한다.
* 정규화의 기본 목표는 중복된 데이터를 없애는 것이다. 즉, removing redundnacy를 해서 발생할 수 있는 anomalies를 방지한다.
* 중복된 데이터를 허용하지 않으면서 무결성(integrity)를 유지할 수 있다. 정규화를 거친 테이블을 각각의 테이블이 하나의 기능만 수행하게 되어서, 향후 업무가 증가하거나 변화해도 최소한의 수정만으로 처리할 수 있게 된다.
* 단점도 존재한다. 정규화를 한다는 것은 테이블을 세세하게 쪼갠다는 것인데 그 만큼 JOIN 연산을 많이 해야 하고 결국 조회할 때 성능이 떨어질 수밖에 없다.
## 이상현상의 종류
![](https://i.imgur.com/yjYViCW.png)
* 위와 같은 테이블이 있다고 가정하자.
    * 한 row를 유일하게 식별할 수 있는 복합키로 (학번, 과목코드) 가 있다고 가정하자.
    * 한 학생은 하나의 학부에만 속할 수 있다.

### 삽입 이상 Insertion Anomaly
#### 새 데이터를 삽입하기 위해 불필요한 데이터도 함께 삽입해야 하는 문제를 삽입이상 이라고 한다.

* 수업을 하나도 수강하지 않은 학생이 있다고 하자.
* 현재 기본키는 복합키인 (학번, 과목코드) 이다. 따라서 과목코드는 불필요하게 NULL이 되어야 한다. 불필요한 NULL 값, 또는 ```미수강``` 과 같이 별도의 데이터가 불필요하게 들어가는 것을 ```삽입이상``` 이라고 부른다.

### 갱신 이상 Update Anomaly
#### 중복 튜플 중 일부만 변경하여 데이터가 불일치하게 되는 모순의 문제를 갱신이상 이라고 한다.
* 야붕쿤이 컴공에서 전전으로 전과한다고 생각해보자. 그렇다면 우리는 위의 테이블에 있는 모든 야붕쿤의 row를 컴공에서 전전으로 바꿔야 한다.
* 트랜잭션이 일어나다가 에러가 발생해서 하나의 row에 변동사항이 적용되지 않았다고 가정한다면, 우리는 이것을 보고 ```갱신 이상```이라고 부른다.

### 삭제 이상 Delete Anomaly
#### 튜플을 삭제하면 꼭 필요한 데이터까지 함께 삭제되는 데이터 손실의 문제를 삭제이상 이라고 한다.
* 팥빵이 POD032939 ID의 코스를 drop했다고 가정하자. 그렇다면 해당 row를 지워야 하는데 그럴 경우 테이블에서 아예 팥빵의 학생 정보가 모두 날라가게 된다.
* 수강취소를 반영하기 위해 학생등록정보를 모두 날리게 되는 것이다. 우리는 이것을 보고 ```삭제 이상```이라고 부른다.

### 이상 현상이 발생하는 이유는 무엇인가?
#### 정규화가 되어 있지 않은 테이블 설계 때문이다.
* 개발할 때 우리가 OOP를 지향해서 관심사를 분리하면 코드의 재사용성과 유지보수의 편의성이 높아지는 것처럼 데이터베이스 설계에서도 비슷한 원칙이 적용된다.
* 이론적으로는 정규화를 수행하려면 속성들간의 관련성을 파악해야 하는데, 이 속성들간의 관련성을 ```함수적 종속성(Functional Dependency)``` 라고 한다.
* 일반적으로 하나의 릴레이션에는 하나의 함수적 종속성만이 존재하도록 정규화를 하게 된다.
## 함수적 종속성 Functional Dependency
* ```X->Y``` 가 있다고 가정하자.
    * X는 결정자이다.
    * Y는 종속자이다.
    * Y는 X에 종속되어 있다.

* 예시: 학생 테이블

![](https://i.imgur.com/m01SmBP.png)
* 학번에 의해서 학생 이름과 학부는 고유하게 결정된다. 해당 테이블에서 학번은 기본키이다.
* 학번은 결정자이며, 학생 이름과 학부는 종속자이다.
* 학생 이름과 학부는 학번에 종속되어 있다.
* 그래서 우리는 다음과 같이 표현할 수 있다.
    * ```학번 -> (학생 이름, 학부)```

* 예시 2: 학생 테이블 2

![](https://i.imgur.com/1A0cGRX.png)
* 해당 테이블에서 (학번, 과목 코드) 가 기본키이자 복합키이다.
* 학번과 과목 코드에 따라서 성적이 결정된다.
* 학번과 과목 코드에 따라서 이름이 결정된다.
* 따라서 학번과 과목 코드는 결정자이며, 성적과 이름은 종속자이다.
* 성적과 이름은 학번과 과목 코드에 종속되어 있다.
* 그래서 우리는 다음과 같이 표현할 수 있다.
    * ```(학번, 과목 코드) -> 성적```
    * ```(학번, 과목 코드) -> 이름```
* 그런데 학번에 따라서 이름이 결정된다.
* 따라서 학번은 결정자이며, 이름은 종속자이다.
* 이름은 학번에 종속되어 있다.
* 그래서 우리는 다음과 같이 표현할 수 있다.
    * ```학번 -> 이름```

#### 여기에서 우리는 이름이 해당하는 함수적 종속성이 두 개임을 발견할 수 있다.

### 부분 함수적 종속 Partial Functional Dependency
#### 속성집합 Y 가 속성집합 X 의 전체가 아닌 일부분에도 함수적으로 종속됨을 의미한다.
* 우리는 위 테이블에서 (학번, 과목 코드) 가 기본키임을 알 수 있다.
* 따라서 속성집합 Y는 이름이고, 속성집합 X는 (학번, 과목 코드) 이다.
* 즉, 속성집합 Y는 속성집합 X의 전체에 함수적으로 종속되고 있다.
    * ```(학번, 과목 코드) -> 이름```
* 하지만 속성집합 X의 일부인 학번 만으로도 속성집합 Y의 함수종속 관계를 발견할 수 있다.
* 즉, 속성집합 Y는 속성집합 X의 일부에 함수적으로 종속되고 있다.
    * ```학번 -> 이름```
* 우리는 이러한 경우를 보고 부분 함수적 종속이라고 부른다.

### 완전 함수적 종속 Full Functional Dependency
#### 속성집합 Y 가 속성집합 X 전체에 대해서만 함수적으로 종속된 경우를 말한다.
* 우리는 위 테이블에서 (학번, 과목 코드) 가 기본키임을 알 수 있다.
* 따라서 속성집합 Y는 성적이고, 속성집합 X는 (학번, 과목 코드) 이다.
    * ```(학번, 과목 코드) -> 성적```
* 속성집합 X의 전체를 참조하지 않고 속성집합 Y를 유일하게 분기할 수 없다.
    * 위의 경우를 제외하고 함수 종속관계가 발견될 수 없다.
* 우리는 이러한 경우를 보고 완전 함수적 종속이라고 부른다.

## 제 1 정규형 (1NF; First Normal Form)
#### 릴레이션에 속한 모든 속성의 도메인이 원자 값으로만 구성되어 있으면 제1정규형에 속한다.
* 여기서 원자 값 Atomic Value라는 것은 필드에 이름이 들어간다고 하면
    * ```박진형``` 이 들어가야 하지
    * ```박진형 김한울 박지성 ``` 이런 식으로 들어가면 곤란하다는 것이다.

![](https://i.imgur.com/EILp1eM.png)

* 관계형 데이터베이스의 릴레이션은 모든 속성이 원자 값을 가지는 특성이 있기 때문에, 최소한 제1정규형을 만족해야 릴레이션이 될 자격이 있다.
* 그래서 사실 제 1 정규형은 일반적인 정규화라고 보기는 힘들다.
* 아래의 형태가 잘 된 제 1 정규형의 형태이다. 위의 테이블은 제 1 정규형이 이루어지지 않은 형태이다.

![](https://i.imgur.com/hsxeevH.png)

## 제 2 정규형 (2NF; Second Normal Form)]
#### 제1정규형에 속하면서, 기본키가 아닌 모든 속성이 기본키에 완전 함수 종속되면 제2정규형이다. 

![](https://i.imgur.com/pCFYsZP.png)

* 위에 있는 테이블의 기본키는 {학번, 과목코드} 이다. 해당 테이블의 함수적 종속성을 나타내면 다음과 같다.
    * {학번, 과목코드} -> 성적
    * {학번, 과목코드} -> 학부
    * {학번, 과목코드} -> 등록금
    * 학번 -> 학부
    * 학번 -> 등록금
    * 학부 -> 등록금
* 우리는 두 개의 부분 함수 종속성을 발견할 수 있다.
    * 가) 학부
        * {학번, 과목코드} -> 학부
        * 학번 -> 학부
    * 나) 등록금
        * {학번, 과목코드} -> 등록금
        * 학번 -> 등록금
    * 예외) ```학부 -> 등록금``` 은 부분 함수 종속성이 아니다.
        * 왜 일까? 부분 함수 종속성의 정의는 ```속성집합 X 의 전체가 아닌 일부분에도``` 종속되어 있는 것을 의미한다.
        * 여기서 속성집합 X는 ```학부``` 이다.
        * 따라서 속성집합 X가 ```학번, 과목코드``` 여서 집합의 일부인 ```학번``` 에도 종속되는 부분 함수 종속성의 경우와 다르다.
        * 속성집합 X는 기본키일 필요도 없고 후보키일 필요조차도 없다. 따라서 학부도 속성집합 X가 될 수 있으므로, 완전 함수 종속성이라고 부를 수 있다.

* 따라서 현재 ```학번->학부```, ```학번->등록금``` 두개의 부분 함수 종속성을 가지고 있다. 이를 제거해 주는 것을 제2정규화라고 한다.
* 학번, 학부, 등록금 속성을 가지는 학생 릴레이션과 학번, 과목코드, 성적 속성을 가지는 성적 릴레이션 둘로 나누어 주면 부분 함수 종속성을 제거할 수 있다.
    * ```학번->학부``` 함수종속성으로 볼때, 학번만으로 학부에 대한 결정을 지을 수 있다.
    * 하지만 현재 기본키가 (학번, 과목코드) 로 이루어져 있기 때문에 학번만으로 학부에 대한 결정을 짓는 것은 의미가 없다. 학번과 과목코드 두 개를 같이 이용해야 의미가 있다.
    * 그래서 기본키가 학번인 테이블을 별도로 분리하여 학번 만으로 학부와 등록금을 변경할 수 있도록 만들어주는 과정이 부분 함수 종속성을 제거하는 제2정규화 과정이다.

![](https://i.imgur.com/zNOJR8g.png)
* 릴레이션이 둘로 분해되면서 학부와 등록금에 대한 중복항목이 제거되었다.
* 정규화를 통해 분해된 릴레이션들이 JOIN을 통해 역정규화가 되면서 다시 원래대로 합쳐질 수 있어야 한다.
* 두 릴레이션 모두 제1정규형에 속하고, 기본키가 아닌 모든 속성이 기본키에 완전 함수 종속되므로 제2정규형을 만족한다.

### 이행적 함수 이상 Transitive Functional Dependency
#### 만약, X, Y, Z 에 대해 X->Y 이고 Y->Z 이면 X->Z 가 성립한다고 했을 때, 우리는 이를 Z 가 X 에 이행적으로 함수 종속되었다고 한다.
* 제 2 정규화를 하더라도 이상현상이 발생할 수 있다.
    * 삽입이상
        * 새로운 학부가 생기는 경우 등록된 학생(학번)이 없다면 학번속성이 NULL이 되므로 삽입할 수 없다.
    * 갱신이상
        * 컴퓨터공학부 등록금이 400으로 오르는 경우 20800399, 21500399 둘 모두 바꾸어 주지 않으면 데이터 불일치 문제가 발생한다.
    * 삭제이상
        * 21400001 학번을 가진 학생이 자퇴하는 경우, 기계공학부에 대한 정보가 함께 사라진다.
* 지금 위의 학생 릴레이션에서 다음과 같은 이행적 함수 이상을 발견할 수 있다.
    * 학번 -> 학부
    * 학부 -> 등록금
    * 학번 -> 등록금
* 어? 학번에 따라 등록금이 결정되는 것이 아니다. 학부에 따라 결정되는 것이다.
* 그래서 우리는 다음의 원칙에 따라 분류한다.
    * ```X->Y, Y->Z 함수적 종속관계로 인해 X->Z 의 이행적 함수 종속 관계가 나타나면 [X, Y], [Y, Z] 두 릴레이션으로 분해한다.```
![](https://i.imgur.com/mvKHaWm.png)
* 위의 테이블은 이행적 함수 이상을 제거한 학생과 학부 테이블이다.

#### 제 3 정규형을 만족하면 이상 현상이 사라질까?
* 아니다. 후보키를 여러개 가지고 있는 릴레이션에서는 제3정규형을 만족하더라도 이상현상이 생길 수 있다.

### 보이스-코드 정규형 (BCNF; Boyce-Codd Normal Form); Strong 3NF
#### X -> Y 는 trivial FD, 즉 Y 가 X 의 부분집합인 경우이거나, X 는 릴레이션 R 의 슈퍼키이다.
* Y가 X의 부분집합인 경우는 너무 자명하므로 넘어가도 되는 정의이다.
    * 예를 들어 AA->A, AB->A와 같은 것들이다.
* 따라서 쉽게 바꾸어 실용적으로 말하자면, 모든 결정자가 SUPER KEY인 경우 BCNF 이다.

![](https://i.imgur.com/qJamHf6.png)
* 위의 테이블은 BCNF를 위반하는 릴레이션을 보여주는 이론적 예시이다.
* 위 1NF를 만족하는 릴레이션의 경우,
    * 기본키가 아닌 모든 속성이 기본키에 완전 함수 종속이므로 2NF 를 만족한다.
        * (A, B) -> C
        * (A, B) -> D
        * (A, B) -> E
    * 기본키가 아닌 모든 속성이 기본키에 이행적 함수 종속이 되지 않으므로 3NF 를 만족한다.
        * (A, B) -> C -> B가 있지만, B는 기본키의 일부일 뿐이다.
    * 그런데 결정자 C가 슈퍼키가 아니다.
        * 기본키인 (A, B)는 슈퍼키이지만, 결정자 C에 따라 B는 중복될 수 있는 가능성이 있다. 따라서 결정자 C는 슈퍼키라고 결론지을 수 없다.

* BCNF 위반 사례
![](https://i.imgur.com/rijutAh.png)
    * ```{Student, Course}``` 를 기본키로 선정한 경우 3NF 까지 만족하지만 BCNF 를 만족하지 않는 경우에도 삽입이상, 갱신이상, 삭제이상이 생길 수 있다.
    * 삽입이상
        * Algorithms 라는 수업이 Honux 에 의해 열렸다고 하자. 하지만 수강생이 아무도 없는 경우 삽입할 수 없다.
    * 갱신이상
        * Yagom 이 담당하는 강의가 바뀌게 될 경우 수강생의 수만큼 갱신해줘야 하므로 하나라도 빠뜨리면 데이터 불일치 문제가 발생할 여지가 있다.
    * 삭제이상
        * 모찌가 자퇴해서 Computer Architecture 수업의 수강생이 없어지면 Alan Turing 이라는 강사도 사라진다.
        
* 분해
    * 가. nontrivial FD X -> Y
    * 나. 두 개의 릴레이션으로 분해한다.
        * XY 로 구성된 릴레이션 하나
        * X 와 나머지 속성들로 구성된 릴레이션 하나

    * 예시
        * nontrivial FD, 즉 서로의 부분 집합이 아닌 Instructor -> Course 를 찾았다.
        * ```Instructor, Course, Student``` 한 개의 릴레이션을 두 개의 릴레이션으로 분해한다.
            * Instructor 및 Course로 이루어진 릴레이션
            * Instructor 와 나머지 속성들로 구성된 릴레이션
                * Instructor, Student
    * 분해한 두 개의 릴레이션에서 기존 릴레이션에서 결정자 역할을 했던 속성을 키로 해준다. 그러면 BCNF 까지 만족시키는 릴레이션 두 개가 생기게 된다.

    * 위 최종 테이블의 예시의 경우 학생과 강사를 직접 연결하고 있어, 다소 어색하다는 생각이 들 수 있다. 따라서 BCNF를 위반하지 않으려고 하나의 릴레이션에 모든 데이터 칼럼을 담기 보다는, BCNF를 만족하지 않았던 테이블의 데이터가 과목ID, 학생ID, 강사ID 를 외래키로 갖는 경우를 간접 참조하는 것이 좋겠다는 의견이 있다.

### 참고한 자료
* https://yaboong.github.io/database/2018/03/10/database-normalization-2/
* http://www.vertabelo.com/blog/technical-articles/boyce-codd-normal-form-bcnf
