## 패키지
- 자바에서는 클래스를 체계적으로 관리하기 위해 패키지를 사용한다. 폴더를 만들어 파일을 저장 관리하듯 패키지를 만들어 클래스를 저장 관리한다.
- 패키지는 클래스를 유일하게 만들어주는 식별자 역할을 한다. 동일한 **클래스 이름이다 하더라도, 패키지가 다르면 다른 클래스로 인식**한다.
	```java
	상위패키지.하위패키지.클래스
	com.na.Car
	```
- 예를 들어 Car 클래스가 com.na 패키지에 속해 있다면 Car 클래스의 전체 이름은 com.na.Car 가 된다
- 실제 파일 시스템에서 com\na 폴더에 Car.class가 위치한다.
- 클래스만 따로 복사해서 다른 곳으로 이동하면 클래스는 사용할수 없다.
-  예를 들어 Car 클래스가 com.na 패키지에 소속되어 있을 경우. 파일 시스템을 com\me 로 저장하면 Car 클래스를 사용할 수 없다. 
- 클래스를 이동할경우 패키지 전체를 이동해야한다.

## 패키지 선언
- 패키지는 클래스를 컴파일하는 과정에서 자동으로 생성되는 폴더이다. 컴파일러는 클래스에 포함되어 있는 패키지 선언을 보고 파일 시스템의 폴더로 자동 생성시킨다. 
	```java
	package 상위패키지.하위패키지
	public class ClassName{
		...
	}
	``` 
- 패키지 명명 규칙
	- 숫자로 시작해서는 안되며 _, $ 제외한 특수문자를 사용해서는 안된다.
	- java 로 시작하는 패키지는 자바표준 api 에서만 사용하므로 금지된다.
	- 모두 소문자로 작성하는 것이 관례이다.
## 패키지 선언이 포함된 클래스 컴파일
- 패키지 선언이 포함된 클래스를 명령 프롬프트에서 컴파일 할 경우, 단순히 java ClassName.java 로 컴파일해서 패키지 폴더가  생성되지 않으며, 패키지 폴더가 자동으로 생성되려면 다음과 같이 지정해줘야한다.
	```java
	javac -d . ClassName.java // 현재폴더에서 생성
	javac -d ..\bin ClassName.java//현재 폴더와 같은 위치의 bin폴더에 생성
	javac -d C:\Temp\bin ClassName.java c:\Temp\bin 폴더에 생성
	```
	
- 예시 Application.java
	```java
	package sec12.exam0;
	
	public class Application{
		public static void main(String[] args){
			System.out.println("실행");
		}
	}
	```
- 명령프롬프트
	```java
	C:\Temp> java -d . Application.java
	```
- 컴파일이 성공하면 위 경로에 패키지 폴더들이 생성되고 마지막에 Application.class  가 저장된다.

=============================================
## import
- 다른패키지에 속하는 클래스를 사용하려면 **패키지와 클래스를 모두 기술하는 방법**이나, 이를 축약해 쓸수 있는 import문을 사용하는 것이다.
	```java
	package com.na;
	public class Car{
		com.tire.Tire tire = new com.tire.Tire();
	}
	
	import com.tire.Tire;
	public class Car{
		Tire tire= new Tire();
	}	
	```
==================================================
## 클래스 패스
- JVM 이 프로그램을 실행할때 클래스 파일을 찾는데 기준이 되는 파일 경로이다.
- java 파일을 컴파일 하면 바이트 코드파일인 class 파일로 변환되는데, java runtime(jre)가 class 파일을 찾아 classpath 에 지정된 경로를 사용한다. 
- 이 경로를 지정하는 것이 바로 classpath 이다.
## 클래스 패스로 지정할 수 있는 파일타입
- 디렉토리
- zip
- jar
## 클래스 패스 사용법
- 파일 디렉토리 지정
	- CLASSPATH=/export/home/username
- 현재 작업 디렉토리 지정
	- CLASSPATH =. 
- 다수의 디렉토리 추가 지정
	- CLASSPATH = /export/home/username:/export/home/username/util
- jar 추가지정
	- CLASSPATH = /export/home/username:/export/home/username/util/util.jar

## 패키지가 있는 자바 컴파일
-  /export/home/username/util/Coding.java 파일이 있다.
- Coding.java 파일 내에는 com.util 이라는 패키지를 **임의**로 넣었을때 
	```java
	package com.util 
	```	
- 방법 1 : **X** 
	- CLASSPATH =/export/home/username/util  -> 지정
	- javac Coding.java -> 컴파일하면 해당 폴더에 class 파일이 생긴다. 
	- java Coding
	- 해당 폴더에 class 파일이 생성되었지만 **패키지가 식별**되지 않아 jre가 작동하지 않는다.
- 방법 2 : **O**
	- java 파일내 패키지가 지정되어 있으므로 패키지도 작성해주어야 한다
	- CLASSPATH =/export/home/username/util  -> 지정
	- javac -d Coding.java -> 컴파일시 해당 폴더에 **패키지를 생성**해줌
	- java com.util.Coding

=========================================================
## 접근지시자 or 제한자
- public : 패키지 내외부 모든 클래서에서 접근 가능
- protected : **같은 패키지나 상속관계**에서 접근 가능
- default : 생략가능하며 동일한 패키지 내에서 접근가능하다
- private : 같은 클래스나 내부 멤버
