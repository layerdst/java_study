## 데몬스레드
- 데몬 스레드는 주 스레드의 작업을 돕는 보조적인 역할을 수행하는 스레드이다. 
- 주스레드가 종료되면 데몬스레드는 강제적으로 종료된다. 
- 스레드를 데몬으로 만들기 위해서는 주 스레드가 데몬이 될 스레드의 setDaemon(true) 를 호출해주면 된다.
- 아래의 코드를 보면 메인 스레드가 주 스레드이고, AutoSaveThread 가 데몬 스레드가 된다.
	>Main
	```java
	public static void main(String[] args){
		AutoSaveThread thread = new AutoSaveThread();
		thread.setDaemon(true);
		thread.start()
	}
	```
-주의할 점은 start() 메소드가 호출되고나서 setDaemon(true)를 호출하면 IllegalThreadStateException 이 발생하기 때문에 **start() 메소드 호출 전에 setDaemon(true) 를 호출**해야한다.
- 실행중인 스레드가 데몬스레드 인지 판별하기 위해서는 isDaemon() 메소드의 리턴값을 보면 된다. 
	>AutoSaveThread
	```java
	public class AutoSaveThread extends Thread{
		public void save(){
			System.out.println("작업 내용을 저장함");
		}
		
		@Override
		public void run(){
			while(true){
				try{
					Thread.sleep(1000);
				}catch(InterrutpedException e){
					break;
				}
				save();
			}
		}
	}
	```
	>Main
	```java
	public class DaemonExample{
		public static void main(String[] args){
			AutoSaveThread ast = new AutoSaveThread();
			ast.setDaemon(true);
			ast.start();
			
			try{
				Thread.sleep();
			}catch(InterruptedException e){}
		}
	}
	//작업 내용을 저장함
	//작업 내용을 저장함
	//작업 내용을 저장함
	//메인 스레드 종료
	```

## 쓰레드 그룹
- 관련된 스레드를 그룹으로 묶어 관리할 목적으로 사용한다.
- JVM 실행되면 system 스레드 그룹을 만들고 JVM 운영에 필요한 스레드를 생성해 system 스레드 그룹에 포함시킨다. 
- system 하위 스레드 그룹으로  main 을 만들고 메인 스레드를 main 스레드 그룹에 포함시킨다. 
- 스레드는 반드시 하나의 스레드 그룹에 포함되는데, 명시적으로 스레드 그룹에 포함시키지 않으면, 기본적으로 자신을 생성한 스레드와 같은 스레드 그룹에 속하게 된다.
- 우리가 생성하는 작업 스레드는 대부분 main 스레드가 생성하므로 기본적으로 main 스레드 그룹에 속하게 된다.
## 스레드 그룹얻기
- 스레드 그룹 이름 얻기
	```java
	ThreadGroup group = Thread.currentThread().getThreadGroup();
	String groupName = group.getName();
	```
- 스레드 그룹 정보 얻기
	```java
	Map<Thread, StackTraceElement[]> map = Thread.getAllStackTraces();
	```
	- getAllStackTraces() 메소드는 Map 타입의 객체를 리턴하는데, 키는 스레드 객체이고, 값은 스레드 상태 기록을 갖고 있는 StackTraceElement[] 배열이다.
	>Main
	```java
	public class ThreadInfoExample{
		public static void main(String[] args){
			AutoSaveThread ast= new AutoSavThread();
			ast.setName("autoSaveThread");
			ast.setDaemon(true);
			ast.start();
			
			Map<Thread, StackTraceElement[]> map = Thread.getAllStackTrace();
			Set<Thread> threads = map.keySet();
			threads.stream().forEach(a->{
				System.out.print("[" + a.getThreadGroup().getName() + "]");  
				System.out.print(a.getName() + " : ");  
				System.out.println(a.isDaemon() ? "데몬" : "");
			});
		}
	}
	```
## 스레드 그룹생성
- 명시적으로 스레드 그룹을 만들고 싶으면 다음 생성자중 하나를 이용해 ThreadGroup 객체를 만들면 된다.
- 부모 ThreadGroup 과 이름을 매개값으로 줄 수 있다.
	```java
	ThreadGroup tg = new ThreadGroup(String name);
	ThreadGroup tg = new ThreadGroup(ThreadGroup parent, String name);
	```
