## 어노테이션 정의
- 메타데이터 어플리케이션이 처리해야할 데이터가 아니라, 컴파일과정과 실행과정에서 코드를 어떻게 컴파일하고 처리할 것인지 알려주는 정보인데, 어노테이션은 메타데이터 역할을 하는 그 자체이다.

## 어노테이션 용도
- 컴파일러에게 코드 문법 에러를 체크하도록 정보를 제공
	- Ex) : @Override : 오버라이드 재정의 된 것임을 컴파일러에게 알려주어 컴파일러가 오버라이드 검사를 하도록 해줌 
- 소프트웨어 개발 툴이나 배치시 코드를 자동으로 생성할 수 있도록 정보를 제공
- 실행시 (런타임시) 특정 기능을 실행하도록 정보를 제공

## 어노테이션 타입 정의와 적용
- 어노테이션 타입을 정의하는 방법은 인터페이스를 정의하는 것과 유사하다.
- 다음과 같이 @interface 를 사용해서 어노테이션을 정의하고, 그 뒤에 사용할 어노테이션 이름이 온다.
```java
public @interface AnnotationName{
	
}
```
- 이렇게 정의한 어노테이션 코드에서 다음과 같이 사용한다.
```java
@AnnotationName
```
- 어노테이션 엘리먼트를 멤버로 가질수 있다. 각 엘리먼트는 타입과 이름으로 구성되며, 디폴트 값을 가질 수 있다.
```java
public @interface AnnotationName{
	타입 elementName() [default 값]
}
```
- 엘리먼트 타입으로 int나 double 과 같은 기본 데이터 타입이나, String, 열거 타입, Class 타입, 그리고 배열타입 사용이 가능하다. 
- 엘리먼트 이름 뒤에는 메소드를 작성하는 것처럼() 를 붙여야한다.
- 예를 들어 String 타입의 엘리먼트와 int 타입의 엘리먼트를 다음과 같이 선언할 수 있다.
```java
public @interface AnnotatioName{
	String elementName1();
	int elementName2() default 5;
}
```
- elementName1 은 디폴트 값이 없기때문에 반드시 값을 기술해야하고, elementName2 는 디폴트 값이 있기 때문에 생략이 가능하다. 
```java
@AnnotationName(elementName1="값", elementName2=3);
@AnnatationName(elementName1="값")
```
- 어노테이션은 기본 엘리먼트인 value 를 가질 수 있다.
```java
public @interface AnnotationName{
	String value();
	int elementName() default 5;
}
```
- Value 엘리먼트를 가진 어노테이션을 코드에서 적용할때에는 다음과 같이 값만 기술할 수 있다.
```java
@AnnotaionName("값")
```
- 만약 value 엘리먼트와 다른 엘리먼트의 값을 동시에 주고 싶다면, 다음과 같이 정상적인 방법으로 지정하면 된다.
```java
@AnnotationName(value="값", elementName2=3)
```
## 어노테이션 적용대상
|ElementType 열거상수 | 적용대상|
|-------|------|
| Type | 클래스, 인터페이스, 열거 타입|
| AnnationType | 어노테이션|
| FIELD | 필드|
| Constructor | 생성자|
|METHOD|메소드|
|LOCAL_VARIABLE | 로컬변수
|PACKAGE | 패키지|
- 어노테이션이 적용될 대상을 지정할때에는 @Target 어노테이션을 사용한다.
- @Target 의 기본 엘리먼트인 value 는 ElementType 배열을 값으로 가진다.
- 이것은 어노테이션이 적용될 대상을 복수개로 지정하기 위해서다.
```java
@Target({ElementType.TYPE, ElementType.FIELD, ElementType.METHOD})
public @interface Annotation{
	
}
```
- 위 어노테이션 적용으로 클래스, 필드 메소드만 적용이 가능하고, 생성자는 적용이 불가하다.
```java
@AnnotationName
public class ClassName{
	@AnnotationName
	private String fieldName;

	@AnnotationName // 적용불가
	public ClassName(){}

	@AnnotationName 
	public void methodName(){}
}
```

