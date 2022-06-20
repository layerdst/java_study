## 람다식
익명함수를 생성하기 위한 식으로 객체지향 언어보다는 함수지향 언어에 가깝다.  
```java
람다식 -> 매개변수 -> 익명 구현 객체
```
- 람다식 기본 문법
	- 타입 매개변수는 중괄호 블록을 실행하기 위해 필요한 값을 제공하는 역할을 한다. 변수 이름은 개발자가 자유롭게 줄수 있다.
		```java
		(타입 매개변수 , ... ) -> {실행문 }
		(int a) -> {System.out.println(a);} 
		```
	- 매개변수 타입은 런타임시 대입되는 값에 따라 자동으로 인식될 수 있어, 람다식에서는 매개변수 타입을 언급하지 않는다 . 
		```java
		(a) -> {System.out.println(a);} 
		```
	- 하나의 매개 변수만 있다면 () 를 생략하고, 하나의 실행문만 있다면 {} 도 생략 가능하다. 
		```java
		a -> System.out.println(a) 
		```
	- 만약 매개 변수가 없다면 람다식에서 매개변수 자리가 없어지므로 다음과 같이 빈괄호() 를 반드시 사용해야 한다.
		```java
		() -> {실행문;} 
		```
	- 중괄호를 실행하고 return 해야 한다면 다음과 같이 return 문으로 결과값을 지정할 수 있다
		```java
		(x,y) -> {return x+y;} 
		```
	- 중괄호 {} return 문만 있을 경우, 람다식에서 다음과 같이 작성하는 것이 정석이다.
		```java
		(x,y) ->  x+y 
		```

- 타겟 타입과 함수적 인터페이스
	- 람다식의 형태는 매개변수를 가진 코드블록이다. 때문에 자바의 메소드를 선언하는 것처럼 항상 클래스의 구성멤버로 선언되기 때문에 람다식은 단순 메소드를 선언하는 것이 아니라 메소드를 가지고 객체를 생성해댄다. 
		```
		인터페이스 변수 = 람다식; 
		```
	
	- 람다식은 인터페이스의 익명 구현 객체를 생성하는데, 인터페이스는 직접 객체화 할 수 없고, 실제 구현을 해야하는데 람다식이 이를 대신할 수 있다. 때문에 람다식이 대입될 인터페이스를 람다식의 **target type** 이라고 부른다.
	
-  함수적 인터페이스 (@FuntionalInteface)
	모든 인터페이스를 람다식의 타겟 타입으로 사용할 수 없다. 람다식이 하나의 메소드를 정의하기 때문에 두 개 이상의 추상 메소드가 선언된 인터페이스는 람다식을 이용해서 구현 객체를 생성할 수 없다.

	즉 하나의 추상메서드가 선언된 인터페이스만이 람다식의 타겟타입이 될 수 있는데, 이러한 인터페이스를 **함수적 인터페이스** 라고 부른다.
	```java
	@FunctionalInterface
	public interface MyFunctionalInterface{
		public void method():
		public void otherMethod(); // 컴파일 오류 
	}
	```

	두 개 이상의 추상메소드가 선언되지 않도록 컴파일러가 체킹해주는 기능이 있는데, 인터페이스 선언시 @FuntionalInterface 어노테이션을 붙이면 된다.  

- **매개변수와 리턴값이 없는 람다식**
 	```java
	@FunctionalInterface
	public interface MyFunctionalInterface{
		public void method():
	}
	```

	- 인터페이스를 타겟 타입을 갖는 람다식은 다음과 같은 형태로 작성해야 한다. 람다식에서 매개변수가 없는 이유는  method() 가 매개변수를 가지지 않기 때문이다. 
		```java
		MyFunctionalInterface fi = () -> {...}
		```
	
	- 람다식이 대입된 인터페이스의 참조변수는 다음과 같이 method() 를 호출할 수 있다. method() 호출은 람다식의 **중괄호 {}** 를 실행시킨다. 
		```java
		fi.method()
		```  
		
		```java
		public class MyFunctionalInterfaceExample{
			public static void main(String[] args){
				MyFunctionalInterface fi;
				fi = () -> {
					String str = "call1";
					System.out.println(str);
				}
				
				fi.method();
				
				fi = () -> {
					String str = "call2";
					System.out.println(str);
				};
				fi.method();
				
				fi = () -> System.out.println(call3);
				fi.method();	
			}
		}
	   	//실행결과 :  call1
		             call2
			     call3
		```