- 스레드 그룹 생성시 부모 스레드 그룹을 지정하지 않으면 **현재 스레드가 속한 그룹의 하위 그룹**으로 생성된다.
- 예를 들어 main 스레드가 ThreadGroup(String name) 을 이용해서 새로운 스레드 그룹을 생성하면, main 스레드 그룹의 하위 스레드 그룹이 된다. 
- 새로운 스레드 그룹을 생성한 후 이 그룹에 스레드를 포함시키려면 Thread 객체를 생성할때 생성자 매개값으로 스레드 그룹을 지정하면 된다. 스레드 그룹을 매개값으로 갖는 Thread 생성자는 네가지가 있다.
```java
Thread t = new Thread(ThreadGoup group, Runnable target);
Thread t = new Thread(ThreadGoup group, Runnable target, String name);
Thread t = new Thread(ThreadGoup group, Runnable target, String name, long stackSize);
Thread t = new Thread(ThreadGoup group, String name);
```

## 스레드 그룹의 일괄 interrupt()
- 스레드 그룹에서 제공하는 interrupt() 메소드를 이용하면 그룹내에 포함된 모든 스레들을 일괄 interrupt 할 수 있다.
- 스레드 그룹의 interrupt() 메소드는 소속된 스레드의 interrupt() 메소드를 호출할 뿐 개별 스레드에서 발생하는 InterruptedException 에 대한 예외 처리를 하지 않는다
- 따라서 안전한 종료를 위해서는 개별 스레드가 예외 처리를 해야한다.
	> WorkThread
	```java
	public class WorkThread extends Thread{
		public WorkThread(ThreadGroup group, String threadName){
			super(threadGroup, threadName);
		}
		
		@Override
		public void run(){
			while(true){
				try{
					Thread.sleep(1000);
				}catch(InterruptedExeption e){
					System.out.println(getName() + " interrupted ");
					break;
				}
			}
			System.out.println(getName() + "종료됨");
		}
	}
	```
	>Main
	```java
	public class ThreadGroupExample{
		public static void main(String[] args){
			ThreadGroup gr = new ThreadGroup("myGroup");
			WorkThread workThreadA = new WorkThread(gr, "a");
			WorkThread workThreadB = new WorkThread(gr, "b");
			
			workThreadA.start();
			workThreadB.start();
			
			ThreadGroup mainGroup = Thread.currentThread().getThreadGroup();
			mainGroup.list();
			
			try{
				Thread.sleep(3000);
			catch(InterruptedException e){}
			gr.interrupt();
		}
	}
	```

