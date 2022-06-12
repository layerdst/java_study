

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
- 컬렉션 (java.util.Collection) 의 stream() 메소드로 스트림 객체를 얻고 나서 stream.forEach... 메소드를 통해 컬렉션 요소를 하나씩 콘솔에 출력하다. forEach() 메소드는 다음과 같이 Consumer 함수적 인터페이스 타입의 매개값(target type) 을 가지므로 컬렉션의 요소를 소비할 코드를 람다식으로 기술할 수 있다
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
