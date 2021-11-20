# 세마포어와 뮤텍스
## Process Synchronization vs Thread Synchronization
* 현대 운영체제는 Thread Synchronization 위주로 동작한다.
* Process 1의 Thread A가 실행되고, Process 1의 Thread B가 실행되고 이후 Process 2의 Thread C가 실행되는 형태로 진행된다. 즉, Context Switching의 기준이 현대 OS에는 Thread인 것이다. 각 프로세스 내의 Thread 1, Thread 2… 동일 프로세스 내에서 서로 다른 쓰레드들이 번갈아가며 처리된다.
* Independent Process란 Process 1, Process 2 가 아무런 관계가 없는 독립적인 프로세서인 경우를 의미한다. 이와 달리, Cooperating Processor는 Process 1, Process 2가 서로 영향을 주고 받는 경우를 의미한다. 예를 들면, 프로세스 간 자원공유가 필요한 경우(DB 등)을 꼽아볼 수 있다.
* Cooperating Processor의 경우, 쓰레드 간의 공유자원을 사용하므로 동기화 작업이 필요하다. 공유 자원에 대한 동시 접근(concurrent access)는 데이터 일관성을 깨뜨리므로, cooperating process 간의 순서있는 실행(orderly execution)을 통해 데이터 일관성을 유지해야 한다.
## Example: Bank Account Problem
```
// Test.java
class Test {
	public static void main(String[] args) throws InterruptedException {
		BankAccount b = new BankAccount();
		Parent p = new Parent(b);
		Child c = new Child(b);
		p.start();   // start(): 쓰레드를 실행하는 메서드
		c.start();
		p.join();    // join(): 쓰레드가 끝나기를 기다리는 메서드
		c.join();
		System.out.println("balance = " + b.getBalance());
	}
}

// 계좌
class BankAccount {
	int balance;
	void deposit(int amount) {
		int temp = balance + amount; // 임계구역 코드
		System.out.print("+"); // 시간 지연 의도를 위한 코드
		balance = temp;
	}
	void withdraw(int amount) {
		int temp = balance - amount; // 임계구역 코드
		System.out.print("-"); // 시간 지연 의도를 위한 코드
		balance = temp;
	}
	int getBalance() {
		return balance;
	}
}

// 입금 프로세스
class Parent extends Thread {
	BankAccount b;
	Parent(BankAccount b) {
		this.b = b;
	}
	public void run() {   // run(): 쓰레드가 실제로 동작하는 부분(치환)
		for (int i = 0; i < 100; i++)
		  b.deposit(1000);
	}
}

// 출금 프로세스
class Child extends Thread {
	BankAccount b;
	Child(BankAccount b) {
		this.b = b;
	}
	public void run() {
		for (int i = 0; i < 100; i++)
		  b.withdraw(1000);
	}
}
```
* 위 코드에 대한 출력값은 다음과 같다. 만약 시간 지연을 위한 코드(System.out.print 함수 호출)이 없었다면, balance 값은 0이 되었을 것이다.
```
++++++++++++++++++++++++++++++++++----------------------------------------------
--------------------------------------------------------------------------++++++
+++----------------------------------------------+++++++++++++++++++++++++++++++
+----+++++++-+++++----+++-------------------------------------------------------
-+++++++-++++-+++++++++-------++++++++++++++++++++++++++++++++++++++++++++++++++
++++++---------------+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+-++++++++++++-------------------++++++++++++++++++++-++++++++++++++++++++++++++
++++++-+------------------------------------------------------------------------
-+++++++++++-+++++++----------------------------------------+-------+-----------
-+------+-----------------------------------------------------------------------
-+------------------------------------------------------------------------------
-+------------------------------------------------------------------------------
-------------------+-------+----------------------------------------------------
------------------------------+-------------------------------------------------
------------------------------------------------------+-------------------------
-+------------------------------------------------------------------------------
-++---------------------------------------++++++++++++++++++++++++++++++++++++++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

balance = 1000000
```
* 간단한 실험을 통해, 우리는 시간 지연이 주어질 때 여러 쓰레드가 하나의 공유 자원을 사용하는 프로그램은 망가진다는 사실을 알 수 있다. 출력되는 결과는 운영체제에서 쓰레드를 스위칭하는 패턴이 매번 다르므로 수행할 때마다 다르다. 이는 공통변수, 즉 계좌의 잔액(common variable)에 대한 동시 업데이트(concurrent update) 가 이루어지기 때문이다. Java와 같이 High-level 언어에서는 한 줄로 족하지만, assembly code로 번역될 때 여러 줄로 바뀌므로 중간에 스위칭이 발생할 수 있다.
* 이를 해결하기 위해서는, 공통변수에 해당하는 임계구역(critical section)을 설정해 임계구역 코드에 접근하는 쓰레드가 단 하나만 존재하도록 해야 한다.
```
// 해결 코드
class Test {
 static final int MAX = 100; // 입출금 회수
 public static void main(String[] args) throws InterruptedException {
	// 은행계좌를 만들고
	BankAccount b =
	new BankAccount();
	// 부모 쓰레드와
	Parent p = new Parent(b, MAX);
	// 자식 쓰레드를 만든 후
	Child c = new Child(b, MAX);
	// 각각 실행시킨다.
	p.start();
	c.start();
	p.join();// 부모와 자식 쓰레드가
	c.join();// 각각 종료하기를 기다린다.
	System.out.println("Final balance = "
	+ b.getBalance());// 최종 잔액 출력
 }
}

class BankAccount {
	int balance;
	void deposit(int amount) {
		balance = balance + amount;
	}
	void withdraw(int amount) {
		balance = balance - amount;
	}
	int getBalance() {
		return balance;
	}
}

class Parent extends Thread {
	BankAccount b;
	int count;
	Parent(BankAccount b, int count) {
		this.b = b;
		this.count = count;
}
	public void run() {
		for (int i=0; i<count; i++)
			b.deposit(1);
	}
}

class Child extends Thread {
	BankAccount b;
	int count;
	Child(BankAccount b, int count) {
		this.b = b;
		this.count = count;
}
public void run() {
	for (int i=0; i<count; i++)
		b.withdraw(1);
	}
}
```
* 임계구역 문제를 해결하기 위해서는 다음의 해결책을 꼽아볼 수 있다. 이를 통하여 프로세스의 실행 순서를 제어하는 것이 목적이다.
* Mutual exclusion(상호배타): 오직 한 쓰레드만이 진입 가능하다. 한 쓰레드가 임계구역에서 수행 중인 상태에서는 다른 쓰레드는 절대 이 구역에 접근할 수 없다.
* Progress(진행): 한 임계구역에 접근하는 쓰레드를 결정하는 것은 유한 시간 이내에 이루어져야한다.
* Bounded waiting(유한대기): 임계구역으로 진입하기 위해 대기하는 모든 쓰레드는 유한 시간 이내에 해당 임계구역으로 진입할 수 있어야 한다.
* Semaphore
    * 세마포어에는 P와 V가 있다. P : 임계 구역 들어가기 전에 수행 (프로세스 진입 여부를 자원의 개수(S)를 통해 결정), V : 임계 구역에서 나올 때 수행 (자원 반납 알림, 대기 중인 프로세스를 깨우는 신호)