## 어노테이션 유지정책
- 어노테이션 정의시 한가지 더 추가해야할 내용은 사용용도에 따라 @AnnotationName 을 어느 범위까지 유지할 것인지 지정해야 한다. 
- 쉽게 설명하면 소스상에만 유지할 건지, 컴파일된 클래스까지 유지할 건지, 런타임시에도 유지할 건지 지정해야한다. 
- 어노테이션 유지정책은 java.lang.annotation.RetentionPolicy 열거 상수로 다음과 같이 정의되어 있다.

|RetentionPolicy 열거상수 | 설명 |
|--------------|--------------|
|SOURCE| 소스에서만 유지, 소스분석시 의미가 있고, 바이트 코드 파일에는 정보가 남지 않음|
|CLASS | 바이크 코드 파일까지 유지, 리플렉션을 이용해서 어노테이션 정보를 얻기 불가|
|RUNTIME | 리플렉션까지 포함|

- 리플렉션이란 런타임시 클레스 메타정보를 얻는 기능을 말한다. 예를들어 클래스가 가지고 있는 필드가 무엇인지, 어떤 생성자와 메소드를 가지고 있는지 적용된 어노테이션을 무엇인지 알아내는 것이 리플렉션이다.
- 리플렉션을 이용해 런타임시 어노테이션 정보를 얻으려면, 어노테이션 유지 정책을 RUNTIME 으로 설정해야한다.
- 코드 자동 생성툴을 개발하지 않는 이상 거의 모든 어노테이션은 런타임 시점에 사용하기 위한 용도로 만들어진다. 
```java
@Target({Element.TYPE, Element.FIELD, Element.MEHTOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface AnnotationName{

}
```
## 런타임시 어노테이션 정보 사용
- 런타임시 어노테이션이 적용되었는지 확인하고, 엘리먼트 값을 이용해서 특정작업을 수행하는 방법이다.
- 어노테이션 자체는 아무런 동작을 가지지 않는 표식일 뿐이지만, 리플렉션을 이용해서 적용여부와 엘리먼트 값을 읽고 적절히 처리할 수 있다.
- 클래스에 적용된 정보를 얻으려면 java.lang.Class 를 이용하면 되지만, 필드, 생성자, 메소드에 적용된 어노테이션 정보를 얻으려면 Class 의 다음 메소드를 통해서 java.lang.reflect 패키지의 Field, Constructor, Method 타입의 배열을 얻어야한다.

|리턴타입 | 메소드명(매개변수)|설명|
|--------|--------|--------|
| Field[] | getFields() | 필드정보를 Field 배열로 리턴|
|Constructor[] | getConstructors() | 생성자정보를 Constructor 배열로 리턴|
| Method[] | getDeclaredMethods() | 메소드정보를 Method 배열로 리턴|

 - Target 은 메소드만 적용
 - Retention 은 런타임시
 - 기본 엘리먼트 value 는 구분선에 사용될 문자이고, number는 반복 출력횟수 이다.
 
```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface PrintAnnotation{
	String value() default "-";
	int number() default 15;
}
```
- 다음은 PrintAnnotation 을 적용한 Service 클래스이다.
```java
public class Service{
	@PrintAnnotation
	public void method1(){
		System.out.println("내용1")
	}

	@PrintAnnotation("*")
	public void method2(){
		System.out.println("내용2")
	}

	@PrintAnnotation(value="#", number=20)
	public void method3(){
		System.out.println("내용3")
	}
}
```
- PrintAnnotationExample 클래스는 리플렉션을 이용해서 Service 클래스에 저용된 어노테이션을 정보를 읽고 엘리먼트 값에 따라, 출력할 문좌와 횟수를 콘솔에 출력한 후 메소드를 호출한다.
```java
public class PrintAnnotationExample{
	public static void main(String[] args){
		Method[] declaredMethods = Service.class.getDelaredMethods();
		// Method 객체를 하나씩 처리
		for(Method method : declareMethods){
			if(method.isAnnotationPresent(PrintAnnotation.class)){
				PrintAnnotation pa = method.getAnnotation(PrintAnnotation.class);

				//메소드 이름출력
				System.out.println(method.getName());

				for(int i=0; i<pa.number();i++){
					System.out.println(pa.value());
				}

				System.out.println();

				try{
					method.invoke(new Service());
				}catch(Exception e){
					System.out.println();
				}
			}
		}
	}
}
```