- **매개변수가 있는 람다식**
	- 다음과 같이 매개변수가 있고, 리턴값이 없는 추상메소드를 가진 함수적 인터페이스가 있다고 보자. 
	 	```java
		@FunctionalInterface
		public interface MyFunctionalInterface{
			public void method(int x):
		}
		```
	- 인터페이스를 타겟 타입을 갖는 람다식은 다음과 같은 형태로 작성해야 한다. 람다식에서 변수가 한개인 이유는 method()가 매개변수 하나만 가지기 때문이다.
		```java
		MyFunctionalInterface fi = (x) -> {...}
		```
	- 람다식에 대입된 인터페이스 참조변수는 다음과 같이 호출할 수 있다. 		
		```java
		public class MyFunctionalInterfaceExample{
			public static void main(String[] args){
				MyFunctionalInterface fi;
				fi = (x) -> {
					int result = x*5;
					System.out.println(result );
				}
				
				fi.method(5);
				
				fi = (x) -> {System.out.println(x*5);};
				fi.method(5);
				
				fi = x -> System.out.println(x*5);
				fi.method(5);	
			}
		}
		//실행결과 :  10
					 10
					 10
		```

- 리턴값이 있는 람다식
	- 매개변수와 리턴값이 있는 추상메소드를 가진 함수적 인터페이스가 있다.
	 	```java
		@FunctionalInterface
		public interface MyFunctionalInterface{
			public void method(int x, int y):
		}
		```
	
	- 인터페이스를 타겟타입으로 갖는 람다식은 다음과 같이 작성한다.
	 	```java
		MyFunctionalInterface fi = (x, y) -> {; return 값;}
		```
	- 중괄호 안에 return 문만 있고, return 문 뒤에 연산이나 메소드 호출이 있을 경우 다음과 같이 작성할 수 있다. 
	 	```java
		MyFunctionalInterface fi = (x, y) -> { return x+y;}
		MyFunctionalInterface fi = (x, y) -> x+y;

		MyFunctionalInterface fi = (x, y) -> { return sum(x+y);}
		MyFunctionalInterface fi = (x, y) -> sum(x+y);
		```
	
		```java
		public class MyFunctionalInterfaceExample{
			public static void main(String[] args){
				MyFunctionalInterface fi;
				fi = (x, y) -> {
					int result = x+y;
					return result;
				}
				
				fi.method(5,6);
				
				fi = (x, y) -> {return x+y;};
				fi.method(5,6);
				
				fi = (x, y) -> sum(x+y);
				fi.method(5,6);	
			}
		}
		//실행결과 :  11
					 11
					 11
		```
- 클래스 멤버와 로컬변수 사용
	- 람다식의 실행블록에서 클래스의 필드와 메소드, 로컬변수를 사용할 수 있다. 클래스 멤버는 제약사항이 없는제 로컬변수는 제약사항이 따른다. 
	 
	- **클래스 맴버의 사용**
	람다식 실행블록에느 클랫의 멤버인 필드와 메소드를 제약사항 없이 사용할수 있다.
		- **다만 this 키워드 사용시 주의가 필요하다.**
			람다식의 this 는 내부적으로 생성되는 익명객체의 참조가 아니라, 람다식을 실행한 객체의 참조 이다. 

			```java
			public class MyFunctionalInterface{
				public void method();
			}
			
			public class UsingThis{
				public int outterField = 10;
				
				class Inner{
					int innerField = 20; 
					void method () {
						MyfunctionalInterface fi = () -> {
							System.out.println("outterField" + outterField )
							System.out.println("outterField" + Usingthis.this.outterField)
							System.out.println("innerField " + innerField )
							System.out.println("innerField " + this.innerField )    
						}
					}
				}
			}
			```

- 로컬 변수 사용
	- 람다식은 메소드 내부에서 주로 작성되기 때문에 로컬 익명 구현 객체를 생성시킨다고 봐야한다. 람다식에서 외부클래스의 필드나 메소드는 제한없이 사용가능하나, 메소드의 매개 변수 또는 로컬 변수를 사용하면 이 두변수는  final 특성을 가져야 한다.  
	
	- 매개변수 또는 로컬 변수를 람다식에서 읽는 것은 허용되지만, 람다식 내부 또는 외부에서 변경할 수 없다.
		```java
		public class UsingLocalVar{
			void method(int arg){
				int locarVar = 40; // final 특성을 가짐
				localVar = 41; // final 특성때문에 수정불가
				
				MyfuntionalInterface fi = () ->{
					System.out.println(localVar) // 읽기는 가능
				};
				fi.method();
			}
		}
	```
