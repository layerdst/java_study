## Enum (열거) 타입 정의
- 요일(월, 화, 수, 목, 금, 토, 일) 이나 계절(봄, 여름, 가을, 겨울) 과 같은 데이터는 한정된 값을 가진다
- 이런 한정된 값을 갖는 데이터 타입이 열거타입(enumeration type) 이다.
## Enum 타입 선언
- 열거타입의 이름은 첫문자는 대문자 나머지는 소문자로 구성하는 것이 관례이다.
- 접근 제한자 다음에 열거 타입을 명시하기 위해  소문자 enum 을 선언해주어야 한다.
```java
public enum 열거타입이름{...}
```
- 열거타입을 선언했다면 열거 상수를 선언한다.
- 열거 상수는 열거 타입의 값으로 사용되는데 관례적으로 모두 대문자로 작성한다.
```java
public enum Week{
	MONDAY,
	TUESDAY,
	WEDNESDAY,
	THURSDAY,
	FRIDAY,
	...
} 
```
- 만약 열거 상수가 여러 단어로 구성될 경우 단어사이에 밑줄로 연결하는 것이 관례이다.
```java
public enum LoginResult{
	LOGIN_SUCCESS,
	LOGIN_FAILED,
	...
} 
```
## 열거타입 변수 
- 열거타입은 하나의 데이터 타입이므로 변수를 선언하고 사용해야 한다.
```java
열거타입 변수;
```
- Week 로 변수를 선언
```java
Week today;
Week reservationDay;
```
-  열거타입 변수를 선언했다면 다음과 같이 열거 상수를 저장할 수 있으며, 단독으로 사용할 수 없고 반드시 열거타입 열거 상수로 사용된다.
```java
열거타입 변수 = 열거타입.열거상수
```
- Week 예제
```java
Week today = Week.SUNDAY;
```
- 열거타입은 참조타입이기 때문에 null 값을 저장할 수 있다.
```java
Week birthday=null;
```
## 자바 API 의 enum 타입 Calendar
- 자바는 컴퓨터의 날짜 및 요일, 시간을 프로그램에서 사용할 수 있도록 하기 위해 Date, Calendar, LocalDateTime, 등의 클래스를 제공하는데, 그중 Calendar 의 열거 타입을 활용하여 값을 가져올 수 있다.
```java
Calendar now = Calendar.getInstance();

int year = now.get(Calendar.YEAR);  
int month = now.get(Calendar.MONTH);  
int day = now.get(Calendar.DAY_OF_MONTH);  
int week = now.get(Calendar.DAY_OF_WEEK);

```
> Calendar 를 활용한 enum 예제
```java
public class EnumWeekExample {  
	public static void main(String[] args) {  
		Week today = null;  

		Calendar cal = Calendar.getInstance();  
		int week = cal.get(Calendar.DAY_OF_WEEK);  

		switch(week){  
			case 1 :  
				today=Week.SUNDAY; break;  
			case 2 :  
				today=Week.MONDAY; break;  
			case 3 :  
				today=Week.TUESDAY; break;  
			case 4 :  
				today=Week.WEDNESDAY; break;  
			case 5 :  
				today=Week.THURSDAY; break;  
			case 6 :  
				today=Week.FRIDAY; break;  
			case 7 :  
				today=Week.SATURDAY;break;  
		}  
		
		if(today==Week.MONDAY){  
			System.out.println("월요일");  
		}  
	}  
}
```
 ## 열거 객체 메소드 
 | 리턴타입 | 메소드 | 설명 |
 |----------|----------|----------|
 |String|name()|열거객체의 문자열|
 |int |ordinal()|열거 객체의 순번을 리턴|
 |int | compareTo()|열거객체를 비교해서 순번 차이를 리턴|
 |열거타입|valueOf(String name) | 주어진 문자열의 열거 객체를 리턴|
 |열거배열|values()|모든 열거 객체들을 배열로 리턴|
- name()
	```java
	Week today = Week.SUNDAY;
	String name = today.name();

	결과 : SUNDAY
	```

- ordinal()
	- 전체 열거 객체 중 몇번째 열거 객체인지 알려준다. 열거 객체 순번은 타입을 정의할때 주어진 순번을 말하는데, 0 부터 시작한다.
		```java
		Week today = Week.SUNDAY;
		String name = today.ordinal();

		결과 : 6
		```

- compareTo() 메소드
	- 메소드 매개값으로 주어진 열거 객체를 기준으로 전후로 몇번재 위치하는 지를 비교한다. 열거 객체가 매개값의 열거 개체보다 순번이 빠르다면 음수, 늦다면 양수가 리턴된다.
		```java
		Week day1 = Week.MONDAY;
		Week day2 = Week.WEDNESDAY;
		
		int result1=day1.compareTo(day2) // -2
		int result2=day2.compareTo(day1) // 2
		```

- valueOf()
	- 매개값으로 주어진 문자열과 동일한 문자열을 가지는 열거 객체를 리턴한다.
	```java
	Week weekDay = Week.valueOf("SATURDAY");
	```

- values() 
	- 열거타입의 모든 열거 객체들을 배열로 만들어 리턴한다.
	```java
	Week[] days = Week.values();
	for (Week day : days) {
		System.out.println(day);  
	}
	
	결과
	MONDAY
	TUESDAY
	WEDNESDAY
	THURSDAY
	FRIDAY
	SATURDAY
	SUNDAY
	```
## EnumSet
- Enum 클래스와 같이 사용할 수 있는 Set Collection을 지칭한다. 
- Collection 과 Set 의 메소드를 거의 대부분 구현이 가능하다.
- 겉으로는 Set으로 동작하나 내부 비트필드를 사용하기 때문에 속도가 빠르다. 
## EnumSet의 특징
- 동일한 Enum  Type 을 가져야하며, Enum Type 외에 다른 값은 포함할 수 없다.
- null 값을 추가할 수 없고, null 값이 있을 경우 NPE에 동작한다.
- 동기화가 필요한 경우 외부에서 동기화를 하여야 한다.
## EnumSet 메소드
- allOf() : enum 에 있는 모든 요소를 포함한 객체를 생성
	```java
	EnumSet<Week> set = EnumSet.allOf(Week.class);
	set.forEach(a->System.out.println(a));
	``` 
- noneOf() : 빈 enum객체를 만든다
	```java
	EnumSet<Week> weeks= EnumSet.allOf(Week.class);
	weeks.forEach(a->System.out.println(a));
	``` 
- range(a, b) : enum 안에서 a 순번 이상  b 이하의 값을 가진 객체를 만든다
	```java
	EnumSet<Week> range = EnumSet.range(Week.THURSDAY, Week.FRIDAY);  
	range.forEach(System.out::println);	
	```
-	complementOf() 값을 제외한 객체를 만든다
	```java
	EnumSet<Week> comp = EnumSet.complementOf(EnumSet.of(Week.THURSDAY, Week.FRIDAY));  
	comp.forEach(System.out::println);
	```
