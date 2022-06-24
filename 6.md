## 상속
- 부모클래스가 자식 클래스에게 물려주는 행위이며, 자식은 상속을 통해서 부모클래스에 명시된 필드나 메소드를 이용가능하다.
- 상속은 부모클래스를 이용해 새로운 클래스를 만들기 때문에 코드의 중복을 줄여줄수 있다. 
```java
public class A{
	int field;
	void method(){}
}

//A 클래스를 상속받은 B 클래스
public class B extends A{
	String field2;
	void method2(){} 
}

B b = new B();
b.field= 10;
b.method();

b.field2="홍길동";
b.method2();
```

## 클래스 상속 특징
- 상속은 부모가 자식을 선택해서 물려주지만, 프로그램에서는 자식이 부모를 선택한다
- 자바는 다중상속을 허용하지 않는다.

##  생성자 호출
- 자식 클래스의 생성자 호출시 **부모객체가 먼저 생성되고 자식객체**가 그 다음에 생성된다. 
	```java
	A 클래스 생성 후 B 클래스가 생성된다. 
	B b = new B();
	```
- 자식 클래스 생성자 호출시 컴파일러는 다음과 같이 동작한다.
	>  부모 생성자는 자식생성자의 맨 첫줄에서 부모의 기본생성자를 super() 메서드로 호출한다 
	```java
	public B(){
		super(); // 부모의 기본 생성자를 호출한다.
	}
	```
	```mermaid
	graph LR
	A[B class] --> B(B class super method) --> C[A class] 
	```
- 부모클래스에 생성자가 선언되지 않았더라도, 기본 생성자가 만들어지므로 문제없이 실행된다.

- 직접 자식생성자를 선언하고 명시적으로 부모 생성자를 호출하고 싶다면 아래와 같은 코드를 작성해주면, 매개값 타입과 일치하는 부모생성자를 호출한다. 
	 > 만약 일치하는 부모생성자가 없을 경우 컴파일 오류가 발생한다.
	```java
	자식클래스(매개변수, ..){
		super( 매개값,...);
	}

	B(String a, String b){
		super(a, b);
	}

	A(String a, String b){
		....
	}
	```
- 예시 
```java
public class People{
	public String name;
	public String ssn;

	public People(String name, String ssn){
		this.name = name;
		this.ssn=ssn;
	}
	
	public void f1(){
		System.out.println("f1 method() 호출");
	}
}

public class Student extends People{
	public int studentNo;
	public Student(String name, String ssn, int studentNo){
		super(name,ssn);
		this.studentNo = studentNo;
	}
	
	public void getSuperF1(){
		super.f1(); // 부모생성자의 method도 이와같이 호출한다.
	}
}
```

## 메소드 오버라이딩
- override 는 사전적 의미로** 강한 명령 ** 을 의미한다. 자바에서의 오버라이드는 상속받은 클래스의 메소드 명만 위임을 받고 **기존 메서드를 무시**하며 **재정의** 함을 뜻한다.
	> overloading 은 사전적의 의미로 과적을 뜻하며, **동일 메소드에 매개변수에 따라 기능이 추가**되는 것을 의미한다.
	```java
	class P{
		void m();
		void m1();
	}

	class C extends P{
		void m1(); //재정의
		void m2();
	}

	C c = new C();
	c.m(); P 클래스 메서드 실행
	c.m1() : P 클래스의 메소드의 명만 위임을 받고 C의 m1 메소드가 실행 즉 override
	c.m2() : C 클래스 메소드 실행
	```
- 예제 : 원넓이를 구하는 클래스
	```java
	class Calculator{
		double areaCircle(double r){
			return 3.14 * r * r;
		}
	}

	class Computer extends Calculator{
		@Override
		double areaCircle(double r){
			return Math.PI * r * r;
		}
	}

	//main
	Calculator c = new Calculator();
	caculator.areaCircle(r);

	Computer com = new Computer();
	computer.areaCircle(r); // 오버라이드된 메소드 호출 
	```
- @Override 는 생략하여도 되나, 기술할 것을 권고하고 있다. 이를 명시하게 되면 areaCircle() 이 정확히 오버라이딩된 것인지 컴파일러가 체크하기 때문에 개발자의 실수를 줄여준다. 


## final 클래스 및 메소드
- final 키워드는 클래스, 필드, 메소드 선언시 사용할수 있으며, 해당 선언이 **말 그대로 final 최종 상태**이고, 수정할 수 없음을 나타낸다.
	
