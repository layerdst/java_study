

## 스트림
자바8부터 추가된 컬랙션의 저장요소를 하나씩 참조해서 람다식으로 처리할수 있는 반복자이다. 

- 자바 7 이전까지는 List<String> 컬렉션에서 요소를 순차적으로 처리하기 위해서 Iterator 반복자를 다음과 같이 사용해왓다
	```java
	List<String> list =Arrays.asList("홍", "김", "이");
	Iterator<String> iterator = list.iterator();
	while(iterator.hasNext()){
		String name = iterator.next();
		System.out.println(name);
	}
	```

-  이 코드를 Stream 으로 사용해서 변경하면 다음과 같다
	```java
	List<String> list =Arrays.asList("홍", "김", "이");
	Stream<String> stream = list.stream();
	steam.forEach(name -> System.out.println(name));
	```
- 컬렉션 (java.util.Collection) 의 stream() 메소드로 스트림 객체를 얻고 나서 stream.forEach... 메소드를 통해 컬렉션 요소를 하나씩 콘솔에 출력다. forEach() 메소드는 다음과 같이 Consumer 함수적 인터페이스 타입의 매개값(target type) 을 가지므로 컬렉션의 요소를 소비할 코드를 람다식으로 기술할 수 있다
	```java
	void forEach(Consumer<T> action)
	```
- Iterator 를 사용한 코드와 Stream 을 사용한 코드를 비교해보면 Stream 을 사용하는 것이 훨씬 단순해 보인다. 

## 스트림 특징
- Stream 은 Iterator 와 비슷한 역할을 하는 반복자이지만, 
- 람다식으로 요소처리 코드를 제공하는 점
- 병렬처리가 쉽다는 점
	- 외부반복자 (external iterator) 란 개발자가 코드로 직접 컬렉션의 요소를 반복해서 가져오는 코드 패턴을 말한다. index를 이용한 for문 그리고 Iterator 를 이용하는 while 문은 모두 외부 반복자를 이용하는 것이다. 
	- 반면에 내부 반복자(internal iterator) 는 컬렉션 내부에서 요소들을 반복시키고, 개발자는 요소당 처리해야할 코드만 제공하는 코드패턴을 말한다.
	- 내부반복자를 사용해서 얻는 이점은 컬렉션 내부에서 어떻게 요소를 반복시킬 것인가는 컬렉션에게 맡겨두고, 개발자는 요소처리 코드에만 집중할 수 있다는 것이다. 
	- 내부반복자는 요소들의 반복순서를 변경하거나 , 멀티코어 CPU를 최대한 활용하기 위해 요소들을 분배시켜 병렬작업을 할 수 있게 도와주기 때문에 하나씩 처리하면 순차적 외부반복자보다는 효율적으로 요소를 반복시킬수 있다. 
	- Iterator 는 컬렉션의 요소를 가져오는 것에서 부터 처리하는 것까지 모두 개발자가 작성해야 히지만, 스트림은 람다식으로 요소 처리 내용만 전달할뿐 반복은 컬렉션 내부에서 일어난다. 스트림을 이용하면 코드도 간결해지지만, 무엇보다도 요소의 병렬처리가 컬렉션 내부에서 처리되므로 일석이조의 효과를 가져온다.
	- 병렬처리란 한가지 작업을 서브작업으로 나누고 , 서브작업을 분리된 스레드에서 병렬적으로 처리하는 것을 말한다. 병렬 처리 스트림을 이용하면 런타임시 하나의 작업을 서브작업으로 자동으로 나누고, 서브작업의 결과를 자동으로 결합해서 최종 결과물을 생성한다,
	- 예를 들어 컬렉션의 요소 총합을 구할때 순차 처리 스트림은 하나의 스레드가 요소들을 순차적으로 읽어 합을 구하지만, 병렬 처리 스트림을 이용하면 여러개의 스레드가 요소들을 부분적으로 합하고 이 부분합을 최종결합해서 전체합을 생성한다. 
	```java
	import java.util.Arrays;  
	import java.util.List;  

	public class StreamEx {  

	    public static void print(String str){  
		System.out.println(str + " : " + Thread.currentThread().getName());  
	    }  
	    public static void main(String[] args) {  
		      List<String> list = Arrays.asList("홍", "신", "김");  

			  list.stream().forEach(s->StreamEx.print(s));  
			  //list.stream().forEach(StreamEx::print);  
			  /* 결과 
			   홍 : main
			   신 : main
			   김 : main
			  */

			  list.parallelStream().forEach(s -> StreamEx.print(s));  
			  //list.parallelStream().forEach(StreamEx::print);
			  /* 결과 
			   신 : main
			   김 : ForkJoinPool.commonPool-worker-5
			   홍 : ForkJoinPool.commonPool-worker-3
			  */
	    }  
	}
	```			



