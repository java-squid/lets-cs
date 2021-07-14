# B tree, B+tree 란?
- 데이터 베이스의 인덱스를 구성하는 자료구조를 말함.
- 왜 공부해야할까?
  - 어떻게 구성되어있는 지 이해하면, 인덱스 생성 및 사용에 이해도가 좀 더 높아지지 않을까.

# B tree
- 핵심은 데이터가 **정렬** 된 상태로 트리 형태로 저장하고 있다는 점.
- Binary Search Tree랑 헷갈릴 수도 있음.
   - B tree의 경우, Binary Search Tree와는 다르게, 자식 노드를 2개이 상 갖는 것이 가능.
   - 그래서 B tree는 Binary Search Tree에서 비롯된 타입이라 볼 수 있음.
- 여러개의 key, value 값을 하나의 노드에 가지고 있고, key 를 중심으로 오름차순 정렬되어있음.
  - 데이터베이스에서 key는 PK, 혹은 indexed column of row를 의미하고,
  - value는 실제 record 값일 수도 혹은 참조하고 있는 값일 수도 있음.
  - 노드란?
    ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FqycZ2%2FbtqBQnr4QYG%2F7J8KpnmNaJiTjgS0K9TEIK%2Fimg.png)
    - 위 그림에서 하나하나를 모두 노드라고 지칭
    - 다만 단계별로 Root, Brach, Leaf로 나뉘어서 부른다. 

## 페이지 란? 
> .. the smallest unit of data that is exchanged/stored.

- 데이터베이스에 저장되는 가장 작은 단위나 데이터를 일컫는 말인듯.
- InnoDB 에서 기본적으로 페이지 사이즈는 **16KB**
- 참고
  - https://stackoverflow.com/questions/4401910/mysql-what-is-a-page 

## 노드에 대해서 좀 더 알아보자
- B tree를 만족하기 위한 노드 조건
 ```
the nodes in a B-tree of order m can have a maximum of m children
each internal node (non-leaf and non-root) can have at least (m/2) children (rounded up)
the root should have at least two children – unless it’s a leaf
a non-leaf node with k children should have k-1 keys
all leaves must appear on the same level
```
- 최대 M개 자식을 가질수 있는 B tree를 M차 B tree라고 함.

- 노드들은 항상 그들의 자식노드들을 참고 하고 있으며, 이러한 참조는 해당 page ID임.
  - pointer의 느낌
- 또한 노드안에 있는 데이터는 반드시 **정렬** 된 상태