```
class Semaphore {
  int value;      // number of permits
  Semaphore(int value) {
    // ...
  }
  void acquire() { // P
    value--;
    if (value < 0) {
      // add this process/thread to list
      // block
    }
  }
  void release() { // V
    value++;
    if (value <= 0) {
      // remove a process P from list
      // wakeup P
    }
  }
}
procedure P(S)   --> 최초 S값은 1임
    while S=0 do wait  --> S가 0면 1이 될때까지 기다려야 함
    S := S-1   --> S를 0로 만들어 다른 프로세스가 들어 오지 못하도록 함
end P

--- 임계 구역 ---

procedure V(S) --> 현재상태는 S가 0임
    S := S+1   --> S를 1로 원위치시켜 해제하는 과정
end V
```
* 위 코드에서 acquire 함수는 P에 해당하는 것으로서, 쓰레드가 임계구역에 진입하는 경우 value 값을 감소시키는 역할을 한다. 만약 value 값이 0보다 작으면 해당 임계구역에 어느 쓰레드가 존재한다는 뜻이므로, 새로운 쓰레드가 접근하지 못하도록 막아야 한다.
* If the semaphore value is negative, its magnitude is the number of processes waiting on that semaphore. 즉, value의 절댓값 크기만큼 현재 프로세스/쓰레드들이 임계구역 접근 번호표를 뽑은 셈이 된다. 이와 달리, value의 초기값은 1이므로 1일 때는 프로세스가 직접 접근 가능하다는 뜻이며 0이면 현재 누군가가 쓰고 있으니 잠깐만 대기하라는 뜻이다.
* 대기열은 list(Queue)로 구현하되, 원소 추가 이후 block을 걸어준다.
* 위 코드에서 release 함수는 V에 해당하는 것으로서, 쓰레드가 임계구역에 빠져나가는 경우 value 값을 증가시키는 역할을 한다. 만약 value 값이 0보다 크면 임계구역에 진입하려고 대기하는 프로세스가 list(Queue)에 남아있다는 의미이므로 순서대로 상위 프로세스를 임계구역에 접근하도록 해 주어야 한다.
### 예시: 최초 S 값은 1이고, 현재 해당 구역을 수행할 프로세스 A, B가 있다고 가정하자.
* 먼저 도착한 A가 P(S)를 실행하여 S를 0으로 만들고 임계구역에 들어감
* 그 뒤에 도착한 B가 P(S)를 실행하지만 S가 0이므로 대기 상태
* A가 임계구역 수행을 마치고 V(S)를 실행하면 S는 다시 1이 됨
* B는 이제 P(S)에서 while문을 빠져나올 수 있고, 임계구역으로 들어가 수행함
* Bank Account Problem을 Semaphore를 활용하여 해결하면 다음과 같다. 쓰레드 동기화를 통하여 임계구역 문제를 해결하였고, 이에 따라 +- 출력을 제외하고 balance 값이 0 출력된다.
```
import java.util.concurrent.Semaphore;
class BankAccount {
	int balance;
	Semaphore sem;
	BankAccount() {
		sem = new Semaphore(1);// 초기값 = 1
	}
	void deposit(int amount) { // 입금
		try {
			sem.acquire(); // 진입 전: acquire()
		} catch (InterruptedException e) {}
		int temp = balance + amount;
		System.out.print("+");
		balance = temp;
		sem.release(); // 나온 후: release()
	}
	void withdraw(int amount) { // 출금
		try {
		sem.acquire(); // 진입 전: acquire()
		} catch (InterruptedException e) {}
		int temp = balance - amount;
	System.out.print("-");
	balance = temp;
	sem.release(); // 나온 후: release()
}
int getBalance() {
	return balance;
}
}
```
### Ordering
* 세마포어를 사용하는 이유 중 하나는 ordering하기 위함인데, 이는 프로세스의 실행 순서를 원하는 대로 설정하는 것에 있다.
* 예시: 프로세스가 Process 1, Process 2 두 개가 있다고 가정하자. 원하는 순서는 Process 1, Process 2 순으로 실행하기를 원한다. 그러면 아래와 같이 설정해줄 수 있다.