- 중간처리와 최종처리작업을 수행하는 점에서 많은 차이를 가진다.
	 - 스트림은 컬렉션의 요소에 중간처리와 최종처리를 수행할 수 있는데, 중간처리에서는 맵핑, 필터링, 정렬을 수행하고 
	 
	 - 최종처리에서는 반복, 카운팅, 평균, 총합등의 집계를 수행한다.
	 



	```java
	import java.util.Arrays;  
	import java.util.List;  

	public class StreamMapAndReduce {  
	    public static void main(String[] args) {
		    /*
			학생 객체 요소를 가지는 컬렉션
			*/
		List<Student> studentList = Arrays.asList(  
			new Student("홍길동", 10),  
					new Student("홍길동", 20),  
					new Student("홍길동", 30));  
			/*
			중간처리에서 학생을 점수를 뽑아내고, 
			최종처리에서는 점수의 평균값을 산출한다.
			*/

			double avg = studentList.stream().
						 mapToInt(Student::getScore).
						 average().
						 getAsDouble();

			System.out.println("avg = " + avg);  
		}  
	}  

	class Student{  
		private String name;  
		private int score;  

		public String getName(){  
			return this.name;  
		}  

	    public int getScore(){  
		return this.score;  
		}  

	    public Student(String name, int score){  
		this.name = name;  
		this.score = score;  
	    }  
	}
	```

## 스트림의 pipelines
 - 스트림은 데이터의 필터, 맵핑, 정렬 그룹핑등 중간처리와 합계, 평균, 카운팅, 최대, 최소의 최종처리를 파이프라인(pipeline)으로 해결한다
 
 - 최종 처리를 제외하고는 모두 중간처리 스트림이다.

 - 중간스트림이 생성될때 요소들이 바로 중간처리(filter, mapping, sort) 되는 것이 아니라, 최종처리가 시작되기 전까지 중간처리는 지연(lazy) 된다.

- 예를 들어 회원컬렉션에서 남자만 필터링하는 중간 스트림을 연결하고, 다시 남자의 나이로 맵핑하는 스트림을 연결한 후 최종남자 평균나이를 집계하면 파이프 라인이 형성된다.

## 스트림의 종류
- BaseStream 인터페이스에는 모든 스트림에서 사용할 수 있는  공통 메소드들이 정의되어 있을뿐 코드에서 직접적으로 사용되지 않는다.

- Stream, IntStream, LongStream, DoubleStream 이 직접적으로 이용되는 스트림이다

- Stream은 객체를 처리하는 스트림 나머지는 int, Long Double 을 처리하는 스트림이다.

## 컬렉션에서 스트림 얻기
```java
List<Student> students = Arrays.asList(
    new Student("홍길동", 10),  
    new Student("홍길동", 20),  
    new Student("홍길동", 30));
Stream<Student> stream = students.stream();
stream.forEach(s->System.out.println(s.getName());
```

## 배열에서 스트림 얻기
```java
String[] arr = {"홍", "김"}
Stream<String> strStream = Arrays.stream(arr);
arr.forEach(a->System.out.println(a));

int[] intArr = {1,2,3};
IntStream intStream = Arrays.stream(intArr);
intStream.forEach(a->System.out.println(a));
	
```
## 숫자범위에서 스트림 얻기

