
##  블로킹 방식의 작업완료 통보
- ExecutorService 의 submit 메소드는 매개값으로 준 Runnable 과 Callable 작업을 스레드풀 작업큐에 저장하고 Future 메소드를 리턴한다. 

|   리턴타입 | 메소드명 (매개변수) |
|-------------------|--------------
|    Future<?>| execute(Runnable task) | 
|    Future < V> | submit(Runnable task, V result) |
|    Future < V> | submit(Callable< V > task|

- Future 객체는 작업 결과가 아니라 작업이 완료될때 까지 기다렸다가 최종 결과를 얻는데 사용된다. 
- 그래서 Future 를 지연완료 객체라 한다.
- Future 의 get() 메소드를 호출하면 스레드가 작업을 완료할때까지 블로킹되었다가 작업을 완료하면 처리결과를 리턴한다.

	|리턴타입 | 메소드명 (매개변수) | 설명 | 
	|-------------------|------------------- |-------------------|
	|    V| get() | 작업이 완료될때까지 블로킹 되었다가 처리결과 V를 리턴 |
	|    V | get(long timeout, TimeUnit unit) | timeout 시간전에 작업이 완료되면 결과 V를 리턴하지만 작업이 완료되지 않으면 TimeOutException 을 발생시킴 | 

- 리턴 타입 V 는 submit(Runnable task, V result) 두번째 매개값인 V 타입이거나, submit(Callable< V > task) 의 Callable 타입 파라미터 V 타입이다. 

	|   메소드명| 작업 완료 후 리턴타입 | 작업처리중 예외 발생
	|-------------------|-------------- | ---------------|
	|    execute(Runnable task)| future.get() -> null | future.get() -> 예외발생 | 
	|    submit(Runnable task, Integer result) | future.get() -> int 타입값|future.get() -> 예외발생 |
	|    submit(Callable< String > task |future.get() -> String 타입값|future.get() -> 예외발생 |

- Future 를 이용한 블로킹 방식의 작업완료통보는 스레드가 작업완료하기 전까지 get() 메소드가 블로킹되므로 다른 코드를 실행할 수 없다.
- 만약 UI를 변경하고 이벤트를 처리하는 스레드가 get() 메소드를 호출하면 작업을 완료하기 전까지 UI 를 변경할 수 없고 이벤트를 처리할 수 없다.
- 그렇기 때문에 get() 메소드를  호출하는 스레드는 새로운 스레드이거나 또 다른 스레드가 되어야 한다.
> 새로운 스레드를 생성해서 호출
```java
new Thread(new Runnable(){
	@Override
	public void run(){
		try{
			future.get();
		}catch(Exception e){}
	}
}).start();
```
> 스레드풀에서 호출
```java
executorService(new Runnable(){
	@Override
	public void run(){
		try{
			future.get();
		}catch(Exception e){}
	}
});
```
- Future 메소드는 다른 메소드도 제공한다

	|   리턴타입 | 메소드명 (매개변수) | 설명 |
	|-------------------|--------------|------------------------|
	|    boolean | cancel(boolean mayInterruptIfRunning | 작업처리가 진행 중일 경우 취소 |
	|   boolean | isCancelled() | 작업이 취소되었는지 여부 |
	|   boolean | isDone() | 작업처리가 완료되었는지 여부 |
	- cancel 메소드는 작업을 취소하고 싶을 경우 호출할 수 있다.
	- cancel 메소드는 작업이 시작되기 전이라면 mayInterruptIfRunning 매개값과는 상관없이 작업 취소후 true 를 리턴하지만 작업이 진행중이라면 mayInterruptIfRunning 매개값이 true 일 경우에만 interrupt 한다. 
	- cancel 메소드는 작입이 완료되었을때, 어떤 이유로 취소될수 없다면 false 를 리턴한다.
	- isCancelled 메소드는 작업이 완료되기전에 작업이 취소되었을 경우에면 true 를 리턴한다.
	- isDone 메소드는 정상적 예외 취소등 어떤 이유에서건 작업이 완료되었다면 true를 리턴한다.

## 리턴값이 없는 작업 완료통보   
- 리턴값이 없는 작업일 경우 Runnable 객체로 생성하면 된다. 
```java
Runnable task = new Runnable(){
	@Override
	public void run(){
		//스레드가 처리할 작업 내용
	}
}
```

- 결과값이 없는 작업 처리 요청은 submit(Runnable task) 메소드를 이용하면 된다. 결과값이 없음에도 불구하고 다음과 같이 Future 객체를 리턴하는데, 이것은 스레드가 작업처리를 정상적으로 완료했는지, 처리도중 예외가 발생했는지 확인하기 위해서다.
```java
Future future = executorService.submit(task);
```
- 작업 처리가 완료되었다면 get 메소드는 null을 리턴하지만, 스레드가 작업처리 도중 interrupt 되면 interruptException을 발생시키고, 작업처리 도중 예외가 발생하면 ExecutionException 을 발생시킨다.  그래서 다음과 같은 예외처리 코드가 필요하다.
```java
try{
	future.get()
}catch(InterruptedException e){
	// 작업처리 도중 스레드가 interrupt 경우 실행할 코드
}catch(ExecutionException e){
	// 작업처리 도중 예외가 발생된 경우
}
```
> 1부터 10까지 합을 출력하는 작업 
```java
public class NoResultExample{
	public static void main(String[] args){
		ExecutorService es = Executors.newFixedThreadPool(
			Runtime.getRuntime().availableProcessors()
		);
	
	
		System.out.println("작업처리요청);
		
		Runnable runObj = new Runnable(){
			@Override
			public void run(){
				int sum = 0; 
				for(int i = 0; i<=10; i++){
					sum += i;
					System.out.println( sum );
				}
			}
		};
		
		Future ft = executorService.submit(runObj);
		try{
			ft.get();
		}catch(Exception e){
			System.out.println(e.getMessage());
		}
		executorService.shutdown();
	}
}
```

## 리턴값이 있는 작업 완료통보 
- 스레드풀의 스레드가 작업 완료한 후에 처리결과를 얻어야한다면 작업 객체를 Callable 로 생성한다.
```java
Callable<T> task = new Callable<T>(){
	@Override
	public T call() throws Exception{
		//스레드가 처리할 작업 내용
		return T;
	}
}
```
- Callable 작업의 처리 요청은 Runnable 작업과 마찬가지로 ExecutorService 의 submit() 메소드를 호출한다.
- submit() 메소드는 작업큐에 Callable 객체를 저장하고 즉시 Future < T >를 리턴한다.
- 이때 T 는 call 메소드가 리턴하는 타입이다.
```java
Future<T> future = executorService.submit(task);
```
- 스레드 풀의 스레드가 Callable 객체의 call() 메소드를 모두 실행하고, T 타입의 값을 리턴하면 , Future < T > 의  get 메소드는 블로킹이 해제되고 T타입의 값을 리턴한다.
```java
try{
	T result = future.get();
}catch(InterruptedException e){}
}catch(ExecutionException e){}
```

>1부터 10까지 합을 리턴하는 Callable 객체 생성, 스레드풀의 스레드가 처리하는 예제
```java
public class ResultByCallableExample{
	public static void main(String[] args){
		ExecutorService es = Executor.newFixedThreadPool(Runtime.getRuntime().availableProcessors());
		
		System.out.println("작업처리요청");
		
		Callable<Integer> call() throws Exception{
			int sum = 0;
			for(int i = 0 ; i<=10;i++){
				int sum = 0;
				for(int i =0; i<=10; i++){
					sum+=i;
				}
				return sum;
			}
		};
		Future<Integer> ft = executorService.submit(task);
		
		try{
			int sum = future.get();
			System.out.println(sum + " 합 " );
		}catch(Exception e){
			System.out.println(e.getMessage());
		}
		es.shutdonw();
	}
}
```

## 작업처리 결과를 외부 객체에 저장
- 스레드가 작업을 완료하고 외부 Result 객체에 작업 결과를 저장하면, Result 객체를 사용해서 어떤 작업을 진행할수 있는데, 대개 Result 객체는 공유객체가 되어, 두개 이상의 스레드 작업을 취합할 목적으로 이용된다. 
- 이런 작업을 하기 위해서는 ExecutorService 의 submit(Runnable task, V result) 메소드를 사용할 수 있는데, V가 바로 Result 타입이다.
- 메소드를 호출하면 즉시 Future< V > 가 리턴되는데, Future 의 get() 메소드를 호출하면 스레드가 작업을 완료할때까지 블로킹되었다가 작업을 완료하면 V 타입 객체를 리턴한다.
- 리턴된 객체는 submit() 의 두번째 매개값으로 준 객체와 동일한데 차이점은 스레드 처리 결과가 내부에 저장되어 있다.
```java
Result result = ..;
Runnable task = new Task(result);
Future<Result> future = executorService.submit(task, result);
result = future.get();
```  
- 작업 객체는 Runnable 구현 클래스로 생성하는데, 주의할 점은 스레드에서 결과를 저장하기 위해 외부 Result 객체를 사용해야 하므로 생성자를 통해 Result 객체를 주입받도록 해야 한다.
```java
class Task implements Runnable{
	Result result;
	Task(Result result){
		this.result = result;
	}
	
	@Override
	public void run{
		//작업코드
		//처리결과를 result 에 저장
	}
	
}
```
>1 부터 10까지 외부 객체에 저장
```java
public class ResultByRunnableExample{
	public static void main(String[] args){
		ExecutorService es = Excutors.newFixedThreadPool(
			Runtime.getRuntime().availalbeProcessors()
		);
		class Result{
			int accumValue;
			synchronized void addValue(int value){
				accumValue += value;
			}
		}
		
		class Task implements Runnable{
			Result result;
			Task(Result result){
				this.result = result;
			}
			
			@Override
			public void run(){
				int sum=0;
				for(int i=0;i<=10;i++){
					sum+=i;
				}
				result.addValue(sum);
			}
		}
		
		Result result = new Result();
		Runnable task1 = new Task(result);
		Runnable task2 = new Task(result);
		
		Future<Result> ft1 = es.submit(task1, result);
		Future<Result> ft2 = es.submit(task2, result);
		
		try{
			result
		}catch(InterruptedException e){}
		}catch(ExecutionException e){}
		
		es.shutdown();
	}
}
```

## 작업 완료 순으로 통보
- 작업 요청 순서대로 작업처리가 완료되는 것은 아니다.
- 작업의 양과 스레드 스케쥴링에 따라 먼저 요청한 작업이 나중에 완료되는 경우도 발생한다.
- 여러개의 작업들이 순차적으로 처리될 필요성이 없고, 처리결과도 순차적으로 이용할 필요가 없다면 작업처리가 완료된 것 부터 결과를 얻어 이용하면 된다.
- 스레드풀에서 작업이 완료된 것만 통보받는 방법이 있는데 CompletionService 를 이용하는 것이다.
- CompletionService 는 처리 완료된 작업을 가져오는 poll, take 메소드를 제공한다.

	|  리턴타입  |  메소드명  |  설명 | 
	|------------|------------|------------|
	|Future< V > | poll() | 완료된 작업의 Future를 가져오고, 없다면 null 리턴|
	|Future< V > | poll(long timeout, TimeUnit unit) | 완료된 작업의 Future를 가져오고, 없다면 timeout 블로킹|
	|Future< V > | take() | 완료된 작업의 Future 를 가져오고, 없다면 있을때까지 블로킹됨|
	|Future< V > | submit(Callable< V > task | 쓰레드풀에 Callable 작업 처리 요청|
	|Future< V > | submit(Runnable task, V result) | 쓰레드풀에 Callable 작업 처리 요청|
- CompletionService 구현 클래스는 ExecutorCompletionService < V > 이다. 객체를 생성할때 생성자 매개값으로 ExecutorService 를 제공하면 된다.
	```java
	ExecutorService es = Executors.newFixedThreadPool(
		Runtime.getRuntime().availableProcessors()
	);
	
	CompletionService< V > completionService = new ExecutorCompletionService< V >(executorService);
	```
- poll(), take() 메소드를 이용해서 처리 완료된 Future 를 얻으려면 CompletionService 의 submit() 메소드로 작업처리 요청을 해야한다.
	```java
	completionService.submit(Callable<V> task);
	completionService.submit(Runnable task, V result);
	```
- take() 메소드를 호출하여 완료된 Callable 작업이 있을 때까지 블로킹 되었다가 완료된 작업의 Future를 얻고, get() 메소드로 결과값을 얻어내는 코드이다. while 문은 어플리케이션 종료될때까지 반복 실행해야하므로 스레드풀의 스레드에서 실행하는 것이 좋다
	```java
	executorService.submit(new Runnable(){
		@Override
		public void run(){
			while(true){
				try{
					Future<Integer> future = compeletionService.take();
					int val = future.get();
					System.out.println("처리결과"+value);
				}catch(Exception e){
					break;
				}
			}
		}
	});
	```
	- take() 메소드가 리턴하는 완료된 작업은 submit() 으로 처리 요청한 작업순서가 아니다. 작업의 내용에 따라서 먼저 요청한 작업이 나중에 완료될수 있으며, 더 이상 완료된 작업을 가져올 필요가 없다면 take() 블로킹에서 빠져나와 while문을 종료한다.
	- ExecutorService 의 shutdownNow() 을 호출하면 take() 에서 InterruptedException 이 발생하고 catch 절에서 break 가 되어 while 문이 종료하게 된다.
	> 3개의 Callable 작업을 처리요청하고 처리가 완료되는 순으로 작업결과 출력
	```java
	public class CompletionServiceExample {  
		public static void main(String[] args) {  
			ExecutorService executorService = Executors.newFixedThreadPool(  
				Runtime.getRuntime().availableProcessors()  
			);  

			CompletionService<Integer> completionService = new ExecutorCompletionService<Integer>(executorService);  

			System.out.println("작업처리요청");  

			for(int i = 0; i< 3 ; i++){  
				completionService.submit(new Callable<Integer>(){  
					@Override  
					public Integer call() throws Exception {  
						int sum = 0;
						
						for(int i = 0;i<=100;i++){  
							sum+=i;  
						}  
						return sum;  
					}  
				});  
			}  


			executorService.submit(new Runnable() {  
				@Override  
				public void run() {  
					while (true){  
						try {  
							Future<Integer> future = completionService.take();  
							int value = future.get();  
							System.out.println("처리결과" + value);  
						} catch (InterruptedException e) {  
							e.printStackTrace();  
						} catch (ExecutionException e) {  
							e.printStackTrace();  
						}  
					}  
				}  
			});  

			try{  
				Thread.sleep(3000);  
			}catch (InterruptedException e){}  
			executorService.shutdownNow();  
		}  
	}
	```
	
## 콜백방식의 작업완료 통보
- 콜백이란 스레드에게 작업 처리를 요청한 후, 스레드가 작업을 완료하면 특정 메소드를 자동 실행하는 기법을 말한다.
- 자동 실행되는 메소드를 콜백 메소드라고 한다.
- 블로킹 방식은 작업 처리를 요청한 후에 작업이 완료되면 블로킹된다. 
- 콜백방식은 작업처리를 요청한 후 결과를 기다릴 필요 없이 다른 기능을 수행할 수 있으며, 작업 처리가 완료되면 자동적으로 콜백메소드가 실행되어 결과를 알 수 있기 때문이다.
- 다만 자바 ExecutorService 에서 공식적으로 콜백을 위한 메소드는 제공하지 않는다. 하지만 Runnable 구현 클래스를 작성할때 콜백기능을 구현할 수 있다.
- 먼저 콜백 메소드를 가진 클래스가 있어야하는데, 직접 정의해도 좋고 java.nio.channels.CompeltionHandler 를 이용해도 좋다.
- 이 인터페이스는 NIO 패키지에 포함되어 있는데 비동기 통신에서 콜백 객체를 만들때 사용된다.
	```java
	CompletionHandler<V, A> callback = new CompletionHandler<V, A>(){
		@Override
		public void completed(V result, A attachment){...}
		@Override
		public void failed(Throwable exc, A attachment){...}
	};
	```
- CompletionHandler 는 completed() 메소드와 fail() 메소드가 있는데, completed 는 작업을 완료할때 호출되는 콜백메소드이며, failed() 작업 처리 도중 예외가 발생했을때 호출되는 콜백메소드이다. 
- V타입 파라미터는 결과값의 타입, A는 첨부값의 타입이며, **첨부값은 콜백 메소드 결과값 이외에 추가적으로 전달하는 객체**이다 만약 첨부값이 필요 없다면 **A 는 void 로 지정**해주면 된다.  
	```java
	Runnable task = new Runnable(){
		@Override
		public void run(){
			try{
				V result = ...;
				callback.completed(result, null);
			}catch(Excetion e){
				callback.failed(e, null);
			}
		}
	}
	```
	> 두개의 문자열을 정수화 하여 더하는 작업을 콜백방식으로 처리, 첫번째 작업은 3, 3 을 주었고 두번째는 5, 6 을 준다. 
	```java
	public class CallbackExample {  
		private ExecutorService executorService;  

		public CallbackExample() {  
			this.executorService = Executors.newFixedThreadPool(  
			       Runtime.getRuntime().availableProcessors()  
			);  
		}  

		private CompletionHandler<Integer, Void> callback = new CompletionHandler<Integer, Void>() {  
			@Override  
			public void completed(Integer result, Void attachment) {  
			   System.out.println("결과" + result*result);  
			}  

			@Override  
			public void failed(Throwable exc, Void attachment) {  
			   System.out.println("실패"+ exc.toString());  
			}  
		};  

		public void doWork(final String x, final String y) {  
			Runnable task = new Runnable() {  
			
				@Override  
				public void run() {  
					try {  
						int intX = Integer.parseInt(x);  
						int intY = Integer.parseInt(y);  
						int result = intX + intY;  
						callback.completed(result, null);  
					}catch (NumberFormatException e){  
						callback.failed(e, null);  
					}  
				}  
			};  
			executorService.submit(task);  
		}  

		public void finish(){  
			executorService.shutdown();  
		}  

		public static void main(String[] args) {  
			CallbackExample example = new CallbackExample();  
			example.doWork("3","3");  
			example.doWork("5", "6"); 
			example.finish();
		}  
	}
	```
	```
	결과121
	결과36
	```


