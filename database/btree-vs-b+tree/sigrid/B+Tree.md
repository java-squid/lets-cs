# B+Tree
![](https://i.imgur.com/kaVGSJH.png)
* B+tree의 경우 브랜치 노드에 key만 담아두고, data는 담지 않는다.
    * 오직 리프 노드에만 key와 data를 저장하고, 리프 노드끼리 Linked list로 연결되어 있다.

### B+Tree의 장점
* 리프 노드를 제외하고 데이터를 담아두지 않는다. 따라서 메모리를 더 확보해서 더 많은 key를 수용할 수 있다.
* 하나의 노드에 더 많은 Key를 담을 수 있으므로 같은 데이터에 따라 트리의 높이는 더 낮아진다. (```=== cache hit```)
* 풀 스캔 시, B+Tree는 Leaf Node에 데이터가 모두 있으므로 단 한 번의 선형 탐색만 하면 된다. 따라서 B-Tree에 비해 빠르다.
    * InnoDB에서는 B+Tree를 Double Linked List로 연결했다.
