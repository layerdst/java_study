## 인터페이스 역할
- 인터페이스는 객체의 사용방법을 정의한 타입으로서, 개발 코드와 객체가 서로 통신하는 접점 역할을 한다.
- 개발코드는 객체 내부구조를 알필요가 없고, 인터페이스 메소드만 알고 있으면 된다
- 인터페이스는 개발 코드를 수정하지 않고, 사용하는 객체를 변경할 수 있도록 하기 위함이다.
- 인터페이스는 하나의 객체가 아니라, 여러객체들과 사용이 가능하므로 어떤 객체를 사용하느냐에 따라 실행내용과 리턴값이 다를수 있다.

## 인터페이스 선언
```java
접근제한자 interface 인터페이스명
```
- 인터페이스는 상수와 메소드만을 구성멤버를 가진다.
- 인터페이스는 객체를 생성할 수 없기 때문에 생성자를 가질수 없다
- 자바 7 이전에는 인터페이스의 메소드는 실행 블록이 없는 추상메소드로만 선언이 가능했지만, 자바8 부터는 디폴드 메소드와 정적메소도 선언이 가능하다.
```java
interface 인터페이스명{
	타입 상수명 = 값;
	타입 메소드명(매개변수 ... );
	default 타입 메소드명(매개변수... );
	static 타입 메소드명(매개변수...); 
}
```
- Constant Field
	- 인터페이스는 객체 사용 설명서이므로, 런타임시 데이터를 저장할 수 있는 필드를 선언할 수 없다. 
	- 다만 상수 필드는 선언이 가능한데 인터페이스 고정된 값으로 런타임시 데이터를 바꿀수 없다
	- 인터페이스에 선언된 필드는 **public static final** 특성을 갖는다.
	- 반드시 초기값을 대입해야 한다.
	```java
	public interface RemoteControl{
		public int MAX_VOLUME = 10;
		public int MIN_VOLUME = 0;
	}  	
	```
- Abstract Method
	- 추상메소드는 객체가 가지고 있는 메소드를 설명한 것으로 호출할때 어떤 매개값이 필요하고, 리턴 타입이 무엇인지만 알려준다. 실제 실행부구현 객체가 가지고 있다.
	- 추상메소드는 리턴타입, 메소드명, 매개변수만 기술되고 중괄호를 붙이지 않는 메소드를 말한다.
	- 선언된 추상메소드는 모두 public abstract 의 특성을 갖기 때문에 생략하더라도 자동적으로 컴파일 과정에서 붙게 된다.
		```java
		public interface RemoteControl{
			public int MAX_VOLUME = 10;
			public int MIN_VOLUME = 0;
			
			public void turnOn();
			public void turnOff();
			public void setVolume(int v);
		}  	
		```
- Default Method
	- 디폴트 메소드는 인터페이스에 선언되지만, 객체가 가지고 있는 인스턴스 메소드이며, 자바 8에서는 기존 인터페이스 확장을 하면서 새로운 기능을 추가하기 위해 허용하였다.
	- 형태는 클래스 인스턴스 메소드와 동일하나, default 키워드가 리턴타입 앞에 붙으며 암묵적으로 public 을 생략하더라도 컴파일 과정에서 붙게된다.
		```java
		public interface RemoteControl{
			public int MAX_VOLUME = 10;
			public int MIN_VOLUME = 0;
			
			public void turnOn();
			public void turnOff();
			public void setVolume(int v);
			
			default void setMute(boolean mute){
				if(mute){
					System.out.println("무음");
				}else{
					System.out.println("소음");
				}
			}
		}  	
	```
- Static Method
	-  정적메소드 역시 자바 8에서 작성할수 있는데, 디폴트 메소드와 달리 객체가 없어서 인터페이스 만으로 호출이 가능하다.
	- 클래스의 정적메소드와 동일하며, public 특성을 가지기에 암묵적으로 컴파일 과정에서 public 이 붙게 된다.
		```java
		public interface RemoteControl{
			public int MAX_VOLUME = 10;
			public int MIN_VOLUME = 0;
			
			public void turnOn();
			public void turnOff();
			public void setVolume(int v);
			
			default void setMute(boolean mute){
				if(mute){
					System.out.println("무음");
				}else{
					System.out.println("소음");
				}
			}
			
			static void changeBattery(){
				System.out.println("교환");
			}
		}  	
	```
## 인터페이스 구현
- 개발코드가 인터페이스 메소드를 호출하면 인터페이스는 객체의 메소드를 호출한다, 객체는 인터페이스에서 정의된 추상 메소드와 동일한 메소드 이름, 매개타입, 리턴타입을 가진 실체 메소드를 가지고 있어야한다.
- 이러한 객체를 인터페이스의 구현 객체라고 하고, 구현객체를 생성하는 클래스를 구현 클래스라고 한다. 

