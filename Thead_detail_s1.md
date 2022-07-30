## 프로세스와 쓰레드
- 운영체제에서 실행 중인 하나의 어플리케이션을 프로세스라고 부른다. 
- 사용자가 어플리케이션을 실행하면 운영체제로 부터 메모리를 할당받아 애플리케이션의 코드를 실행하는데 이것이 프로세스다,
- 하나의 어플리케이션은 다중 프로세스를 만들기도 한다. 예를 들어 Chrome 을 두 개 실행했다면 두개의 프로세스가 생성된다.

## 멀티태스킹과 멀티쓰레드
- 두가지 이상의 작업을 동시에 처리하는 것
- 운영체제는 멀티 태스킹을 할 수 있도록 CPU 및 메모리 자원을 프로세스 마다 적절히 할당해주고, 병렬로 실행시키는 것을 말한다.
- 워드로 문서작업을 하면서 멜론으로 음악을 듣는 것도 멀티태스킹이라 할 수 있다.
> 하지만 멀티 태스킹이 멀티 프로세스를 뜻하지는 않는다.
- 예를 들어 메신져는 채팅기능을 제공하면서 파일 전송 기능을 수행하기도 하는데 이는 하나의 프로세스를 두가지 이상의 쓰레드를 이용하는 것이다.
- 쓰레드는 사전적 의미로 한가닥의 실을 뜻하는 말이며, 순차적으로 실행할 코드를 실처럼 이어놓았다고 해서 유래된 이름이다.
- 하나의 쓰레드는 하나의 실행흐름이기 때문에 한 프로세스 내에 쓰레드가 두개라면 두개의 코드 실행 흐름이 생성된다,
>즉 멀티프로세스가 어플리케이션 단위의 멀티태스킹이라면, 멀티쓰레드는 어플리케이션 내부의 멀티태스킹이라 말할 수 있다.
- 멀티프로세스들은 운영체제에서 할당 받은 자신의 메모리를 가지고 실행하기 때문에 독립적이다. 따라서 하나의 프로세스에서 오류가 발생해도 다른 프로세스에 영향을 미치지 않는다.
- 하지만 하나의 스레드가 예외가 발생하면 다른 스레드에 영향을 미쳐 종료될 수 있다.

## Thread 클래스와 Runnable 인터페이스
- 멀티 스레도르 실행하는 애플리케이션을 개발 하려면 먼저 몇 개의 작업을 병렬로 실핼할지 결정하고 각 작업별로 스레드를 생성해야 한다.

- 어떤 자바 어플리케이션이건 메인 스레드는 반드시 존재하며, 메인 작업외에 추가적인 병렬작업의 수만큼 스레드를 생성하면 된다.

- 작업스레드는 객체로 생성되기 때문에 클래스가 필요한데 Thread 를 직접 객체화 하여 생성해도 되지만, 상속받아 하위 클래스를 만들어 생성할 수 있다.
- Thread 클래스와 Runnable 인터페이스 관계
	- Thread 클래스는 Runnable 인터페이스를 구현한 클래스이다. 
	- 일반적으로 Thread 클래스는 ** Runnable 인터페이스를 생성자 주입** 으로 사용한다. 
- **Thread 클래스와 Runnable 주입을 활용한 직접 생성**
	>BeepTask
	```java
	public class BeepTask implements Runnable{
		public void run(){
			Toolkit toolkit = Toolkit.getDefaultToolkit();
			for(int i=0;i<5;i++){
				toolkit.beep();
				try{
					Thread.sleep(500);
				}catch(Exception e){
					
				}
			}
		}
	}
	```
	>BeepPrintExample2
	```java
	public class BeepPrintExample2{
		public static void main(String[] args){
			Runnable beepTask = new BeepTask();
			Thread thread = new Thread(beepTask);
			thread.start();
			
			for(int i=0;i<5;i++){
				System.out.println("벨");
				try{
					Thread.sleep(500);
				}catch(Exception e){
				
				}
			}
		}
	}	
	```
	>BeepPrintExample - Runnable 익명객체사용
	```java
	Thread thread = new Thread(new Runnable()){
		@Override
		public void run(){
			Toolkit tk = Toolkit.getDefaultToolkit();
			for(int i = 0; i< 5; i++){
				tk.beep();
				try{
					Thread.sleep(500);
				}catch(Exception e){}
			}
		}
	}
	```
	>BeepPrintExample - 람다식
	```java
	Thread thread = new Thread(()->{
		Toolkit tk = Toolkit.getDefaultToolkit();
		for(int i =0; i<5;i++){
			tk.beep();
			try{
				Thread.sleep(500);
			}catch(Exception e){}
		}
	});
	
	```