-  final 클래스 아래와 같이 명시하며, **상속할 수 없다. 대표적으로 String은 final 클래스 중 하나인데, String 클래스는 어떠한 클래스도 상속받지** 못한다. 
	```java
	public final class 클래스{...}
	```
- 또한 수정이나 상속받을 수 없기 때문에 메소드나 생성자 override도 할 수 없다.

## Dynamic Method Dispatch
- 오버로딩의 경우 컴파일시에 어떤 메소드를 사용할지 결정되지만, 오버라이딩은 런타임시 결정된다.
- 오버라이딩의 런타임 호출에 대한 메커니즘을 Dynamic Method Dispatch 라고 부른다.
	```java
	class A{
		void m1(){
			System.out.println("A.m1");
		}
	}
	
	class B extends A{
		@Override
		void m1(){
			System.out.println("B.m1");
		}
	}
	
	class C extends A{
		@Override
		void m1(){
			System.out.println("A.m1");
		}
	}

	```
	- 부모클래스 참조변수는 자식클래스 객체를 지칭할 수 있으며, 이를 업캐스팅이라 한다.
		```java
		A SuperASubB = new B();
		
		//부모클래스 참조변수 : A
		//자식클래스 객체 : B
		```

	-  오버라이딩된 메소드는 상위 클래스 참조를 통해 호출될때, 참조되는 객체 유형에 따라 런타임시 메서드 버전이 결정된다 
		```java
		A SuperASubB = new B();
		
		//참조변수 A
		//참조되는 객체 유형 : B
		```
	- 런타임에서 재정의된 메서드 버전을 결정하는 것은 참조되는 개체 유형에 따라 결정된다.
		```java
		A SuperASubC = new C();
		SuperASubC.m1(); // B.m1;
		
		//참조변수 A
		//참조되는 객체 유형 : C
		//사용되는 메소드 : C.m1
		```

## 추상클래스
- 객체를 직접 생성할 수 있는 클래스를 실체클래스라 한다면, 이 클래스들의 공통적인 특성을 추출하여 선언한 클래스를 추상클래스라 한다. 
	```java
	/*
	추상클래스의 선언
	*/
	abstract class Animal{
		....
	}
	
	class Cat extends Animal{
		...
	}
	
	class Dog extends Animal{
		...
	}

	```
- 추상클래스는 실체클래스와 상속관계이며, 객체를 직접 생성할 수 없다.
	```java
	Animal a = new Animal(); // XXXXX 객체 생성 불가
	```


- 추상클래스의 용도
	- 실체클래스들의 공통된 필드와 메소드 이름을 통일하면서 작성시간을 줄인다. 
- 추상클래스의 선언
	```java
	public abstract class 클래스명{
		//필드
		//생성자
		//메소드
	}
	```
- 추상클래스와 실체클래스 응용
	```java
	public abstract class Phone{
		public String owner;
		
		public Phone(String owner){
			this.owner = owner;
		}
		
		public void turnOn(){
			System.out.println("on");
		}
		
		public void turnOff(){
			System.out.println("off");
		}
	}
	
	public class SmartPhone extends Phone{
		public SmartPhone(String owner){
			super(owner);
		}
		
		public void internetSearch(){
			System.out.println(*
		}
	}

	//main
	Phone p = new Phone(); // 불가 추상클래스
	
	SmartPhone s = new SmartPhone();
	s.turnOne();
	s.internetSearch();
	s.turnOff();
	```
	
## 추상메소드 오버라이딩
- 모든 실체들이 가지고 있는 메소드의 실행 내용이 동일하다면 추상 클래스에 작성하는게 좋다.
- 하지만 실행 메소드가 상속 받은 실체 클래스마다 달라야한다면  실체클래스에서 직접 작성해야한다. 
- 이런 경우 추상클래스는 추상메소드를 선언할수 있으며, 추상메소드는 메소드 선언부만 있고 실행내용인 {} 가 없는 메소드를 뜻한다.
	```java
	public abstract 리턴타입 메소드명 (매개변수);
	```
