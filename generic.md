## 제네릭
- 데이터 형식에 의존하지 않고, **하나의 값이 여러 다른 데이터 타입**을 가지게 할 수 있는 방법을 말한다.

- 아래와 같은 Box 클래스가 있다고 가정한다. Box 클래스는 필드타입이 Object 인 obj 를 필드변수로 가지고 있어서 모든 종류의 객체를 저장할 수 있다.

```java
public class Box{
	private Object obj;
	public void set(Object obj){
		this.obj = obj;
	}
	public Object getObj{
		return obj;
	}
}
```

- 위 Box 클래스에서 객체를 저장하고 출력하기 위해서는 자동캐스팅, 강제캐스팅 과정이 일어나게 된다.
```java
public class BoxEx{
	public static void main(String[] args){
		Box box = new Box();
		box.set("홍길동");  // String -> Object 자동캐스팅
		String name = (String) box.get(); //Object-> String 강제 캐스팅

		Box fruitBox = new Box();
		fruitBox.set(new Apple()); //Apple -> Object 자동캐스팅
		Apple apple = (Apple) box.get(); // Object -> Apple 강제캐스팅
	}
}
```
- Object 타입을 사용하면 모든 종류의 자바 객체를 저장할 수 있지만, 저장할때 타입변환이 일어나고, 읽어올때도 타입변환이 발생한다. 
- 이러한 타입변환은 전체 프로그램 성능에 좋지못한 결과를 가져올 수 있다. 그래서 모든 종류의 객체를 저장하면서 **타입변환이 일어나지 않도록 제네릭**을 사용하는 것이다.

- 아래의 예시를 통해 제네릭을 활용하여 클래스를 만든다.

```java
public class Box<T>{
	private T t;
	public T set(T t){
		this.t = t;
	}
	public T getObj{
		return t;
	}
}
```
- main 클래스에서의 이전의 String 형의 Box 객체가 생성되면, Box 클래스의  내부는 다음과 같이 자동으로 재구성된다.
```java
Box<String> box = new Box<String>();
```

```java
public class Box<String> {
	private String t;
	private void set(String t){
		this.t = t;
	}

	private String get(){
		return t;
	}
}
```

## 제네릭의 등장
- 제네릭은 java5 부터 도입이 되었는데, 제네릭 타입을 이용함으로써 잘못된 타입이 사용될 수 있는 문제를 컴파일 과정에서 제거할 수 있다.
- 제네릭은 클래스와 인터페이스, 그리고 메소드를 정의할때 타입을 파라미터로 사용할 수 있도록 하며, 타입 파라미터 작성시 구체적인 타입으로 대체되어 다양한 코드를 생성하도록 한다.

## 제네릭의 특징
### 컴파일시 강한 타입 체크를 할 수 있다.
- 자바 컴파일러는 코드에서 잘못 사용된 타입 때문에 발생하는 문제점을 제거하기 위해, **제네릭에 강한 타입체크**를 하는데, 실행시 타입 에러가 나는 것보다 컴파일시 미리 타입을 체크해서 에러를 방지하는 것이 좋다

### 타입변환을 제거한다.
- 위 예제들을 보다 시피비, **비제네릭** 코드는 불필요한 타입 변환을 하기 때문에 프로그램 성능에 악영향을 미치는데 제네릭 코드로 전환하면 타입변환을 할필요 없어 프로그램 성능이 향상된다.

### 제네릭의 타입 파라미터는 참조타입만 가능하다. 
- int, double, char 같은 원시타입은 올수 없으므로, 이를 대신한 Wrapper Type인 Integer, Double 을 사용해야한다.


## 제네릭 타입
### 제네릭 타입 정의
- 제네릭 타입은 타입을 파라미터로 가지는 클래스와 인터페이스를 말한다. 제네릭 타입은  클래스 또는 인터페이스 이름뒤에 <> 부하고 붙고, 사이에 타입 파라미터가 위치한다. 
```java
public class 클래스명<T> {...}
public interface 인터페이스명<T> {...}
```

### 제네릭 타입 명명 관례
- 타입파라미터는 변수명과 동일한 규칙에 따라 작성할 수 있지만, 일반적으로 대문자 알파벳 한글자로 표현한다.

- 다음 한글자의 대문자는 공식문서나 개발자들 사이에서 관례적 의미를 지닌다. (의무는 아니다.)

| 타입  | 설명    |
| ----- | ------- |
| \<T\> | Type    |
| \<E\> | Element |
| \<K\> | Key     |
| \<V\> | Value   |
| \<N\> | Number  |

### 멀티타입 파라미터
- 제네릭 타입은 두 개 이상의 멀티 타입 파라미터를 사용할 수 있는데, 이 경우 각 타입 파라미터를 콤마로 구분한다.
- Product 멀티 파라미터 제네릭 클래스 예시
```java
public class Product<T, M>{
	private T kind;
	private M model;

	public T getKind(){
		return this.kind;
	}

	public M getModel(){
		return this.model;
	}

	public void setKind(T kind){
		this.kind = kind;
	}

	public void setModel(M model){
		this.model = model;
	}
}
```

- 제네릭 객체 생성


```java
public class ProductExample{
	public static void main(String[] args){
		Product<Tv, String> pr1 = new Product<Tv, String>();
		pr1.setKind(new Tv());
		pr1.setModel("스마트TV");
		Tv tv = pr1.getKind();
		String tvModel = pr1.getModel();

		Product<Car, String> pr2= new Product<Car, String>();
		pr2.setKind(new Car());
		pr2.setModel("디젤");
		Car car = pr2.getKind();
		String carModel = pr2.getModel();
	} 

}
```