## 스레드풀
- 병렬작업처리가 많아지면 스레드 개수가 증가되고, 그에 따른 스레드 생성과 스케쥴링으로 인해 CPU가 바빠져 메모리 사용량이 늘어난다.
- 갑작스런 병렬작업의 폭증으로 인한 스레드의 폭증을 막으려면 스레드풀(ThreadPool) 을 사용해야 한다.
- 스레드풀은 작업처리에 사용되는 스레드를 제한된 개수만큼 정해놓고 작업 큐에 들어오는 작업들을 하나씩 스레드가 맡아 처리한다.
- 작업처리가 끝난 스레드는 다시 작업 큐에서 새로운 작업을 가져와 처리한다.
- 자바는 스레드풀을 생성하고 사용할 수 있도록 concurrent 패키지에서 ExecutorService 인터페이스와 Executors 클래스를 제공하고 있다. 
- Executor의 다양한 정적 메소드를 이용해서 ExecutorService 구현 객체를 만들수 있는데, 이것이 바로 스레드 풀이다.
- 쓰레드풀 생성
	-  ExecutorService 구현 객체는 Executors 클래스의 다음 두가지 메소드 중 하나를 이용해 간편하게 생성할 수 있다
	
		|    메소드명 | 초기스레드수 | 코어스레드수 | 최대 스래드수|
		|:------------:|:------------:|:------------:|:------------:|
		|     newCachedThreadPool() | 0 | 0 | Integer.MAX_VALUE|
		|     newFixedThreadPool(int nThreads)  | 0 | nThreads | nThreads |
	- 초기 스레드 수는 ExecutorService 객체가 생성될때 기본적으로 생성되는 스레드 수
	- 코어 스레드 수는 스레드 수가 증가된 후 사용되지 않는 스레드를 스레드풀에서 제거할때 최소한 유지해야 할 스레드 수 
	- 최대 스레드 수는 스레드풀에서 관리하는 최대 스레드 수이다.
		```java
		ExecutorService service = Executor.newCachedThreadPool();			
		```
		- newCachedThreadPool() 메소드 특징은 초기스레드와 코어스레드가 각각 0 이지만, 스레드 개수보다 작업 개수가 많으면 새 스레드를 생성시켜 작업을 처리한다. 
		- 이론적으로는 int 값이 가질 수 있는 최대값만큼 스레드가 추가되지만, 운영체제 성능과 상황에 따라 달라진다.
		- 1개 이상 스레드가 추가되었을 경우 60초 동안 추가된 스레드가 아무 작업을 하지 않으면 추가된 스레드를 종료하고 풀에서 제거한다.
		```java
		ExecutorService service = Executor.newFixedThreadPool(
			Runtime.getRuntime().availableProcessors();
		);			
		```
		 - 스레드풀의 초기 스레드 수는 0개 코어스레드 수는 nThreads 이다.
		 - 스레드 수 보다 작업개수가 많으면 새 스레드를 생성시키고 작업을 처리한다.
		 - 최대 스레드 개수는 매개값으로 준 nThreads 이다
		 - 이 스레드풀은 스레드가 작ㅂ업을 처리하지 않고 놀고 있더라도, 스레드 개수가 줄지 않는다. 
	 - newCachedThreadPool() 과 newFixedThreadPool() 메소드를 사용하지 않고 코어스레드 개수와 최대스레드 개수를 설정하고 싶다면 직접 ThreadPoolExecutor 객체를 생성하면 된다. 
		 > 초기 스레드수 0개, 코어스레드수 3개, 최대스래드수 100개 스레드풀 생성
		 ```java
		 ExecutorService threadPool = new ThreadPoolExecutor(
			3,	// 코어스레드수
			100 // 최대스레드수
			120L // 놀고있는 시간
			TimeUnit.SECONDS // 놀고있는 시간 단위
			new SynchronousQueue<Runnable>() // 작업큐
		);
			 ```
## 스레드풀 종료
- 스레드풀의 스레드는 데몬스레드가 아니기 때문에 main 스레드가 종료되더라도 작업을 처리하기 위해 계속 실행상태로 남아있다. 그래서 main 메소드가 실행이 끝나도 애플리케이션 프로세스는 종료되지 않는다. 어플리케이션이 종료하려면 스레드풀을 종료시켜 스레드들이 종료상태가 되도록 처리해주어야 한다. 
- 다음 세개의 메소드를 이용해 종료하며 작업을 마무리하고 스레드풀을 종료할때에는 shutDown() 일반적으로 호출하고 , 강제 종료시에는 shotDownNow() 를 호출한다.

	|  리턴 타입   | 메소드명(매개변수) | 설명|
	|:---------------:|:--------------:|:------------------------------------------------:|
	|void| shutdown()|현재처리중인 작업뿐만아니라 작업큐에 대기하고 있는 모든 작업을 처리한 후에 스레드풀이 종료된다.|
	|List<Runnable>| shutdownNow()|현재 작업 처리 중인 스레드를 interrupt 해서 작업 중지를 시도하고 스레드풀을 종료시킨다. 리턴값은 작업 큐에 있는 미처리된 작업 <Runnable> 목록이다.|
	|boolean|awaitTerminaton(long timeout, TimeUnit unit) | shutDown() 메소드 호출 이후, 모든 작업 처리를 timeout 시간내 완료하면 true 를 리턴하고, 완료하지 못하면 작업처리중인 스레드를 interrupt 하고 false 를 리턴한다. 

## 작업생성과 처리요청
- 작업생성 
	- 하나의 작업은 Runnable 또는 Callable 구현클래스로 표현한다. 
	- 두 작업의 차이는 리턴값 유무 이다. 
	> Runnable 구현 클래스
	```java
	Runnable task = new Runnable(){
		@Override
		public void run(){
			//스레드가 처리할 작업
		}
	}
	```
	>Callable 구현 클래스
	```java
	Callable<T> task = new Callable<T> (){
		@Override
		public T call() throws Exception {
			return T;
		}			
	}
	```