- Thread를 상속받아 사용하는 방법
	 ```java
	public class WorkerThread extends Thread{
		@Override
		public void run(){
			//쓰레드가 실행할 코드
		}
	} 
	 ```
 	>BeepTask
	```java
	public class BeepTask extends Thread{
		@Override
		public void run(){
			Toolkit toolkit = Toolkit.getDefaultToolkit();
			for(int i=0;i<5;i++){
				toolkit.beep();
				try{
					Thread.sleep(500);
				}catch(Exception e){}
			}
		}
	}
	```
	>BeepPrintExample3
	```java
	public class BeepPrintExample3{
		public static void main(String[] args){
			Thread thread = new BeepTask();
			thread.start();
			
			for(int i=0;i<5;i++){
				System.out.println("벨");
				try{
					Thread.sleep(500);
				}catch(Exception e){
				
				}
			}
		}
	}	
	```
	
## Thread 관련 메소드
```java
thread.setName(); // Thread 이름 설정
thread.getName(); // Thread 이름 가져오기
thread.currentThread(); //코드를 실행하는 쓰레드 객체 얻기 
```

## Thread 우선순위
- 멀티스레드는 동시성 또는 병렬성으로 실행된다.
	- 동시성은 멀티작업을 위해 하나의 코어에 멀티스레드가 번갈아가며 실행하는 성질을 말한다.
	- 병렬성은 멀티작업을 위해 멀티 코어에서 멀티스레드가 번갈아가며 실행하는 성질을 말한다.
- Thread 개수가 코어의 수보다 많은 경우 Thread 는 어떤 순서에 의해 동시성으로 실행할 것인가를 결정해야 하는데, 이것을 스레드 스케쥴링이라한다.
- 자바의 스레드 스케쥴링은 우선순의 방식과 순환할당 방식을 사용한다
	- 우선순위 방식은 우선순위가 높은 스레드가 실행상태를 더 많이 가지도록 스케쥴링하는 것을 말하며, 우선순위 번호를 부여하여 개발자가 코드를 제어할 수 있다.
		- 우선순위는 1~10까지 부여되는데, 1이 가장낮고, 10이 가장높다. defualt 우선순위는 5로 할당받는다.
		- 우선순위 변경은 setPriority() 메소드를 이용하면 된다.
			```java
			thread.setPriority(우선순위);
			```
		-  우선순위 매개값으로 1~10까지의 값을 직접 주어도 되지만, 코드의 가독성을 높이기 위해 Thread 클래스의 상수를 사용할 수 있다.
			```java
			thread.setPriority(Thread.MAX_PRIORITY);
			thread.setPriority(Thread.NORM_PRIORITY);
			thread.setPriority(Thread.MIN_PRIORITY);	
			``` 
	- 싱글코어에서 동시성으로 실행할 경우, 우선 순위가 높은 스레드가 실행 기회를 더 많이 가지기 때문에 우선순위가 낮은 스레도보다 계산 작업을 빨리 끝낸다.
	- 쿼드코어일 경우에는 4개의 스레드가 병렬성으로 실행될 수 있기 때문에 4개 이하의 스레드를 실행 할 경우 우선순위 방식에 영향을 받지 않으며, 최소한 5개 이상의 실행되어야 우선순위의 영향을 받는다.
	
	- 순환할당방식은 시간할당량을 정하여 하나의 스레드를 정해진 시간만큼 실행하고 다시 다른 스레드를 실행하는 방식을 말한다.
	- 다만 가상 기계에 의해 정해지기 때문에 코드제어가 불가능하다. 
	