- 제네릭 타입 변수 선언과 객체생성을 동시에 할때 타입 파라미터 자리에 구체적인 타입을 지정하는데 코드가 중복이 되어 다소 복잡할 수 있다.


- 자바 7 부터 제네릭 타입 파라미터의 중복 기술을 줄이기 위해 다이아몬드 연산자<> 를 제공하는데, 컴파일러는 타입 파라미터 부분에 <> 를 사용하면 타입 파라미터를 유추해서 자동으로 설정해준다.

```java
Product<Tv, String> pr= new Product<>();
```

### 제네릭 메소드
- 매개타입과 리턴타입으로 타입 파라미터를 갖는 메소드를 말한다. 
```java
public <타입파라미터> 리턴타입 메소드명(매개변수...){...}
```
- 다음 boxing() 제네릭 메소드는 <> 기호 안에 타입 파라미터  T를 기술한뒤, 매개변수 타입으로 T를 사용했고, 리턴 타입으로 제네릭 타입 Box\<T\> 을 사용한다.
```java
public <T> Box<T> boxing(T t){...}
```
- 제네릭 메소드는 두 가지 방식으로 호출할 수 있다. 코드에서 타입파라미터의 구체적인 타입을 명시적으로 지정해도 되고, 컴파일러가 매개값의 타입을 보고 구체적인 타입을 추정하도록 할 수 있다.
```java
리턴타입 변수 = <구체적타입> 메소드명(매개값); // 명시적으로 구체적 타입을 지정
리턴타입 변수 = 메소드명(매개값) // 매개값을 보고 구체적 타입을 추정
```

- 다음 코드는 boxing() 메소드를 호출하는 코드이다.
```java
Box<Integer> box = <Integer> boxing(100); // 타입파라미터를 명시적으로 Integer로 지정
Box<Integer> box = boxing(100); // 타입 파라미터를 Integer로 추정
```

- 다음 예제는 Util 클래스에 정적 제네릭 메소드로 compare() 를 정의하고 CompareMethodExample 클래스에서 호출했다. 타입 파라미터는 K와 V로 선언되었는데, compare() 메소드는 두개의 Pair 매개값으로 받아 K와 V 값이 동일한지 검사하고 boolean 값을 리턴한다.

- Util 클래스
```java
public class Util{
	public static <K, V> boolean compare(Pare<K, V> p1, Pair<K, V> p2){
		boolean keyCompare = p1.getKey().equals(p2.getKey());
		boolean valueCompare = p1.getValue().equals(p2.getValue());
		return keyCompare && valueCompare;
	}
}
```
- Pair Class
```java
public class Pair<K, V>{
	private K key;
	private V value;

	public Pair(K key, V value){
		this.key = key;
		this.value = value;
	}

	public void setKey(K key){
		this.key=key
	}
	
	public void setValue(V value){
		this.value=value
	}

	public K getKey(){return key};
	public V getValue(){return value};

}
```
- Main 클래스
```java
public class CompareMehtodExample{
	public static void main(String [] args){
	
		Pair<Integer, String> p1 = new Pair<Integer, String>(1, "사과");
		Pair<Integer, String> p1 = new Pair<Integer, String>(1, "사과");

		boolean result1=Util.compare(p1, p2); // Util.<Integer, String> compare(p1, p2)
		// 결과 true
	
		Pair<String, String> p3 = new Pair<String, String>("user1", "홍길동");
		Pair<String, String> p4 = new Pair<String, String>("user2", "홍길동");
		boolean result2=Util.compare(p3, p4); // Util.<Integer, String> compare(p1, p2)
		//결과 false
	}

}
```

### 제한된 타입 파라미터 - 바운디드
- 타입 파라미터에 구체적인 타입을 제한할 필요가 종종 있다. 예를 들어 숫자를 연산하는 제네릭 메소드는 매개값으로 Number 타입 및 하위 클래스 타입(Byte, Short, Integer, Long, Double) 의 인스턴스만 가져야한다. 이것이 제한된 파라미터(bounded type parameter)가 필요한 이유다.
- 제한된 타입 파라미터를 선언하려면 타입 파라미터 뒤에 extends 키워드를 붙이고 상위 타입을 명시하면 된다.
- 상위 타입은 클래스뿐만 아니라 인터페이스도 가능하다. 다만 **인터페이스라고 해서 implements 를 쓰진 않는다**
```java
public <T extends 상위타입> 리턴타입 메소드(매개변수...){...}
```
- 타입 파라미터에 지정되는 구체적타입은 상위타입이거나 상위 타입 하위 또는 구현 클래스만 가능하다.
- 주의할 점은 중괄호{} 안에서 타입 파라미터 변수로 사용가능한 것은 **상위 타입의 멤버(필드, 메소드) 로 제한된다. 하위 타입에만 있는 필드와 메소드는 사용이 불가**하다.
```java
public class Util{
	public <T extends Number> int compare(T t1, T t2){
		double v1 = t1.doubleValue(); // Number의 doubleValue 메소드사용
		double v2 = t2.doubleValue(); // Number의 doubleValue 메소드사용
		return Double.compare(v1, v2);
	}
}
```

- 바운디드 제네릭 메소드 호출
```java
public class BoundedMain{
	public static void main(String [] args){
		String str  = Util.compare("a", "b"); (x) // a, b Number 타입이 아님
		int result = Util.compare(1,2)  // int -> Interger 자동 boxing
		int result2 = Util.compare(4.5, 3) // double -> Double 자동 boxing
	} 
}
```

