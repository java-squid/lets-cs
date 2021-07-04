## B-Tree

### 개요

* 이진 트리는 하나의 부모가 두 개의 자식을 갖는다. 구조가 간결해서 균형이 맞으면 ```OlogN``` 의 복잡도를 갖는다.

* 하지만 균형이 맞지 않으면 선형탐색 급의 검색 효율을 갖는다.

* B-Tree는 이진 트리를 확장한 것으로, 더 많은 자식을 가질 수 있도록 일반화한 것이다.

  * B-Tree는 자식 노드의 개수가 2개 이상인 트리를 말한다.
  * B-Tree는 노드 내의 데이터가 1개 이상일 수 있다.
    * 노드 내 최대 데이터 수가 2개이면 2차 B-Tree라고 부른다.
  * B-Tree는 노드의 데이터 수가 n개라면 자식 노드의 개수는 (n+1) 개이다.

  ![btree조건](https://hyungjoon6876.github.io/jlog/assets/img/20180720/btree_1.png)

  * 노드의 데이터 수가 root에 3개이므로, 자식 노드의 개수는 4개이다.

  * 노드 내부의 데이터는 정렬되어 있어야 한다.

    * ```3 1 4``` 가 아니라 ```1 3 4``` 이어야 한다.

  * 노드의 자식 노드 데이터들은 노드 데이터를 기준으로 데이터보다 작은 값은 왼쪽 서브트리에, 큰 값은 오른쪽 서브트리에 이루어져야 한다. 이는 이진 트리와 동일한 논리이다.

    ![btree조건](https://hyungjoon6876.github.io/jlog/assets/img/20180720/btree_3.png)

  * Root 노드를 제외한 모든 M차 B-Tree의 노드는 적어도 M/2개의 데이터를 갖고 있어야 한다.

    ![btree조건](https://hyungjoon6876.github.io/jlog/assets/img/20180720/btree_4.png)

    * 다음과 같은 4차 B-Tree에서 Root 노드의 데이터 8의 왼쪽 서브트리 노드가 데이터가 1개이므로 조건에 부합하지 않는다.

  * `Leaf` 노드로 가는 경로의 길이는 모두 같아야 한다. 즉 `Leaf` 노드는 모두 같은 레벨에 존재해야 한다.

    ![btree조건](https://hyungjoon6876.github.io/jlog/assets/img/20180720/btree_5.png)

### 참고

* https://hyungjoon6876.github.io/jlog/2018/07/20/btree.html