# 데이터베이스 논리적 모델: 키의 종류
## 개론
* 관계 데이터 모델 릴레이션의 특징 중 하나인 '튜플의 유일성' 은 키를 통하여 성취될 수 있다.
* 튜플의 유일성은 릴레이션에 똑같은 튜플이 존재하면 안되고, 모든 튜플에는 다른 튜플과 구별되는 유일한 특성이 있어야 된다는 것이었다.
* 이를 위하여 모든 속성을 이용하는 것보다 일부 속성만 이용하는 것이 효율성을 높일 수 있다.
* 이 때, 튜플들을 유일하게 구별해주는 역할은 속성 또는 속성들의 집합인 키가 한다.

## Super Key
* 슈퍼키는 유일성의 특성을 만족하는 속성 또는 속성들의 집합이다.
* 유일성은 키가 갖추어야 하는 기본 특성으로, 하나의 릴레이션에서 키로 지정된 속성 값은 튜플마다 달라야 한다.
    * 사용자의 이름 속성은 이름이 같은 사용자도 있을 수 있으므로 슈퍼키가 될 수 없다.
    * 사용자 아이디는 모든 튜플을 구별할 수 있으므로 슈퍼키가 될 수 있다.
    * 사용자 아이디 + 사용자 이름으로 구성된 속성 집합도 모든 튜플을 구별할 수 있다. 하지만, 불필요한 속성 값까지 확인하는 비효율적인 작업을 하게 된다.

## Candidate Key
* 앞서 살펴보았듯이, 불필요한 속성 값까지 확인하는 비효율적인 작업을 하게되는 키가 존재한다.
* 그래서 꼭 필요한 속성의 집합만으로 튜플을 유일하게 구별할 수 있도록 하는 키가 후보키이다. 후보키는 유일성을 만족시키면서 동시에 최소성까지 만족하는 속성 또는 속성들의 집합이다.
    * 최소성은 위에서 말한 것처럼 꼭 필요한 최소한의 속성들로만 키를 구성하여 튜플을 구별할 수 있게 하는 것을 말한다.
* 후보키가 되기 위해 만족해야 하는 유일성과 최소성의 특성은 새로운 튜플이 삽입되거나 기존 튜플의 속성 값이 바뀌어도 유지되어야 한다.

## Primary Key
* 하나의 테이블에 존재하는 여러 후보키 중에서 기본적으로 사용할 키를 반드시 선택해야 하는데 이것을 기본키라고 한다.
* 기본키의 속성에는 다음을 고려하여야만 한다.
    * 먼저, NULL 값을 가질 수 있는 속성이 포함된 후보키는 기본키로 부적합하다. 기본키가 NULL 값인 튜플은 다른 튜플들과 구별하여 접근하기 어려우므로 이런 가능성이 있는 키는 기본키로 선택하지 않는 것이 좋다.
    * 값이 자주 변경될 수 있는 속성이 포함된 후보키는 기본키로 부적합하다. 기본키는 다른 튜플과 구별되는 값을 가지고 NULL 값은 허용하지 않으므로 이를 확인하는 작업이 필요한데, 만약 값이 자주 바뀐다면 기본키 값으로 적합한지를 지속해서 판단해야 하기 때문에 기본키로 부적합하다.
    * 단순한 후보키를 기본키로 선택한다.

## Alternate Key
* 대체키는 기본키로 선택되지 못한 후보키들이다.
* 대체키는 기본키를 대신할 수 있다.

## Foreign Key
* 외래키는 어떤 릴레이션에 소속된 속성 또는 속성 집합이 다른 릴레이션의 기본키가 되는 키다. 즉, 다른 릴레이션의 기본키를 그대로 참조하는 속성의 집합이다.
* 외래키가 되는 속성과 기본키가 되는 속성의 이름은 달라도 된다. 하지만 외래키 속성의 도메인과 참조되는 기본키 속성의 도메인은 반드시 같아야 한다.