## 동기화 
- 멀티스레드에서 공유객체 관련 이슈 
	- 싱글 스레드 프로그램에서는 한개의 스레드가 객체를 독차지해서 사용하지만, 멀티스레드 프로그램에서는 스레드들이 객체를 공유해서 작업해야 하는 경우가 있다.
	- 이는 마치 여러사람이 계산기를 함께 나눠쓰는 상황과 같아서 사람 A가 계산기를 작업하다가 계산결과를 메모리에 저장한 뒤 잠시 자리를 비웠을때 사람 B가 계산기를 만져서 값을 변경한 경우와 유사하다.
	>Calculator
	```java
	public class Calculator{
		private int memory;
		public int getMemory(){
			return memory
		}
		
		public void setMemory(int memory){
			this.memory = memory;
			try{
				Thread.sleep(500);
			}catch(Exception e){}
			System.out.println(Thread.currentThread().getName()+" : " + this.memory);
		}
	}
	```
	>User1
	```java
	public class User1 extends Thread{
		private Calculator calculator;
		
		public setCalculator(Calcultor cal){
			this.setName("user1");
			this.calculator=cal;
		}
		
		public void run(){
			calculator.setMemory(1000);
		}
	}
	```
	>User2
	```java
	public class User2 extends Thread{
		private Calculator calculator;
		
		public setCalculator(Calcultor cal){
			this.setName("user2");
			this.calculator=cal;
		}
		
		public void run(){
			calculator.setMemory(50);
		}
	}
	```
	>MainThreadExample
	```java
	public class MainThreadExample{
		public static void main(String[] args){
			Calculator cal = new Calculator();
			User1 us1 = new User1();
			us1.setCalculator(cal);
			us1.start();
			
			User2 us2 = new User2();
			us2.setCalculator(cal);
			us2.start();
		}
	}
	```
- 동기화 메소드 및 동기화 블록
	- 위의 예시처럼 사용중인 객체를 다른 스레드가 변경할 수 없도록 스레드 작업이 끝날때까지 객체에 잠금을 걸 수 있다. 멀티스레드 프로그램에서 단 하나의 스레만 실행할 수 있는 코드 영역을 임계영역이라고 한다. 
	- 자바는 임계영역을 지정하기 위해 동기화 메소드와 동기화 블록을 제공한다. 스레드가 객체 내부의 동기화 메소드 또는 블록에 들어가면 즉시 객체에 잠금을 걸어 다른 스레드가 임계 영역 코드를 실행하지 못하게 한다.
	- 동기화 메소드를 만드는 방법은 다음과 같이 메소드 선언에 synchronized 키워드를 붙이고 , 인스턴스와 정적 메소드든 어디든 붙일 수 있다.
		```java
		public synchronized void method(){
			임계영역; // 단하나의 스레드만 실행
		}
		```
		>Calculator
		```java
		public class Calculator{
			private int memory;
			public int getMemory(){
				return memory
			}
			
			public synchronized void setMemory(int memory){
				this.memory = memory;
				try{
					Thread.sleep(500);
				}catch(Exception e){}
				System.out.println(Thread.currentThread().getName()+" : " + this.memory);
			}
		}
		```

	- 동기화 메소드는 메소드 전체 내용이 임계영역이므로 스레드가 메소드를 실행하는 즉시 객체에는 잠금이 일어나고, 동기화 메소드가 종료 되면 잠금이 풀린다. 메소드 전체 내용이 아니라 일부 내용만 임계영역으로 만들고 싶다면 다음과 같이 동기화 블록을 만들면 된다.
		```java
		public void method(){
			//여러 스레드가 실행가능 영역
			synchronized(공유객체){
				임계영역 // 단하나의 스레드만 실행
			}
			// 여러 스레드가 실행 가능 영역
		}
		```
	- 동기화 블록의 외부 코드들은 여러 스레드가 동시에 실행할 수 있디만, 동기화 블록의 내부코드는 임계영역이므로 한번에 한 스레드만 실행할 수 있고, 다른 스레드는 실행할 수 없다. 만약 동기화 메소드와 동기화 블록이 여러개 있을 경우, 스레드가 이들중 하나를 실행할 때 다른 스레드는 물론이고 다른 동기화 메소드 및 블록도 실행할 수 없다. 하지만 일반 메소드는 실행이 가능하다.
		>Calculator
		```java
		public class Calculator{
			private int memory;
			public int getMemory(){
				return memory
			}
			
			public void setMemory(int memory){
				synchronized(this){
					this.memory = memory;
					try{
						Thread.sleep(500);
					}catch(Exception e){}
					System.out.println(Thread.currentThread().getName()+" : " + this.memory);
				}
			}
		}
		```