```java 
IntStream stream = IntStream.rangeClosed(1,100);
stream.forEach(a->sum += a);
System.out.println(sum);
// 5050

```
## 필터링(distinct(), filter())
- Example
	```java
	List<String> names = Arrays.asList("홍길", "감자", "신의", "감자", "신수");
	```
- distinct() 중복제거
	```java
	names.stream()
		.distinct()
		.forEach(n->System.out.println(n));	
	```
- filter() 필터링
	```java
	names.stream()
		.filter(n->n.startsWith("신"))
		.forEach(n->System.out.println(n));	
	```

## flatMap 메소드
- flatMap 메소드는 요소를 대체하는 복수개의 요소들로 구성된 새로운 스트림을 리턴한다. 
	```java
	List<String> inputList = Arrays.asList("java lamda", "js scope" , "c pointer");
	inputList.stream()
		.flatMap(data -> Arrays.stream(data.split(" ")))
		.forEach(word -> System.out.println(word));
	
		/* 출력
			java
			lamda
			js
			scope
			c
			pointer
		*/
	
	List<String> inputLists2 = Arrays.asList("1 , 2 , 3","4, ,5 ,6");
	inputLists2.stream()
		.flatMapToInt(data -> 
			String[] strArr = data.split(",");
			int[] intArr = new int[strArr.length];
			for(int i = 0; i>strArr.length; i++){
				intArr[i] = Integer.parseInt(strArr[i].trim());																																																																																		
			}
		return Arrays.stream(intArr);
		))
		.forEach(n->System.out.println(n));	   
		
		/* 출력
			1
			2
			3
			4
			5
			6
		*/
	```
## mapXXX()
- 요소를 대체하는 요소로 구성된 새로운 스트림을 리턴한다. 
```java
/*
T -> int 
*/
List<Student> students = Arrays.asList(
							new Student("홍길동", 10),  
							new Student("홍길동", 20),  
							new Student("홍길동", 30));
Stream<Student> stream = students.stream();
	.mapToInt(Student :: getScore)
	.forEach(s - > System.out.println(s));
```
## asDoubleStream(), asLongStream(), boxed()
- asDoubleStream() : IntStream 의 int, LongStream의 long  -> double 요소 타입변환
- asLongStream() : int -> long 요소 타입변환
- boxed : int, long, double -> Integer, Long, Double 박싱
	```java
	int[] intArray = {1, 2, 3, 4, 5}
	IntStream ints = Arrays.stream(intArray);
	ints.asDoubleStream()
		.forEach(d->System.out.println(d));
		/*
			1.0
			2.0
			3.0
			4.0
			5.0
		*/
	
	intStream = Arrays.stream(intArray);
	intStream.boxed()
		.forEach(obj->System.out.println(obj.intValue());
		/*
			1
			2
			3
			4
			5
		*/
	```
## sorted()
- Stream 은 요소가 최종처리 되기 전에 중간단계에서 요소를 정렬해서 최종 처리 순서를 변경 할 수 있다.
- 객체 요소일 경우에 클래스가 Comparable을 구현하지 않으면 sorted() 메소드를 호출했을때 ClassCastException 이 발생하기 때문에 Comparable 을 구현한 요소에서만 sorted() 메소드를 호출해야 한다. 

	```java
	public class Student implements Comparable<Student>{
		private String name;
		private int score;

		public Student(String name, int score){
			this.name = name;
			this.score = score;
		}

		public String getName(){
			return name;
		}

		public int getScore(){
			return score;
		}

		// 점수를 기준으로 오름차순 정렬
		@Override
		public int compareTo(Student o){
			return Integer.compare(score, o.score);
		}
	}
		
	```
- 객체 요소가 Comparable을 구현한 상태에서 **기본 비교 방법과 정반대 방향**으로 정렬하고 싶다면 다음 세가지 방법 중 하나를 선택해서 sorted를 호출 하면 된다. 
	```java
	//순방향	
	sorted()
	sorted((a,b)->a.compareTo(b));
	sorted(Comparator.naturalOrder());
	
	//반대방향
	sorted((a,b)->b.compareTo(a));
	sorted(Comparator.reverseOrder());
	
	//람다식으로 작성(객체요소가 Comparable을 구현하지 않을시)
	sorted((a,b) -> {...})
	```