![](https://i.imgur.com/HayFbDq.png)
[출처: 데이터베이스 개론 2판, 한빛아카데미](https://sangwoo0727.github.io/database/Database-4_key/)

* 하나의 릴레이션에는 외래키가 여러 개 존재할 수도 있고, 외래키를 기본키로 사용할 수도 있고 외래키를 포함하여 기본키를 구성할 수도 있다.
* 추가로, 외래키는 반드시 다른 릴레이션을 참조할 필요는 없다. 참조하는 릴레이션과 참조되는 릴레이션이 같을 수도 있다.
* 외래키는 기본적으로는 기본키가 아니기 때문에 널 값을 가질 수도 있고 서로 다른 튜플이 같은 값을 가질 수도 있다. [스택 오버플로우 토론](https://stackoverflow.com/questions/7573590/can-a-foreign-key-be-null-and-or-duplicate)

![](https://i.imgur.com/CTrl4om.png)


## Composite Key
* 두 개 이상의 컬럼이 합쳐져서 하나의 튜플을 구별할 수 있는 후보키가 될 수 있다고 할 때 우리는 이것을 복합키라고 부른다.
* 이전 스터디에서 (학번, 이름) 형태로 튜플로 묶어야 릴레이션에서 여러 칼럼을 독립적으로 구별할 수 있던 형태가 바로 복합키이다.
* 아래와 같이 주문 릴레이션에서 단일 속성으로는 튜플을 유일하게 식별하는 것이 불가능하므로 2개의 속성을 합한(고객번호, 도서번호)가 후보키가 되며 이렇게 2개 이상의 속성으로 이루어진 키를 복합키(Composite Key)이다. [출처](https://mangkyu.tistory.com/21)

![](https://i.imgur.com/xKh7X8n.png)

* 복합키의 속성들 중에서 먼저 정의한 속성에 대해서 클러스터드 인덱스[참고](https://gocoder.tistory.com/1826)가 정의되어지며, 주로 조회가 빈번하게 일어나는 속성을 먼저 정의하는 것이 좋다고 한다.
* JPA에서는 ```@IdClasss```, ```@EmbeddedID```를 이용하여 복합키를 매핑할 수 있다. [출처](https://techblog.woowahan.com/2595/)

```java
// PayShop.java

@Getter
@Entity
@NoArgsConstructor
public class PayShop {

    @EmbeddedId
    private PayShopId id;

    private String shopName;

    @MapsId(value = "payId")
    @ManyToOne(fetch = FetchType.LAZY)
    private Pay pay;

    public PayShop(PayShopId id,
                   String shopName) {
        this.id = id;
        this.shopName = shopName;
    }

    public void setPay(Pay pay) {
        if (pay != null) {
            pay.getPayShops().remove(this);
        }
        this.pay = pay;
        this.pay.getPayShops().add(this);
    }
}
// @MapsId 를 통해서 Pay에 있는 id 값을 자동으로 할동되게 할 수 있음.
```
```java
//PayShopId.java
@Getter
@EqualsAndHashCode(onlyExplicitlyIncluded = true)
@Embeddable
@NoArgsConstructor
public class PayShopId implements Serializable {

    /**
     * 테이블에서 정의하는 컬럼 사이즈
     */
    public static final int SHOP_NUMBER_SIZE = 12;

    @EqualsAndHashCode.Include
    private PayDetailId payDetailId;

    @EqualsAndHashCode.Include
    @Column
    private String shopNumber;

    public PayShopId(PayDetailId payDetailId,
                     String shopNumber) {
        Preconditions.checkArgument(shopNumber.length() <= SHOP_NUMBER_SIZE);

        this.payDetailId = payDetailId;
        this.shopNumber = shopNumber;
    }
}
// 위 예제와 같이 shopNubmer 라는 값의 컬럼사이즈를 초과해서 입력받아지지 않도록 체크를 할 수 있다.
```

## Surrogate Key vs Natural Key
### Surrogate Key 대체 키
* 테이블을 이루는 컬럼들 가운데 유일하게 식별하기에 적합한 단일 후보키가 존재하지 않을 때, 임의의 식별번호로 이루어진 후보키를 추가할 수 있는데 이를 대체키라고 하다. [출처](https://ssayebee.github.io/wiki/surrogate_vs_natural_key.html)
    * Surrogate key의 가장 큰 단점은 DB query가 복잡해진다는 점을 들수 있다. Business적으로 의미가 없는 key를 사용하기 때문에 간단한 query라도 join이 들어가게 되어서 복잡해지게 된다.
* 예를 들면 다음과 같다. [출처](https://rampart81.github.io/post/surrogate_key_vs_natural_key/)
```
CREATE TABLE stock (
    id INT NOT NULL AUTO_INCREMENT,
    ticker VARCHAR(20) NOT NULL,
    price DECIMAL(35, 4),
    PRIMARY KEY (ID)
);
```
* id가 바로 surrogate key의 전형적인 예 이다. 여기서 id는 단순한 정수 값으로서 주식과는 아무 관련이 없는 정보지만 stock 테이블의 각 row를 대표하기 위한 id로 쓰인다.

### Natural Key
* 테이블을 이루는 컬럼들 가운데 의미를 담고 있는 후보키
    * 아무리 고정 값의 필드를 자연키로 사용한다고 해도 대부분의 자연키는 언젠가 변할 수 있다.
    * 또한, 만약 자연키가 문자열 이라면, 숫자 보다 비교를 하는데에 시간이 더 걸린다.
* 예를 들면 다음과 같다.
```
CREATE TABLE stock (
    ticker VARCHAR(20) NOT NULL,
    price DECIMAL(35, 4),
    PRIMARY KEY (ticker)
);
```
* id 필드를 없애고 대신에 ticker 필드를 primary key로 지정하였다. Surrogate key가 아니라 natural key를 primary key로 사용한 것이다.
    * 위에서 설명한 첫 번째 단점이 여기서 부각된다.
    * 주식 ticker도 값이 변할 수 있다. 예를 들어 LUCID 전기차 회사는 CCIV에서 LCID로 티커를 변경했다.
    * 만일 ticker 값이 변하게 되면 그에 따른 관리를 해주는것이 굉장히 복잡하고 어려워질수 있다.
    * 예를 들어, stock_info 와 portfolio 라는 table들이 stock table의 ticker 필드에 foreign key가 걸려 있다고 가정해보자.
    * ticker 필드 값이 변하지 않으면 아무 문제 없지만 만일 ticker 값이 변해버리면 해당 ticker에 foreign key가 걸려있는 다른 table들의 row들도 전부 동시에 변경 해주어야 한다.
    * 큰 규모의 실제 시스템에서는 DB 구조가 이보다 훨씬 복잡한 경우가 많으므로 모든 foreign key를 업데이트 시키는 일은 굉장히 어려워질수 있다.
    * 이와 반면에 surrogate key는 변경되지 않는다. 처음부터 busienss 적인 의미가 없기 때문에 예상치 못하게 변경될 이유가 없는 것이다.

### M:M 관계의 테이블이 있을 때, 대체 키를 이용할 것인가? 자연 키를 이용할 것인가?
* JPA 환경에서 대부분의 테이블을 별 생각없이 ```@GeneratedValue(strategy = GenerationType.IDENTITY)``` 해당 어노테이션을 사용해서 대체키를 이용해서 테이블을 만들었던 기억이 다들 있을 것이다. 꼭 그래야 하는가?
* Single Surrogate
    * 모든 자식 테이블은 기본 키를 참조하기 위해서 단일 열만 필요로 한다.
    * 외래 키로 모든 자식 테이블을 업데이트 할 필요 없이 테이블의 자연 키를 쉽게 업데이트 할 수 있다.
    * 자연키 보다 더 많은 인덱스를 생성한다.
* Natural Composite key
    * 인덱스를 덜 생성한다.
    * 불필요한 열이 없어진다.
    * 시퀀스 생성기가 필요 없기 떄문에 레코드 삽입이 빠르다.
    * 복합키 중 하나를 업데이트 하면 모든 하위 테이블도 업데이트 해야한다.
* 요약
    * 복합키 VS 자연키 사용에 대한 질문은 데이터베이스 설계에 있어서 자주 다뤄지는 고전적인 주제라고 한다.
    * 상황에 따라 어떤 것을 사용하는게 나을지 고민이 필요하다.
    * Stack Overflow 에서는 대체키 사용을 추천했다.

## 기타 참고 사이트
* https://rampart81.github.io/post/surrogate_key_vs_natural_key/
* https://ssayebee.github.io/wiki/surrogate_vs_natural_key.html
