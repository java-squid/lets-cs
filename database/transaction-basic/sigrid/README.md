# 데이터베이스를 지탱하는 Transaction
## Transaction
* 하나의 작업을 수행하기 위해 필요한 연산들의 집합
    * 이를 ```논리적인 작업의 단위``` 라고 부른다.
    * 트랜잭션 = 하나의 작업을 수행하기 위해 사용되는 SQL 문들의 집합
    * 트랜잭션의 모든 작업이 수행되거나(Commit), 또는 하나도 수행되지 않아야 (Rollback) 데이터베이스에 일관성을 유지할 수 있음

### ACID
#### Atomicity 원자성
* 트랜잭션을 구성하는 연산이 모두 정상적으로 실행되거나, 또는 하나도 실행되지 않아야 한다는 all-or-nothing 방식을 의미한다.
* 트랜잭션을 수행하다가 장애가 발생하여 작업을 완료하지 못한다면?
    * 지금까지 실행한 연산들을 모두 취소한다.
    * DB를 트랜잭션 작업 이전의 상태로 되돌려 atomicity를 보장한다.

### Consistency 일관성
* 트랜잭션이 성공적으로 수행된 이후에도 데이터베이스가 일관되게 유지되어야 한다. 즉, 트랜잭션 전에도 일관되었다면 이후에도 일관되어야 한다.
* 예를 들어, 계좌 이체를 하기 전의 돈 거래의 합과 이후의 돈 거래의 합은 각기 같아야 한다.

### Isolation 고립성
* 현재 진행 중인 트랜잭션이 완료될 때까지, 트랜잭션의 중간 연산 과정에 다른 트랜잭션이 개입하지 못한다. 하나의 트랜잭션 연산이 **완료** 되어야 다음 트랜잭션으로 넘어갈 수 있다.
* 예를 들어, 계좌 이체 500원을 보낼 때 갑자기 300원이 결제되는 과정이 동시다발적으로 이루어질 수 없다. 500원을 보내는 트랜잭션이 먼저 발생되었으니 이게 끝나야 300원이 결제될 수 있다.

### Durability 지속성
* 트랜잭션이 성공적으로 완료된 이후에 데이터베이스에 저장되는 결과는 영구해야 한다. 시스템에 장애가 생기더라도 트랜잭션 작업 결과는 손실되지 않아야 한다.

### Commit
* DB에 반영되는 순간은 트랜잭션 연산이 완료되는 순간이다.

![](https://i.imgur.com/AbGykdB.png)
* commit 연산이 수행되어야 트랜잭션의 수행이 완료되고 데이터베이스에 영구히 기록된다.

### Rollback
![](https://i.imgur.com/uem4fFM.png)
* 트랜잭션이 수행을 실패했음을 알려주는 선언
* begin rollback하면 previous point of consistency로 돌아간다.
* 서로 모순되지 않도록 transaction 시작 이전의 DB로 돌아가 일관된 상태를 유지한다.

### Cycle
![](https://i.imgur.com/JuezNpn.png)
* https://beginnersbook.com/2018/12/dbms-transaction-states/
* 활동 상태
* 부분 완료 상태
* 완료 상태
* 실패 상태
* 철회 상태

### How to recover?
#### Immediate Updates 즉시 회복 기법
* 트랜잭션 수행 도중 변경하면 변경 정보를 로그 파일에 저장하고, 트랜잭션이 부분 완료되기 전이라도 모든 변경 내용을 즉시 데이터베이스의 반영하는 기법.
* 로그 파일을 참조하여 REDO & UNDO 연산 모두 실행
    * Dump: copy the ‘whole database’ to other place regularly
    * Log: save ‘before/after states’ of operations to files
    * REDO 연산
        *  가장 최근에 저장한 데이터베이스 복사본을 가져온 후 로그를 이용해 복사본이 만들어진 이후에 실행된 모든 변경 연산을 재실행하여 장애가 발생하기 직전의 상태로 복구
    * UNDO 연산
        * 로그를 이용해 지금까지 실행된 모든 변경 연산을 취소하여 데이터베이스를 원래의 상태로 복구

#### Deferred Updates 지연 기법
* 트랜잭션이 부분 완료 상태에 이르기까지 발생한 모든 변경 내용을 로그 파일에만 저장하고 데이터베이스에는 커밋이 발생할 때까지 저장하는 기법.
* 회복 과정에서 UNDO가 필요 없음.
* 데이터베이스의 원자성 보장

#### Checkpoint Recovery
#### Checkpoint
* 특정한 체크포인트로 설정한 지점을 기점으로, 해당 지점 이전에는 트랜잭션이 성공적으로 수행되어 디스크에 저장되어 있다고 보장할 수 있습니다.
* 실패가 일어날 경우, redo는 check point 시점부터 log를 따라가면서 트랜잭션을 수행합니다. undo는 트랜잭션을 거꾸로 재수행하면서 트랜잭션을 없던 일로 만듭니다

![image](https://user-images.githubusercontent.com/41055141/125605863-8ff54452-4279-44f7-a5c0-bcef1f3ff020.png)
* Transaction T1
checkpoint 이전에 실행이 완료되었으므로 failure 되더라도 이미 disk에 저장이 된 상태이므로 회복을 하지 않습니다.
* Transaction T2
checkpoint 이전에 실행된 내용은 disk에 반영이 되었으므로 check point 이후의 내용을 log를 따라가는 redo를 수행합니다.
* Transaction T3
failure 시점에 실행중이였으므로 check point 시점으로 undo한 후, 트랜잭션을 재실행합니다.
* Transaction T4
checkpoint 이후에 실행됐고 failure 시점 전에 실행이 끝났으므로 redo를 수행합니다.
* Transaction T5
checkpoint 이후에 실행됐고 failure 시점에 실행중이였으므로 시작지점까지 undo한 후, 트랜잭션을 재실행합니다.

그러니까 정리하자면,
* 체크포인트 시점 중간에 (스냅샷 찍는 도중에) 실행되는 트랜잭션은 모두 undo한다.
* 체크포인트 시점 이후에 실행된 트랜잭션은 모두 undo한다.
* undo list에 기록된 트랜잭션이 성공적으로 undo되면 이를 다시 redo한다.