- 쓰레드 상태
	- 객체 생성 
		- new : 쓰레드 객체가 생성 아직 start() 메소드가 호출되기 전.
	- 실행대기 <-> 실행: start 메소드 호출시 스레드 스케쥴링으로 선택된 스레드가 CPU 를 점유하고, run 메소드를 실행하며 실행대기를 오가는 상태
		- runnable : 실행상태로 언제든지 갈 수 있는 상태.
	- 일시정지
		- WAITING : 다른 스렏드가 통지할때까지 기다리는 상태
		- TIMED_WAITING : 주어진 시간동안 기다리는 상태
		- BLOCKED : 사용하고자 하는 객체의 락이 풀릴때까지 기다리는 상태
	- 종료
		- TERMINATED : 실행을 마친 상태  
		>StatePrintThread 
		```java
		public class StatePrintThread extends Thread{
			private Thread targetThread;
		
			public StatePrintThread(Thread targetThread){
				this.targetThread = targetThread;
			}
		
			public void run(){
				while(true){
					Thread.State state = targetThread.getState();
					System.out.println("타겟 스레드 상태 : "+ state);
					
					if(state == Thread.State.NEW){
						targetThread.start();
					}
					
					if(state== Thread.State.TERMINATED){
						break;
					}
					
					 try{
						 Thread.sleep(500);
					 }catch(Exception e){}
				}
			}
		}
		```
		>TargetThread
		```java
		public class TargetThread extends Thread{
			public void run(){
				for(long i = 0;i<100000000;i++){}

				try{
					Thread.sleep(1500);
				}catch(Exception e){}
				
				for(long i=0;i<100000000;i++){}
			}
		}		
		```
		>Main
		```java
		public class mainExample{
			public static void main(String[] args){
				StatePrintThread stateThread = new StatePrintThread(new TargetThread());
				stateThread.start();
			}
		}
		```
## 스레드 상태제어
- 상태제어 메소드 종류
	- interrupt() : 일시정지상태의 스레드에서 InterruptException 예외를 발생시켜 예외처리 코드에서 실행대기 상태로 가거나 종료상태로 갈 수 있도록 한다.
	- notify(), notifyAll() : 동기화 블록내에서 wait 메소드에 의해 일시정지상태에 있는 스레드를 실행 대기 상태로 만든다. -> **Object 클래스 메소드**
	- resume() : suspend() 메소드에 의해 일시정지 상태에 있는 스레드를 실행 대기 상태로 만든다.
	- sleep(long millis) : 주어진 시간동안 일시정지 상태로 만든다.
	- join() : join 메소드를 호출한 스레드는 일시정지상태가 되며, 실행대기 상태로 가려면 join 메소드를 멤버로 가지는 스레드가 종료되거나, 매개값으로 주어진 시간이 지나야한다.
	- wait() : 동기화 블록 내에서 스레드를 일시정지 상태로 만든다. 매개값으로 주어진 시간이 지나면 자동적으로 실행대기 상태가 된다. 시간이 주어지지 않으면 메소드에 의해 실행대기 상태로 갈 수 있다.  -> **Object 클래스 메소드**
	- yield() : 실행 중에 우선순위가 동일한 다른스레드에게 실행을 양보한다.
	- stop() : 스레드를 즉시 종료한다.
	
- **yield()  실행양보 예제**

	> CommonTask 
	```java
	public class CommonTask implements Runnable{  
		boolean stop = false;  
		boolean work = true;  
	  
	@Override  
	public void run() {  
		while(!stop){  
			if(work){  
				System.out.println(Thread.currentThread().getName() + " 작업내용");  
			}else{  
				Thread.yield();  
			}
		}  
	}
	```
	> CommonThread
	```java
	public class CommonThread{  
		private Thread th;  
		private CommonTask task;  

		public CommonThread(String name, CommonTask task){  
		    this.task = task;  
			th = new Thread(this.task);  
			th.setName(name);  
		}  

		public void start(){  
		    th.start();  
		}  

		public void setWork(boolean t){  
		    task.work = t;  
		}  

		public void getWork(boolean t){  
		    task.work = t;  
		}
	}
	```
	> Main
	```java
	public class Main {  
		public static void main(String[] args) {  

		CommonTask run1 = new CommonTask();  
		CommonTask run2 = new CommonTask();  



		CommonThread th1 = new CommonThread("1t", run1);  
		CommonThread th2 = new CommonThread("2t", run2);  

		th1.setWork(false);  

		th1.start();  
		th2.start();   
		}  
	}
	```