- 객체요소가 Comparable을 구현하지 않을시 Comparable을 매개값으로 갖는 sorted() 메소드를 사용하면 된다. 
	```java
	sorted((a,b) -> {...})
	```
- sorted 는 다음과 같이 사용이 가능하다.
	```java
	int[] intArray = {1, 2, 3, 4, 5}
	IntStream ints = Arrays.stream(intArray);
	ints.sorted().forEach(n->System.out.println(n))
	/* 출력결과
		1 2 3 4 5		
	*/
	List<Student> students = Arrays.asList(
				new Student("홍길동", 10),  
				new Student("홍길동", 20),  
				new Student("홍길동", 30));
	students.stream()
		.sorted(Comparator.reverseOrder())
		.forEach(s->System.out.println(s.getScore));
	
	/* 출력결과
		30 20 10	
	*/
	
		
	```

## 루핑(peek(), forEach())
- looping은 요소 전체를 반복하는 것을 말하는데, 루핑메소드는 peek, forEach 가 있다. 이 두 메소드는 루핑한다는 기능에서는 동일 하지만, 동작방식이 다르다.

- peek() : 중간처리 단계에서 전체요소를 루핑하면서 추가적인 작업을 하기 위해 사용한다. 최종 처리 메소드가 실행되지 않으면 지연되기 때문에 반드시 최종처리 메소드가 호출되어야 동작한다.
	```java
	// 동작하지 않음
	//필터링후 어떤 요소만 남았는지 확인하기 위해 다음과 같이 peek을 마지막에 호출할 경우 스트림은 동작하지 않는다.
	intStream
		.filter(a->a%2==0)
		.peek(a-> System.out.println(a))
	
	//요소 처리의 최종단계가 합을 구하는 것이라면 peek() 메소드 호출 후 sum() 을 호출해야만 peek() 정상적으로 동작한다.
	intStream
		.filter(a-> a%2 ==0)
		.peek(a->System.out.println(a))
		.sum()
		
	```
- forEach는 최종 처리 메소드이기 때문에 파이프 라인 마지막에 루핑하면서 요소를 하나씩 처리한다. forEach() 는 요소를 소비하는 최종처리 메소드이므로 이후에 sum() 과 같은 다른 최종 메소드를 호출하면 안된다.


## 매칭(allMatch(), anyMatch(), noneMatch())
- 스트림 클래스는 최종처리 단계에서 요소들이 특정조건에 만족하는지 조사할 수 있도록 세가지 매칭 메소드를 제공하고 있다. 
- allMatch() 메소드는 모든 요소가 매개값으로 만족
- anyMatch()  최소한 한개 만족
- noneMatch() 모든 것을 만족
 ```java 
int[] arr = {2, 4, 6}
boolean result = Arrays.stream(intArr)
	.allMatch(a -> a%2);//true


result = Arrays.stream(intArr)
	.anyMatch(a->a%3==0) // true


result = Arrays.stream(intArr)
	.noneMatch(a->a%3==0) // false

 ```
 ## 기본집계(sum(), count(), max(), min(), findFirst()
  - 최종 요소로 활용가능함. 

## Stream과 Optional  결합
- Optional,OptionalDouble , OptionalInt, OptionalLong 클래스는 저장하는 값 타입만 다를 뿐 제공하는 기능은 거의 동일하다. Optional 클래스는 단순히 집계값만 저장하는 것이 아니라, 집계값이 존재하지 않을 경우 디폴트 값을 설정할 수 있고, 집계값을 처리하는 Consumer 도 등록할 수 있다. 

	 \* **Consumer 는 return 값이 없는 매개변수를 소비하는 accept()**
	
	```java
	List<Integer> list = new ArrayList<>();
	double avg = list.stream()
				.mapToInt(Integer :: intValue)
				.average()
				.getAsDouble();
	//NoSuchElementException 발생 :: 요소가 없기 때문!
	```