- 작업 처리 요청
	- 작업 처리 요청이란 ExecutorService 작업 큐에 Runnable 또는 Callable 객체에 넣는 행위를 말한다.
	- ExecutorService 는 작업 처리 요청을 위해 다음 두가지 종류의 메소드를 제공한다.
		 
		|   리턴타입 | 메소드명 (매개변수) | 설명 |
		|-------------------|--------------|------------------------|
		|    void | execute(Runnable command) | - Runnable 을 작업큐에 저장하나  **작업결과를 받지 않음**
		|    Future<?>, Future < V>,  Future < V> | submit(Runnable task), submit(Runnable task, V result), submit(Callable< V > task) | Runnable 또는 Callable 을 작업큐에 저장, 리턴된 Future 를 통해 **작업처리 결과를 얻을 수 있음**|
	- 두 메소드는 작업결과의 유무, 작업처리 도중 예외의 두가지 차이가 있다.
	- execute 는 작업 처리도중 예외가 발생하면, 스레드가 종료되고 스레드풀에서 제거된다.
	- submit 은 예외가 발생하더라도, 스레드는 종료되지 않고 다음 작업을 위해 재사용된다.
	- 가급적 스레드 오버해더를 줄이기 위해선 **submit을 사용하는 것이 좋다**
	> Runnable 작업정의시 Exception 유도 및 Excute, submit 메소드 활용
	```java
	public class ExcuteExample{
		public static void main(String[] args) throws Exception{
			ExecutorService service = Executors.newFixedThreadPool(2);
			
			for(int i = 0; i<10 ; i++){
				Runnable runnable = new Runnable(){
					@Override
					public void run(){
						ThreadPoolExecutor threadPolExecutor = (ThreadPoolExecutor) service;
						int poolSize = threadPolExecutor.getPoolSize();
						String threadName = Thread.currentThread().getName();
						System.out.println("총스레드 개수 : " + poolSize + " / " + "작업스레드 이름" + threadName);
						int value = Integer.parseInt("삼");  
					}
				};
				service.execute(runnable);
				service.submit(runnable);
				
				Thread.sleep(10);
			}
			service.shutdown();
		}	
	}
	```
	>service.execute(runnable) 에러 메세지
	```
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-2
	총스레드 개수 : 1 / 작업스레드 이름pool-1-thread-1
	총스레드 개수 : 1 / 작업스레드 이름pool-1-thread-3
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-4
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-5
	Exception in thread "pool-1-thread-2" java.lang.NumberFormatException: For input string: "삼"
		at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:65)
		at java.base/java.lang.Integer.parseInt(Integer.java:652)
		at java.base/java.lang.Integer.parseInt(Integer.java:770)
		at thread.ExcuteExample$1.run(ExcuteExample.java:20)
		at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)
		at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)
		at java.base/java.lang.Thread.run(Thread.java:834)
	Exception in thread "pool-1-thread-3" java.lang.NumberFormatException: For input string: "삼"
	.. 같은 에러메세지
	Exception in thread "pool-1-thread-1" java.lang.NumberFormatException: For input string: "삼"
	.. 같은 에러메세지
	Exception in thread "pool-1-thread-4" java.lang.NumberFormatException: For input string: "삼"
	.. 같은 에러메세지
	Exception in thread "pool-1-thread-5" java.lang.NumberFormatException: For input string: "삼"
	.. 같은 에러메세지
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-7
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-8
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-9
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-10
	```
	- execute 메소드는 총 스레드 개수에는 영향이 없지만, 실행스레드 이름을 보면 모두 다른 스레드가 작업을 처리한다.
	- 이는 작업처리 도중 예외가 발생했기 때문이며 해당 스레드는 제거되고 계속 생성되기 때문이다/
	
	>service.submit(runnable) 에러 메세지 
	```
	총스레드 개수 : 1 / 작업스레드 이름pool-1-thread-1
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-2
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-2
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-1
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-2
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-1
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-2
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-1
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-2
	총스레드 개수 : 2 / 작업스레드 이름pool-1-thread-1
	```
	- submit 메소드는 예외가 발생하더라도 스레드가 종료되지 않고 재사용되어, 다른작업을 처리한다.