- 추상클래스를 설계할때 하위 클래스가 반드시 실행 내용을 채우도록 강요하고 싶은 메소드가 있을 경우, 해당 메소드를 추상메소드로 선언하면 된다. 
	```java
	public abstract class Animal{
		public abstract vodi sound();
	}
	
	public abstract class Animal{
		public String kind;
		
		public void breathe(){
			System.out.println("breathe");
		}
		
		public abstract void sound();
	}

	public class Dog extends Animal{
		public Dog(){
			this.kind = "포유류";
		}
		
		@Override // 무조건 오버라이딩을 해줘야함. 하지 않으면 컴파일 에러가 발생한다.
		public void sound(){
			System.out.println("멍");
		}
	}
	```
## Object 클래스
- Object 는 자바의 최상위 부모 클래스에 해당한다.
- 클래스를 선언할때 extends 키워드로 다른 클래스를 상속하지 않으면 암시적으로 java.lang.Object 클래스를 상속받게 된다. 따라서 자바의 모든 클래스는 Object 클래스의 자식이거나 자손 클래스이다. 

- 객체비교 equals()
	```java
	public boolean equals(Object obj){...}
	```
	- equals() 메소드의 매개타입은 Object 인데, 이것은 모든 객체가 매개값으로 대입될 수 있다. Object가 최상위 타입이므로 모든 객체는 Object 타입으로 자동 타입 변환할 수 있다.
	- Object 클래스의 equals() 메소드는 비교 연산자는 == 과 동일한 결과를 리턴한다. 두 객체가 동일한 객체라면 true 리턴하고 아니면 false 리턴한다.
		```java
		Object ob1 = new Object();
		Object ob2 = new Object();

		boolean result = ob1.equals(ob2);
		boolean result = (ob1===ob2);
		``` 
	- Object 클래스는 두 객체를 비교시 객체의 번지가 아닌 논리적 동등을 boolean 값으로 리턴한다.
	- Object의 equals() 메소드는 직접 사용되지 않고, 하위 클래스에서 재정의하여 논리적으로 동등 비교할때 이용된다.
		```java
		public class Member{
			public String id;
			public Member(String id){
				this.id = id;
			}
			
			@Override
			public boolean equals(Object obj){
				if( obj instanceof Member){
					Member m = (Member) obj;
					if(id.equals(member.id)){
						return true;
					}
				}
				return false;
			}
		}
		//main
		Member ob1 = new Member("b");
		Member ob2 = new Member("b");
		Member ob3 = new Member("d");
		
		System.out.println(ob1.equals(obj2)); //true
		System.out.println(ob2.equals(obj3)); //false
		```
		
- 객체 해시코드
	- 객체를 식별할 하나의 정수값을 뜻한다. Object 의 hashCode() 메소드는 객체의 메모리 번지를 이용해서 해시코드를 만들어 리턴하기 때문에 객체마다 다른 값을 가지고 있다. 
	- 논리적 동등 비교시 hashCode()를 오버라이딩할 필요성이 있는데, HashSet, HashMap, Hashtable은 다음과 같은 방법으로 두 객체가 동등한지 비교한다.
	- hashCode() 메소드를 실행하여 리턴된 해시코드 값이 같은지 보고, 해시코드 값이 다르면 다른객체로 판단, 같으면 equals() 로 다시 비교하여 true 와 false 를 리턴한다.
	- 다음은 equals() 메소드를 재정의한 것이다. hashCode() 가 재정의되지 않아서 Object hashCode() 가 사용된다.
		```java
		public class Key{
			public int num;
			
			public Key(int num){
				this.num = num;
			}
			
			@Override
			public boolean equals(Object obj){
				if(obj instanceof Key){
					Key compareKey = (Key) obj;
					if(this.num == compareKey.num){
						return true
					}
				}
				return false;
			}
		}
		//main 
		HashMap<Key, String> hashMap = new HashMap<>();  
		hashMap.put(new Key(1), "남");  
		String v = hashMap.get(new Key(1));  
		System.out.println(v);
		// 결과 null
			
		```
	- 이런 경우 HashMap의 식별키로 Key 객체를 사용하면 저장된 값을 찾아오지 못한다.  num이 같더라도, hashCode 메소드에서 리턴하는 해쉬코드가 다르기 때문에 다른 식별키로 인식하게 된다. 
	- 따라서 다음과 같이 재정의한 hashCode() 메소드를 Key 클래스에 추가하면 된다. 
		```java
		public class Key{
			public int num;
			
			public Key(int num){
				this.num = num;
			}
			
			@Override
			public boolean equals(Object obj){
				if(obj instanceof Key){
					Key compareKey = (Key) obj;
					if(this.num == compareKey.num){
						return true
					}
				}
				return false;
			}
			
			@Override  
			public int hashCode(){  
			    return num;  
			}
		}
		//main 
		HashMap<Key, String> hashMap = new HashMap<>();  
		hashMap.put(new Key(1), "남");  
		String v = hashMap.get(new Key(1));  
		System.out.println(v);
		// 결과 남
			
		```