- join() 예제
	>SumThread 
	```java
	public class SumThread extends Thread{
		private long sum;
		public long getSum(){
			return sum;
		}
		public void setSum(long sum){
			this.sum=sum;
		}
		public void run(){
			for(int i = 1; i<= 100;i++){
				sum+=i;
			}
		}
	}
	```
	>Main
	```java
	public class Main{
		public static void main(String[] args){
			SumThread sumThread = new SumThread();
			sumThread.start();
			
			try{
				sumThread.join(); // sumThread 가 종료할때까지 메인 스레드를 일시정지시킴
			}catch(InterruptException e){}
			
			System.out.println(sumThread.getSum());
		}
	}
	```
- 쓰레드간 협업 (wait(), notify(), notifyAll())
	-  경우에 따라서 두개의 스레드를 번갈아가며 실행해야 할 경우가 있다. 
	- 정확한 교대작업이 필요할 경우 , 자신의 작업이 끝나면 상대방 스레드를 일시 정지 상태에서 풀어주고, 자신은 일시 정지 상태로 만드는 것이다.
	- 이 방법의 핵심은 공유객체에 있다. 공유 객체는 두 스레드가 작업할 내용을 각각 동기화 메소드로 구분해 놓는다.
	- 한 스레드가 작업을 완료하면 notify() 메소드를 호출해서 일시정지상태에 있는 다른 스레드를 실행대기 상태로 만들어 놓고
	- 자신은 두번 작업을 하지 않도록 wait() 메소드를 호출하여 일시정지 상태로 만든다.
	- 만약 wait () 대신 wait(long timeout) 이나 wait(long timeout, int nanos) 를 사용하면 notify 를 호출하지 않더라도 지정된 시간이 지나면 스레드가 자동적으로 실행 대기 상태가 된다.
	-  notifyAll() 메소드는 wait 에 의해 일시정지된 모든 스레드를 실행 대기 상태로 만든다.
- 예시 WorkObject
	> WorkObject
	```java
	public class WorkObject{
		public synchronized void methodA(){
			System.out.println("Thread A의 methodA() 실행");
			notify(); //일시정지 상태에 있는 Thread B 를 실행대기 상태로 만듬
			try{
				wait(); //ThreadA 를 일시 정지상태로 만듬
			}catch(InterruptedException e){}
		}
		
		public synchronized void methodB(){
			System.out.println("Thread B의 methodA() 실행");
			notify(); //일시정지 상태에 있는 Thread A 를 실행대기 상태로 만듬
			try{
				wait(); //Thread B 를 일시정지상태로 만듬
			}catch(InterruptedException e){}
		}
	}
	```
	>ThreadA
	```java
	public class ThreadA extends Thread{
		private WorkObject workObj;
		
		public ThreadA(WorkObject obj){
			this.workObj = obj; //공유객체를 매개객체로 받아 필드에 저장
		}
		
		@Override
		public void run(){
			for(int i=0;i<10;i++){
				workObj.methodA();
			}
		}
	}
	```
	>ThreadB
	```java
	public class ThreadB extends Thread{
		private WorkObject workObj;
		
		public ThreadB(WorkObject obj){
			this.workObj = obj; //공유객체를 매개객체로 받아 필드에 저장
		}
		
		@Override
		public void run(){
			for(int i=0;i<10;i++){
				workObj.methodB();
			}
		}
	}
	```
		
	>Main
	```java
	public class WaitNotifyExample{
		public static void main(String[] args){
			WorkObject shareObj = new WorkObject();
			
			ThreadA ta = new ThreadA(shareObj);
			ThreadB tb = new ThreadB(shareObj);
			
			ta.start();
			ta.start();
		}
	}
	//결과
	methodA()
	methodB()
	methodA()
	methodB()
	methodA()
	methodB()
	
	```