## 구현클래스
- 클래스 선언부에 implements 키워드를 추가하고 인터페이스 명을 명시해야 한다. 
	```java
	public class Television implements RemoteControl{
		private int volume;
		
		public void turnOn(){
			System.out.println("ON");
		}
		
		public void turnOff(){
			System.out.println("OFF");
		}
		
		public void setVolume(int volume){
			return volume < RemoteControl.MIN_VOLUME ? RemoteControl.MIN_VOLUME 
				: volume > RemoteControl.MAX_VOLUME ? RemoteControl.MAX_VOLUME 
				: volume
		}
	}
	```
	- 구현 클래스에서 모든 메소드들은 기본적으로 public 접근 제한을 갖기 때문에 public 보다 낮은 접근제한으로 작성할 수 없다. 
	- 인터페이스에서 선언된 추상메소드에 대응하는 실제 메소드를 구현 되지 않을 경우 구현클래스는 자동적으로 추상클래스가 되며 abstract 키워드를 추가해줘야 한다.
	```java
	public abstract class Television implements RemoteControl{
		private int volume;
		
		@Override
		public void turnOn(){
			System.out.println("ON");
		}
		
		@Override
		public void turnOff(){
			System.out.println("OFF");
		}
		// 구현하지 않음
		public void setVolume(int volume);
	}
	```
## 구현객체의 선언
- 인터페이스 구현 객체는 인터페이스 변수를 선언하고, 구현객체를 대입해야한다.
- 인터페이스 변수는 참조타입이기 때문에 구현객체가 대입될 경우 구현객체의 번지를 저장한다.
```java
RemoteControl rc;
rc = new Television();
rc = new Audio();
```

## 익명구현 객체
- 구현 클래스를 만들어 사용하는 것이 일반적이고, 클래스를 재사용 할 수 있어서 편리하지만 일회성 구현 객체를 만들기 위해 소스파일을 만들고 클래스를 선언하는 것은 비효율적이다.
- 자바는 소스파일을 만들지 않고 구현객체를 만들수 있는 방법을 제공하는데, 그것이 익명구현 객체이다.
```java
인터페이스 변수 = new 인터페이스(){
	//메소드 선언
}

```java
public class RemoteControlExample{
	public static void main(String[] args){
		RemoteControl rc = new RemoteControl(){
			public void turnOn(){
			//실행문
			}
			
			public void turnOff(){
			//실행문
			}
			
			public void setVolume(int v){
			//실행문
			}
		}
			
	}
}
```

- 모든 객체는 클래스부터 생성되는데, 익명 구현 객체도 예외는 아니다. RemoteControlExample 자바 파일을 컴파일 하면 다음과 같은 클래스 파일이 생성된다.
> RemoteControlExample.java

## 다중인터페이스 구현 클래스
```java
public class SmartTv implements RemoteControl, Searchable{
	RemoteControl 인터페이스
	Searchable 인터페이스 
	... 전부 구현...
}
```

## 인터페이스 사용
```java
public class MyClass{
	// 필드 인젝션
	RemoteControl rc = new Television();
	
	//생성자 인젝션
	MyClass(RemoteControl rc){
		this.rc = rc;
	}
	
	//메소드 인젝션
	void setInjection(){
		RemoteControl rc = new Television();
	}
}
```

## 타입 변환과 다형성
- 하나의 타입에 대입되는 객체에 따라서 실행결과가 다양한 형태로 나오는 성질을 말한다.
-  부모 타입에 어떤 자식 객체를 대입하느냐에 따라 실행결과가 달라지듯, 인터페이스 타입에 어떤 구현 객체를 대입하는냐에 따라 실행결과가 달라진다.

## 자동타입 변환
```java
인터페이스 변수 = 구현객체;
```

## 필드의 다형성
	
```java
public interface Tire{
	public void roll();
}

public class Hankook implements Tire{
	@Override
	public void roll(){
		System.out.println("hankook");
	}
}

public class Kumho implements Tire(){
	@Override
	public void roll(){
		System.out.println("Kumho");
	}
}

public class Car{
	Tire frontTire = new Hankook();
	Tire backTire = new Hankook();

	void run(){
		frontTire.roll();
		backTire.roll();
	}
}

//main
Car myCar = new Car();
myCar.frontTire = new Kumho();
myCar.run();

// Kumho, hankook 
```

## 배열로 인터페이스 관리

```java
Tire[] tire = {
	new Hankook(),
	new Hankook(),
	new Hankook()
}

tire[1] = new Kumho();

void run(){
	for(Tire tire : tires){
		tire.roll();
	}
}
```

## 매개변수 다형성
- 자동 타입 변환은 필드값을 대입할때도 발생하지만, 메소드를 호출할때도 발생한다.. 
- 매개변수를 부모타입으로 선언하고 호출할 때에는 자식 객체를 대입하여 호출할 수 있다.
	```java
	public class Driver{
		public void drive(Vehicle v){
			vehicle.run();
		}
	}
	
	public interface Vehicle{
		public void run();
	}

	public class Bus implements Vehicle{
		@Override
		public void run(){
			System.out.println("B");
		}
	}

	public class Taxi implements Vehicle{
		@Override
		public void run(){
			System.out.println("T");
		}
	}
	
	
	//main
	Driver d = new Driver();
	
	Vehicle B = new Bus();
	Vehicle T = new Taxi();
	
	d.driver(B); // B
	d.driver(T); // T
	

	```