- toString()
	- toString() 메소드는 객체의 문자정보를 리턴하는데, "클래스명@16진수해시코드" 로 구성된 문자 정보를 리턴한다. 
		```java
		Object ob= new Object();
		System.out.println(ob.toString());
		//결과 java.lang.Object@de6cde
		```
	- Object의 toString() 메소드는 별값어치 없는 정보이므로 하위클래스에서 toString() 메소드를 오버라이딩하여 쓰인다. Date 클래스는 toString() 를 실행하면 현재 시스템의 날짜와 시간정보를 리턴한다
		```java
		Date d= new Date();
		System.out.println(d.toString());
		```
- clone()
	- 객체복제는 동일한 값을 가지는 새로운 객체를 생성하는 것을 말한다. 
	- 객체를 복제하는 이유는 원본객체를 보호하기 위해서다. 신뢰하지 않는 영역으로 원본 객체를 넘겨 작업할 경우 원본 객체의 데이터가 훼손될수 있기 때문에 복제된 객체를 만들어 신뢰하지 않는 영역으로 넘기는게 좋다. 
	- 객체를 복제하는 방법은 thin clone 과 deep clone 이 있다. 
		- thin clone : 단순히 필드값을 복사해서 객체를 복제하는 것을 말한다. 
			- 필드가 기본 타입일 경우 복사가 일어나고, 필드가 참조타입일 경우 객체의 번지가 복사 된다.
				```java
				int f1  ->  값 복사
				int[] f2 -> 객체의 번지값이 복사
				```  
			- Object의 clone() 메소드는 자신과 동일한 필드값을 가진 얕은 복제된 객체를 리턴한다. 이 메소드로 객체를 복제하려면 원본 객체는 반드시 java.land.Cloneable 인터페이스를 구현하고 있어야한다. 
			- 메소드 선언이 없음에도 Cloneable 인터페이스를 명시적으로 구현하는 이유는 클래스 설계자가 복제를 허용한다는 의도적 표시가 목적이다. 
			- 클래스 설계자가 복제를 허용하지 않는 Cloneable 인터페이스를 구현하지 않으면 되고, clone 메소드를 호출시 CloneNotSupportedException 이 발생하여 복제가 실패된다.
				```java
				class A implements Cloneable{
					public A getA (){
						A cloned = null;
						try{
							cloned = (A) clone();
						}catch(CloneNotSupportedException e){
							return cloned;
						}
					}
				}
			```
		- deep clone 은  참조하고 있는 객체도 복제하는 것을 말한다. 
			- 깊은 복제를 하려면 Object clone()  메소드를 재정의해서 참조 객체를 복제하는 코드를 직접 작성해야한다. 
			- 다음 예제를 보면 Member 클래스에 int[] 배열과 Car 타입의 필드가 있다. 이 필드들은 모두 참조타입 이므로 깊은 복제 대상이 된다. 
			```java
			public class Member implements Cloneable{
			public String name;
			public int age;
			public int[] score[]; // 얕은복제 대상아님
			public Car car;		// 얕은복제 대상아님
					
			public Member(String name, int age, int[]scores, Car car){		
				this.name = name;
				this.age = age;
				this.scores = scores;
				this.car = car;
			
				@Override
				public Object clone throws CloneNotSupporException{
					Member cloned = (Member)super.clone(); // object clone 호출
					cloned.scores = Arrays.copyOf(this.scores, this.scores.length);
					cloned.car = new Car(this.car.model);
					return cloned;
				}
			}
			``` 
 - finalize()
	 - 참조하지 않는 배열이나 객체는 Garbage Collector 가 힙 영역에서 자동적으로 소멸시킨다. 쓰레기 수집기는 객체를 소멸하기 직전에 마지막으로 객체의 소멸자를 실행시킨다. 
	 - 소멸자는 Object의 finalize() 메소드를 말하는데, 기본적으로 실행내용이 없다.
	 - 소멸되기전 데이터를 저장하고 싶다면 finalize() 오버라이드 하기도 한다. 