- 데이터 저장과 가져오는 예시
		>DataBox
	```java
	public class DataBox{
		private String data;
		public synchronized String getData(){
			if(this.data==null){
				try{
					wait();
				}catch(InterruptedException e){}
			}
		}
		
		String returnValue = data;
		System.out.println("소비된 데이터 : " +returnValue )
		data = null;
		notify();
		return returnValue;
		
		public synchronized void setData(String data){
			if(this.data != null){
				try{
					wait();
				}catch(InterruptedException e){}
			}
			this.data = data;
			System.out.println("저장된 데이터 : " +this.data)
			notify();
		}
	}	
	```
	>ProducerThread 
	```java
	public class ProducerThread extends Thread{
		private DataBox dataBox;
		public ProducerThread(DataBox box){
			this.dataBox = box;
		}
		
		@Override
		public void run(){
			for(int i = 1; i<3;i++){
				String data="data-" + i;
				dataBox.setData(data); 
			}
		}
	}
	```
	>ConsumerThread 
	```java
	public class ConsumerThread extends Thread{
		private DataBox dataBox;
		public ProducerThread(DataBox box){
			this.dataBox = box;
		}
		
		@Override
		public void run(){
			for(int i = 1; i<3;i++){
				String data = dataBox.getData(); 
			}
		}
	}
	```
	> Main
	```java
	public class WaitNotifyExample{
		public static void main(String[] args){
			DataBox d = new DataBox();
			
			ProducerThread producerThread = new ProducerThread(d);
			ConsumerThread consumerThread = new ConsumerThread(d);
			
			producerThread.start();
			consumerThread.start();
		}
	}
	```
## 스레드의 안전한 종료
- 스레드는 run 메소드가 끝나면 자동적으로 종료된다. run 메소드가 정상적으로 종료되도록 유도하는 것이 최선이 방법이다.
-  stop 메소드는 불완전한 종료로서 deprecated  되며, 아래의 stop 플래그와 interrupt() 메소드를 이용하는 방법으로 사용된다 .

- stop 플래그를 이용하는 방법
	```java
	public class CustomThread extends Thread{
		private boolean stop;
		public void run(){
			while(!stop){
				스레드가 반복 실행함
			}
			//스레드가 사용한 자원 정리
		}
	}
	```

	>PrintThread
	```java
	public class PrintThread{
		private boolean stop;
		public void setStop(boolean stop){
			this.stop = stop;
		}
		
		public void run(){
			while(!stop){
				System.out.println("실행중");
			}
			System.out.println("자원정리");
			System.out.println("종료");
		}
	}
	```
	>Main - 1초 후 스레드를 중지시킴
	```  java
	public class StopFlagExample{
		public static void main(String[] args){
			PrintThread pt = new PrintThead();
			pt.start();
			
			try{
				Thread.sleep(1000);
			}catch(InterruptedException e){}
		}
		pt.setStop(true);
	}
	```
- interrupt() 메소드를 이용한 방법
	- interrupt 메소드는 스레드가 일시정지 상태에 있을때 InterruptException  예외를 발생시키는 역할을 한다. 이것을 이용하면 run 메소드를 정상적으로 종료시킬수 있다.
		>PrintThread
		```java
		public class PrintThread extends Thread{
			public void run(){
				try{
					while(true){
						System.out.println("실행중");
						Thread.sleep(1);
					}
				}catch(InterruptedException e){}
				System.out.println("자원정리");
				System.out.println("실행종료");
			}
		}
		```
		
		
		>Main
		```java
		public class InterruptExample{
			public static void main(String[] args){
				Thread thread = new PrintThread();
				thread.start();
				
				try{
				}catch(InterruptedException e){}
				
				thread.interrupt();
				
			}
		}
		```
	- 스레드가 실행 대기 또는 실행상태에 있을때 interrupt() 메소드가 실행되면 즉시 InterruptException 예외가 발생하지 않는다.
	- 스레드가 미래에 일시정지상태가 되면 InterruptException 예외가 발생한다는 것이다. 따라서 **스레드가 일시정지 상태가 되지 않으면 interrupt() 메소드 호출은 아무런 의미가 없다.**
	- 그래서 짧은 시간이나마 일시정지를 시키기 위해 sleep 메소드를 사용했다.
	- 일시정지를 만들지 않고도 **interrupt() 호출 여부를 알수 있는 방법은 interrupt 메소드가 호출되었다면, 스레드는 interrupted() 와 isInterrupted 메소드는 true 를 리턴**한다. 
	- interrupted 는 정적메소드로 현재 스레드가 interrupted 되었는지 확인하고 isInterrupted 는 인스턴스 메소드로 현재 스레드가 interrupted 되었는지 확인할때 사용한다.
		```java
		boolean status =Thread.interrupted();
		boolean status =Thread.isInterrupted(); 
		```
		> sleep 을 제거하고 interrupted() 를 활용한 스레드 종료
		```java
		public class PrintThread extends Thread{
			public void run(){
				try{
					while(true){
						System.out.println("실행중");
						if(Thread.interrupted()){
							break;
						}
					}
				}catch(InterruptedException e){}
				System.out.println("자원정리");
				System.out.println("실행종료");
			}
		}
		```

	
	

