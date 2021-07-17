# B-Tree
* 이진 트리가 자식 노드가 최대 2개인 노드를 말하는 것이다.
* B-Tree는 자식 노드의 개수가 2개 이상인 트리를 말한다.
    * 노드 내의 데이터가 1개 이상이다.
    * 노드 내 최대 데이터 수가 2개라면 2차 B-Tree, 3개라면 3차 B-Tree 라고 말한다.

### B-Tree 성립 조건
* 노드의 데이터 수가 n개라면 자식 노드의 개수는 n+1개이다.
![](https://i.imgur.com/8HViFwf.png)
    * root 노드에 데이터가 3개이므로 자식 노드의 개수는 4개이다.
* 노드의 데이터는 정렬된 상태이다.
![](https://i.imgur.com/qmbr7kf.png)
* 노드 데이터를 기준으로, 자식 노드의 데이터가 큰 값은 오른 쪽에, 작은 값은 왼쪽에 배치한다.
* Root 노드에 자식이 있다면, 자식은 2개 이상이다.
* Root 노드를 제외한 모든 노드는 적어도 M/2 개의 데이터를 갖고 있어야 한다. 즉, 4차부터는 2개 이상의 데이터를 갖고 있어야 한다.
    ![](https://i.imgur.com/SrILCLR.png)
* Leaf 노드는 모두 같은 레벨에 존재해야 한다.
    ![](https://i.imgur.com/QS8Rb4Z.png)


### 탐색 방법
* 이진 트리와 동일하다.
* root 노드에서 시작해서 하향식으로 전개해나간다.

### 삽입 방법
![](https://i.imgur.com/s7CKiYC.png)
- 3차 B-Tree를 기준으로 한다.
- 가. 데이터를 탐색해 해당하는 Leaf 노드에 데이터를 삽입한다.
- 나. Leaf 노드가 꽉 찼으면 노드를 분리한다.
    - 위의 예시에서 3차 B-Tree 이므로 Insert 7에서 꽉 찼다.
    - 중간값을 부모 노드로 해서 분리한다.
- 다. 분리한 서브트리가 B-Tree조건에 맞지 않는다면 부모 노드로 올라가며 merge한다.
    - 위의 insert12 에서 [9, 7, 12] 를 서브트리로 분리 하였으나 B-Tree 조건에 맞지 않는다.
    - 다시 merge 하니 Leaf 노드가 모두 같은 레벨에 존재하지 않는다.
    - Root 노드와 merge 함으로서 조건을 만족시켰다.

### 삭제 방법
![](https://i.imgur.com/r0PsDIA.png)
가. Leaf 노드에서 1을 삭제하면 구조가 깨진다.
나. 삭제한 노드의 부모 노드로 올라가며 데이터를 가져온다.
다. 1의 부모 노드와 형제 노드를 merge한다.
라. 부모 노드에서 자식 노드로 값을 가져오고, 자식 노드의 형제 노드와 merge 한다.
마. root 노드까지 올라가며 조건을 충족시킨다.

### 결론: B-Tree는 언제든지 균형된(Balanced) 트리이다.

![](https://i.imgur.com/lifYtSv.png)
* 테이블이 갱신되면 계속 균형이 깨지니까. 갱신 빈도가 높은 테이블에 작성되는 인덱스 같은 경우 인덱스 재구성을 해서 트리의 균형을 되찾는 작업이 필요하다. 


### 참고
* https://hyungjoon6876.github.io/jlog/2018/07/20/btree.html
* https://matice.tistory.com/8

# B+Tree
![](https://i.imgur.com/kaVGSJH.png)
* B+tree의 경우 브랜치 노드에 key만 담아두고, data는 담지 않는다.
    * 오직 리프 노드에만 key와 data를 저장하고, 리프 노드끼리 Linked list로 연결되어 있다.

### B+Tree의 장점
* 리프 노드를 제외하고 데이터를 담아두지 않는다. 따라서 메모리를 더 확보해서 더 많은 key를 수용할 수 있다.
* 하나의 노드에 더 많은 Key를 담을 수 있으므로 같은 데이터에 따라 트리의 높이는 더 낮아진다. (```=== cache hit```)
* 풀 스캔 시, B+Tree는 Leaf Node에 데이터가 모두 있으므로 단 한 번의 선형 탐색만 하면 된다. 따라서 B-Tree에 비해 빠르다.
    * InnoDB에서는 B+Tree를 Double Linked List로 연결했다.