## 어떻게 B tree가 만들어질까?
![](https://www.baeldung.com/wp-content/uploads/sites/4/2020/05/btree1-1.png)

1. root 노드를 생성한다.
    - 해당 노드는 key/value pair로 구성되며, key = 1, value는 *로 묘사한다.
    - *의 의미는, 다른 레코드를 참조함을 의미한다. 
    - root 노드는 왼쪽, 오른쪽 서브트리로 갈 수 있는 pointer를 가지고 있다. 

![](https://www.baeldung.com/wp-content/uploads/sites/4/2020/05/btree2-1.png)

2. 2라는 key를 가진 노드를 추가한다.
   - 현재 노드들은 key값을 기준으로 오름차순 정렬되어 있다.

![](https://www.baeldung.com/wp-content/uploads/sites/4/2020/05/btree3-1.png)

3. 3이라는 key를 가진 노드를 추가한다.
    - 이순간 b tree는 split operation 이라고 불리는 행위를 실행한다.
    - 이 과정을 통해, left, middle , right node가 결정된다.
    - 처음 root 노드는 1 키를 가지고 있는 노드 였지만, 2 키를 가지고 있는 노드로 바뀌었다.

![](https://www.baeldung.com/wp-content/uploads/sites/4/2020/05/btree4-1-768x300-1.png)

4. 4라는 키를 가진 노드를 삽입한다.
   - 이상태에서 B tree는 구조가 잡혀있기 때문에.. (3개이상의 노드가 삽입되었기 때문에)
   - 해당 키 값이 root노드보다 큰지 작은지 판단할 수 있다. (현재는 크다.)
   - right sub tree에 추가되고, right에 있는 노드들의 키값을 기준으로 오름차순 정렬된다.

![](https://www.baeldung.com/wp-content/uploads/sites/4/2020/05/btree5-1-768x288-1.png)

5. 5라는 키를 가진 노드를 삽입한다.
  - root 노드 + 1 갯수를 초과하여, child 노드가 구성되어질 수 없기 때문에..
  - 리밸랜생이 일어난다.

![](https://www.baeldung.com/wp-content/uploads/sites/4/2020/05/btree6-2.png)

6. 6 노드를 삽입한다.

![](https://www.baeldung.com/wp-content/uploads/sites/4/2020/05/btree7-1-1024x471-1.png)

7. 7 노드 삽입
   - 하는 순간 조건을 1번조건 만족시키기 위해 리밸랜싱 일어남 (현재 M=2, 하나의 노드는 최대 2개까지 자식노드를 가질 수 있음.)
   -  오른쪽 서브 트리에서 하나를 상위로 올리게 되면.... (a non-leaf node with k children should have k-1 keys) 이 조건을 어기게됨.
   - 그래서 우선 오른쪽 서브 트리에서.. 리밸랜싱 일어남.
   - all leaves must appear on the same level를 만족시키기 위해 나머지 왼쪽 서브트리에서도 리밸랜싱..
   
![](https://www.baeldung.com/wp-content/uploads/sites/4/2020/05/btreefull-3.png)

8. 8, 9 노드 삽입
    - 8 노드를 삽입하면, 7 오른쪽에 붙었다가.. (each internal node (non-leaf and non-root) can have at least (m/2) children (rounded up)), 에 의거해 최대 2개까지 자식 노드를 가질 수 있으니.. 리밸랜싱

- insert, delete 과정 참고
  - https://cieske.tistory.com/60
  - https://www.cs.usfca.edu/~galles/visualization/BTree.html


# B+Tree
- B tree와 비교되는 가장 큰 차이점
  1. 모든 leaf nodes 는 서로 연결되어 있음 (doubly linked list)
  2. internal node는 라우터처럼 leaf node 로 가는 중간다리 역할만 할 뿐이고, 실제 데이터가 저정되어 있는 page id 값은 leaf node 에만 저장이 된다.
- internal node에 더 많은 key를 저장할 수 있다 (왜나면 키 외 다른 데이터는 저장하지 않으니까..)
  - 이는 B+tree의 height가 Btree보다 낮음을 의미한다.

![](https://www.baeldung.com/wp-content/uploads/sites/4/2020/05/bplustreefull-2.png)
- 2,4 key가 중복됨을 볼 수가 있음. (interanl, leaf)
- 왜냐면 internal node는 satelite data를 가지고 있을 수 없기 때문에
- leaf 노드는 반드시 key/value pair형태를 지니고 있어야함.

## B+tree에서 검색은 어떻게 이루어지는가?
![](https://www.baeldung.com/wp-content/uploads/sites/4/2020/05/bplustreeSearch-1.png)
- Data는 leaf 노드에 저장됨을 알아야함.
- 즉 검색하고자 하는 key를 알고 있으면, root -> internal -> leaf 까지 내려가서 데이터를 찾을 수 있다는 뜻
  - 반면 btree는 root, interanl node에서도 데이터를 가지고 있다.
- B+trees는 range query 를 지원한다.
   - 참고
   - https://cs.stackexchange.com/questions/90961/how-a-range-query-works-on-a-simple-btree

# 참고
- https://www.baeldung.com/cs/b-trees-vs-btrees
- https://zorba91.tistory.com/293
- https://hyungjoon6876.github.io/jlog/2018/07/20/btree.html
- https://ko.wikipedia.org/wiki/B_%ED%8A%B8%EB%A6%AC
- https://www.geeksforgeeks.org/introduction-of-b-tree-2/
- https://velog.io/@emplam27/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-B-Tree