- Optional 객체를 얻어 다음과 같이 사용이 가능함 - 첫번째 ifPresent 으로 if 문 구성	
	```java
	OptionalDouble optional = list.stream()
		.mapToInt(Integer::intValue)
		.average();
	
	if(optional.isPresent()){
		System.out.println(optional.getAsDouble());
	}else{
		System.out.println("0.0")
	}
	```
 - 두번째 orElse() 메소드로 디폴트 값을 고정.
	 ```java
	double avg = list.stream()
		.mapToInt(Iteger :: intValue)
		.average()
		.orElse(0.0);
	```
 - 세번째 ifPresent 메소드로 값을 이용하는 람다식을 실행
	```java
	double avg = list.stream()
		.mapToInt(Iteger :: intValue)
		.average()
		.ifPresent(a->System.out.println(a));
	```

## 커스텀 집계 reduce()
- 스트림은 기본 집계인 sum(), average(), count(), max() 등을 제공하지만, 프로그램화해서 집계결과물을 만들수 있는 reduce() 메소드도 제공한다.   

- 각 인터페이스는 매개타입으로 xxxOperator, 리턴타입으로 OptionalXXX, int, long, double 을 가지는 reduce() 메소드가 오버로딩 되있다. 
- 스트림에 요소가 전혀 없을 경우 디폴트 값이 identity 매개값이 리턴된다. 
```java
int sum = studentList.stream()
	.map(Student :: getScore)
	.reduce((a,b) -> a+b)
	.get(); // 스트림 요소가 없을 경우 NoSuchElementException 발생

int sum = studentList.stream()
	.map(Student :: getScore)
	.reduce(0, (a,b) -> a+b);
	//스트림요소가 없어도 0이 리턴된다.
```

## collect()
- 스트림 요소들은 필터링 또는 매핑ㅍ한 후 요소들을  수집하는 최종처리 메소드인 collect() 를 제공한다. 이 메소드를 이용하면 필요한 요소만 컬렉션으로 담고, 요소들을 grouping 한 후 reduction 할 수 있다.

	```
	interface : Stream
	method : collect(Collector <T,A,R> collector) 
	return type : R
	```

- 매개값이 Collector 는 어떤 요소를 어떤 컬렉션에 수집할 것 인지를 결정한다. Collector의 타입 파라미터는 T 는 요소이고, A 는 누적기이다. 그리고 R은 요소가 저장될 Collection이다. 
- 풀어서 해석하면 T요소를 A누적기가 R에 저장한다는 의미이다.
- Collector의 구현객체는 다음과 같이 Colletors 클래스의 다양한 정적 메소드를 이용해서 얻을 수 있다. 
- 리턴타입이 Collector<T, ? , List<T>> 에서 누적기가 "?"  로 표기 되어 있는데, 이는 R 에서 T 요소로 저장하는 법을 알고 있어서 A가 필요 없기 때문이다.
- Map과 ConcurrentMap 의 차이점은 Map 은 스레드에 안전하지 않고, ConcurrentMap은 스레드에 안전하다. 즉 멀티스레드에서는 ConcurrentMap을 얻는 것이 좋다

```java
Stream <Student> totalStrem = totalList.Stream();
Stream <Student> mailSream = totalSteam.filters(s->s.getSex() == Student.Sex.MALE);
Collector<Student, ? , List<Student>> collector = Collector.toList();
List<Student> maleList = maleStream.collect(collector);

//아래와 같이 간단히 작성이 가능하다.
List<Student> maleList = totalList.stream()
	.filter(s->s.getSex() == Student.Sex.MALE)
	.collect(Collector.toList());


Stream<Student> totalStream = totalList.stream();
Stream<Student> femaleStream = totalStream.filter(s->s.getSex() == Student.Sex.FEMALE);
Supplier<HashSet<Student>> supplier = HashSet :: new;
Collector<Student, ? , HashSet<Student>> colletor = Collector.toCollector(supplier);
Set<Student> femaleSet = femaleStream.collect(collector);

//위 코드는 아래와 같이 간단히 작성이 가능하다
Set<Student> femaleSet = totalList.stream()
	.filter(s->s.getSex() == student.Sex.FEMALE)
	.collect(Collector.toCollection(HashSet :: new));
```