https://velog.io/@codemcd/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9COS-8.-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EB%8F%99%EA%B8%B0%ED%99%94-1

* 위의 사진은, Process 1 > Process 2 순서로 프로세스 실행됨이 보장된다.
* P1이 먼저 실행된 경우는 다음과 같다.
    * Section 1 이전에 아무런 동작이 없으므로 바로 수행한다.
    * sem.release() 를 만나면 value값을 1 증가시키고, 세마포 큐에 있는 프로세스를 깨워주는데 현재에는 큐에 프로세스가 없으므로 아무 동작도 하지 않는다.
    * P2가 실행된다.
    * P2의 sem.acquire() 를 만나면 현재 value값은 1이고 이를 1감소시키면 0이 된다. value = 0이면 block을 하지 않으므로, 무사히 Section 2가 수행된다.
* P2가 먼저 실행된 경우는 다음과 같다.
    * Section 2 이전에 sem.acquire() 가 있으므로 이를 수행하는데, 현재 value값은 0이고 이를 1 감소 시키면 -1 이 된다. value값이 음수면 해당 프로세스를 block 시킨다. 즉, 세마포 큐에 삽입한다.
    * P1이 실행되면 Context Switch으로 Section 1이 바로 수행된다.
    * sem.release() 를 만나면 value값을 1 증가시키고, 세마포 큐에 있는 P2 프로세스를 깨워준다.(현재 value = 0)
    * P2의 Section 2가 수행된다.
* Bank Account Problem을 Ordering으로 해결해보면 다음과 같다.
    * 입금(Parent) 또는 출금(Child)가 먼저 실행되는 경우: 상호배타를 위한 sem 세마포어 및 실행순서 조정을 위한 sem2 세마포어 할당.
    * 프로그램 시작 시, 부모 쓰레드는 그대로 실행하도록 하고 자식 쓰레드가 실행되는 경우 sem2 세마포어에 의해 일단 block되고 이후 부모 쓰레드를 실행하여 자식 쓰레드가 이후에 블록된 상태에서 벗어나도록 설정. 참고: https://zzsza.github.io/development/2018/07/30/process-synchronization/
    * 구체적으로, 초기값이 0인 sem2 세마포어에 대해 acquire()를 호출하게 하도록 deposit(), withdraw() 메소드를 각각 수정
```
class BankAccount {
	int balance;

	Semaphore sem, sem2;
	BankAccount() {
		sem = new Semaphore(1);
		sem2 = new Semaphore(0);   // Ordeing을 위한 세마포
	}

	void deposit(int amount) {
		try {
			sem.acquire();
		} catch (InterruptedException e) {}
		int temp = balance + amount;
		System.out.print("+");
		balance = temp;
		sem.release();
		sem2.release();   // block된 출금 프로세스가 있다면 깨워준다.
	}
	void withdraw(int amount) {
		try {
			sem2.acquire();   // 출금을 먼저하려고 하면 block한다.
			sem.acquire();
		} catch (InterruptedException e) {}
		int temp = balance - amount;
		System.out.print("-");
		balance = temp;
		sem.release();
	}
	int getBalance() {
		return balance;
	}
}
```
* 입출금이 교대로 이루어지는 경우(Child-Parent-Child-Parent…): 블록된 부모 쓰레드는 자식 쓰레드가 깨워주고, 블록된 자식 쓰레드는 부모 쓰레드가 각각 깨워주도록 한다.
    * 상호배타를 위한 sem 세마포어 외에 부모 쓰레드의 블록을 위해 dsem 세마포어를, 자식 쓰레드의 블록을 위해 wsem 세마포어를 각각 사용한다.
    * 잔액이 항상 0 이상인 경우: 출금하려는 액수보다 잔액이 작으면 자식 쓰레드가 블록되도록 하며 이후 부모 쓰레드가 깨워주게 한다.
    * 상호배타를 위한 sem 세마포어 외에 sem2 세마포어를 사용하여 잔액 부족시 자식 쓰레드가 블록되도록 한다.