## 람다식의 삼항연산자 활용
	```java
	public class TestOne {

	    interface Ramda {
		public int method(int x, int y);
	    }

	    interface RamdaIf{
		public int method(int x, int y);
	    }

	    public static void main(String[] args) {
		int a = 10;

		int b = a>5 ? 4 : 5;

		Ramda rd;
		RamdaIf rdIf;

		rdIf = (x, y) ->{
		    int result;
		    if(x+y<3){
			result = 3;
		    }else if(x+y<5) {
			result = 5;
		    }else if(x+y<6){
			result = 6;
		    }else if(x+y<7){
			result = 7;
		    }else {
			result = 0;
		    }
		    return result;
		};


		rd =(x, y ) -> {
		  return x+y<3 ? 3
			  : x+y<5 ? 5
			  : x+y<6 ? 6
			  : x+y<7 ? 7
			  : 0;
		};

		System.out.println("ifv = " + rdIf.method(2,3));
		System.out.println("v = " +rd.method(2,3));

	    }
	}
	```
  ## 표준API 함수적 인터페이스
- 자바에서 제공되는 표준 API에서 한개의 추상 메소드를 가지는 인터페이스들은 모두 람다식을 이용해서 익명 구현 객체로 표현이 가능하다. 
- 예를 들어 스레드 작업을 정의하는 Runnable 인터페이스는 매개변수와 리턴값이 없는 run() 메소드만 존재하기 때문에 다음과 같이 람다식을 이용해서 Runnable 인스턴스를 생성할 수 있다.
```java
	Runnable runnable = () ->{
		for(int i=0;i<10;i++){
			System.out.println(i);
		}
	}
	
	Thread thread = new Thread(runnable);
	thread.start();

```


- 자바 8부터 빈번하게 사용되는 함수적인터페이스는 java.util.function 표준 API 패키지로 제공한다. 이 패키지에서 제공하는 함수적 인터페이스의 목적은 메소드 또는 생성저의 매개 타입으로 사용하여 람다식에 대입할수 있도록 하기 위해서다.
- 자바 8 부터 추가되거나 변경된 API 에서 이 함수적 인터페이스들을 매개 타입으로 사용할 수 있다. 

## 함수적 인터페이스 종류
- Consumer : 매개값은 있고, 리턴값은 없음
- Supplier : 매개값은 없고, 리턴값은 있음
- Function : 매개값 있고, 리턴값 있으며, 주로 매개값을 리턴값으로 매핑(타입변환 목적으로 쓰임)
- Operator : 매개값과 리턴값 둘다 존재, 매개값을 연산하고 결과를 리턴
- Predicate : 매개값은 있고, 리턴값은 boolean, 매매값을 조사하여 true, false 를 리턴

## Consumer or XXXConsumer 
- 리턴값이 없는 accept() 메소드를 가지고 있고, accept() 메소드는 단지 매개값을 소비하는 역할만 한다. 
- 소비한다는 말은 사용만 할 뿐 리턴값이 없다는 뜻이다
	```java
	//변수타입이 String인 t 매개변수를 소비하는 실행문
	Consumer<String> consumer = t -> {t를 소비하는 실행문}

	//변수타입이 각각 String인 t, u 매개변수를 소비하는 실행문 
	BiConsumer<String, String> consumer = (t, u) -> {t, u 소비 실행문

	```
-  예제
	```java
	 Consumer<String> consumer = t->System.out.println(t+"8");
	 consumer.accept("java");

	/*
		실행결과
		java8
	*/

	BiConsumer<Sting,Stirng> bigConsumer = (t,u) -> System.out.println(t+u);
	bigConsumer.accept("java","8");

	/*
		실행결과
		java8
	*/
	``` 

##  Supplier 
- supplier 함수적 인터페이스 특징은 매개변수가 없고 리턴값이 있는 getXXX() 메소드를 가지고 있다. 이 메소드들은 실행 후 호출한 곳으로 데이터를 리턴(공급) 하는 역할을 한다

	```java
	//문자열을 리턴
	Supplier<String> supplier = () -> {,... ; return "문자열"}	
	//int를 리턴
	intSupplier supplier = () - > {return int 값; }
	```
- 예시
	```java
	IntSupplier intSupplier = () -> {
		int num = (int)(Math.random()*6) + 1;
		return num;
	}	

	int num = intSupplier.getAsInt();
	System.out.println("눈의 수 "  + num);
	```
