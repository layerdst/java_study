## IO 
- 프로그램에서 데이터를 외부에서 읽고 출력하는 작업은 빈번하게 일어난다. 자바에서 데이터는 스트림을 통해 입출력되는데 입력받을때는 InputStream, 출력할때는 OuputStream 이라 불린다. 
-  자바에서 기본적인 데이터 입출력 API는 java.io 패키지에서 제공하고 있다.
-  java.io 패키지에는 파일 시스템 정보를 얻기위한 File클래스와 데이터를 입출력하기 위한 다양한 입출력 스트림 클래스를 제공한다. 

## JAVA IO API
  | 설명                                                           | java.io 패키지의 주요클래스                          |
  | -------------------------------------------------------------- | ---------------------------------------------------- |
  |                     File           |       파일정보를 얻기 위한 클래스                                                |     
  | Console                                                        | 콘솔로부터 문자를 입출력하기 위한 클래스             |
  | inputStream / OutputStream                                     | 바이트단위 입출력을 위한 최상위 입출력 스트림 클래스 |  
  | File I/O, Data I/O, Object I/O, PrintStream, Buffered I/O            | 바이트단위 입출력을 위한 하위 스트림                |
  | Reader / Writer                                                | 문자 단위 입출력 위한 **최상위** 입출력 스트림       |
  | File R/W , InputStreamReader, OutputStreamWriter, Buffered R/W | 문자 단위 입출력 위한 하위 클래스                    | 
 
 ** I/O InputStream, OutputStream 
 ** FileReader, FileWriter
 
- 스트림 클래스는 크게 두 종류로 구분되는데 바이트 기반 스트림이고, 하나는 문자 기반 스트림이다. 
- 바이트 기반 스트림은 문자, 멀티미디어, 문자등 모든 종류의 데이터를 주고 받을 수 있으나, 문자 기반 스트림은 오로지 문자만 받고 보낼수 있도록 특화 되었다.
- 바이트 기반 스트림과 문자기반 스트림의 최상위 클래스는 다음과 같이 구분된다.
	
  - 바이트 기반 스트림
	
  | 구분          | 입력 스트림    | 출력스트림      |
  | ------------- | -------------- | --------------- |
  | 최상위 클래스 | InputStream    | OutputStream    |
  | 하위 클래스   | XXXInputStream | XXXOutputStream |

	- 문자 기반 스트림
	 
  | 구분          | 입력 스트림 | 출력스트림 |
  | ------------- | ----------- | ---------- |
  | 최상위 클래스 | Reader      | Writer     |
  | 하위클래스    | XXXReader   | XXXXWriter |


## Stream 
- 데이터가 들어온 순서대로 나가는 FIFO 방식의 단뱡향 통신을 말한다.
- 동기 방식이라 작업은 한가지만 가능하다. 
- 단방향 통신이라 입력과 출력  스트림을 별도로 사용해야한다.

### InputStream
- 바이트 기반의 입력 스트림의 최상위 클래스로 추상클래스로서, 모든 바이트 기반 입력 스트림은 이 클래스를 상속받아 만들어 진다. 
- InputStream 에는 read(), close() 메소드가 존재한다.
- read() 메소드
	- 입력스트림으로부터 1바이트를 읽고 4바이트 int 타입으로 리턴한다.
	- 리턴된 4바이트 중 1바이트에만 데이터가 들어가 있다. 
	- 입력스트림으로 부터 바이트를 읽을 수 없다면 read() 메소드는 -1 을 리턴하기에, 이것을 이용하면 마지막 바이트까지 루프를 돌며 한 바이트씩 읽을 수 있다.
		```java
		InputStream is = new FileInputStream("C:\test.jpg");
		int readByte;
		while((readByte=is.read())!= -1){...}
		```

- read(byte[] b)
	- read(byte[] b) 메소드는 입력 스트림으로 부터 주어진 바이트 배열의 길이만큼 바이트를 읽고 배열에 저장한다.
	- 그리고 읽은 바이트 수가 배열의 길이보다 작을 경우 읽은 수 만큼 리턴한다.
	- 예를 들어 입력스트림에서 5개의 바이트가 들어온다면 길이 3인 바이트 배열로 두번 읽을 수 있다.
	- 바이트를 더이상 읽을 수 없다면 -1 을 리턴한다.
	- read(byte[] b) 메소드는 한 번 읽을 때 매개값으로 주어진 바이트 배열 길이만큼 읽기때문에 루핑 횟수가 현저히 줄어든다.

- read(byte[] b , int off, int len) 
	- 입력 스트림으로 부터 len 개의 바이트만큼 읽고, 매개값으로 주어진 바이트 배열 b[off] 로부터 len 까지 저장한다. 
	- 읽은 바이트 수인 len 개를 리턴한다.
	- 실제 읽은 바이트 수가 len 개보다 작을 경우 읽을 수 만큼 리턴한다.
	- read(byte[] b , 2, 3) - > b[2] 부터 3개 인 , b[2], b[3], b[4] 각각 저장.
	- off 가 0 이고, len 을 배열 길이로 준다면 read(byte[] b) 와 동일하다.
- close()
	- InputStream 을 더 이상 사용하지 않을 경우에는 close() 메소드를 호출하여 사용했던 시스템 자원을 풀어준다.

### OutputStream
- 바이트 기반 출력 스트림의 최상위 클래스의 추상클래스이다.
- OutputStream 에는 wirte(), flush(), close() 가 존재한다.

  | 리턴타입 | 메소드                            | 설명                                                                       |
  | -------- | --------------------------------- | -------------------------------------------------------------------------- |
  | void     | write(int b)                      | 출력스트림으로 1바이트를 보낸다.                                           |
  | void     | write(byte[]  b)                  | 출력스트림으로 주어진 바이트 배열 b 의 모든 바이트를 보낸다.               |
  | void     | write(byte[] b, int off, int len) | 출력스트림으로 주어진 바이트 배열 b[off] 로 부터 len까지의 바이트를 보낸다 |
  | void     | flush()                           | 버퍼 잔류하는 모든 바이트를 출력한다.                                      |
  | void     | close()                           | 사용한 시스템 자원을 반납하고 출력스트림을 얻는다.                         |

### Reader
- 문자 기반 입력 스트림의 최상위 클래스로 추상클래스다.
 
  | 리턴타입 | 메소드                              | 설명                                                                                                                                          |
  | -------- | ----------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
  | int      | read()                              | 입력스트림으로부터 한 개의 문자을 읽고 리턴한다.                                                                                              |
  | int      | read(char[] cbuf)                   | 입력스트림으로부터 읽은 문자들을 매개값으로 주어진 문자 배열 cfuf에 저장하고 실제로 읽은 문자수를 리턴한다.                                   |
  | int      | read(char[] cbuf, int off, int len) | 입력스트림으로부터 len개의 문자를 읽고 매개값으로 주어진 문자 cbuf[off] 부터 len 까지 저장한다. 그리고 실제 읽은 문자 수인 len 개를 리턴한다. |
  | void     | close()                             | 사용한 시스템 자원을 반납하고 입력스트림을 닫는다.                                                                                                                                              |

### Writer
- 문자 기반 출력 스트림의 최상위 클래스로 추상클래스다.

  | 리턴타입 | 메소드                               | 설명                                                                  |
  | -------- | ------------------------------------ | --------------------------------------------------------------------- |
  | void     | write(int c)                         | 출력스트림으로 주어진 한 문자늘 보낸다. (c의 끝 2바이트, 한개문자)    |
  | void     | write(char[] cbuf)                   | 출력스트림으로부터 주어진 문자 배열 cbuf의 모든 문자를 보낸다.        |
  | int      | write(char[] cbuf, int off, int len) | 출력스트림으로부터 주어진 문자 cbuf[off] 부터 len 까지 문자를 보낸다. |
  | void     | write(String str)                    | 출력스트림으로 주어진 문자열을 전부보낸다.                            |
  | void     | write(String str, int off, int len)  | 출력스트림으로 주어진 문자열 off 순번부터 len 까지의 문자를 보낸다.   |
  | void     | flush()                              | 버퍼에 잔류하는 모든 문자열을 출력한다.                               |
  | void     | close()                              | 사용한 시스템 자원을 반납하고 출력스트림을 닫는다.                    |

	- write() 예시
    ```java
    Write writer = new FileWriter("C:\test.txt");
    char[] data = "홍길동".toCharArray();
    for(int i =0; i<data.length;i++){
      writer.write(data[i]); // 홍, 길, 동 하나씩 출력
    }
  ```
	
	- write(char[] cbuf) 예시
  
    ```java
    Write writer = new FileWriter("C:\test.txt");
    char[] data = "홍길동".toCharArray();
    writer.write(data); // 홍길동 -> 모두 출력
    ```
	- write(char[] cbuf, int off, int len)
  
    ```java
    Write writer = new FileWriter("C:\test.txt");
    char[] data = "홍길동".toCharArray();
    writer.write(data, 1 , 2); // 길동 -> 출력
    ```


## Buffer
- 프로그램 실행 성능은 입출력이 가장 늦은 장치를 따라간다. CPU와 메모리가 아무리 뛰어나도 하드디스크의 입출력이 늦어지면 프로그램 실행 성능은 하드디스크의 처리 속도에 맞춰진다
- 이 문제에 대해 완전한 해결책은 될수 없지만, 프로그램이 입출력 소스와 직접 작업하지 않고 중간에 메모리 버퍼와 작업함으로써 실행 성능을 향상할 수 있다.
- 예를 들어 프로그램은 하드디스크에 데이터를 보내지 않고, 메모리 버퍼에 데이터를 보냄으로써 쓰기 속도가 항상된다. 버퍼는 데이터가 쌓이기를 기다렸다가 꽉 차게 되면 데이터를 한꺼번에 하드 디스크로 보냄으로서 출력횟수를 줄여준다.
- 자바는  위와 같이 메모리 버퍼를 제공하여 프로그램의 실행 성능을 향상시키는 것들이 있다
- 바이트 기반 스트림에는 BufferedInputStream, BufferedOutputStream, 문자기반 스트림에는 BufferedReader, BufferedWriter 가 있다.

### BufferedInputStream 과 BufferedReader
- BufferedInputStream 은 바이트 입력 스트림에 연결되어 버퍼를 제공해주는 보조스트림이고 BufferedReader 는 문자 입력  스트림에 연결되어 버퍼를 제공해주는 보조스트림이다.
- 둘다 입력 소스로부터 자신의 내부 버퍼크기만큼 데이터를 미리 읽고 버퍼에 저장해둔다. 프로그램은 외부의 입력 소스로부터 직접 읽는 대신 버퍼로 부터 읽음으로써 읽기 성능이 향상된다.
- 두 보조 스트림은 준 입력스트림과 연결되어 8192 내부 버퍼 사이즈를 가지게된다.
- BufferedInputStream 은 8192 바이트가 BufferedReader 는 최대 8192 문자가 저장된다.
	```java
	BufferedInputStream bis = new BufferedInputStream(바이트입력스트림);
	BufferedReader br = new BufferedReader(문자입력스트림);
	```

## Channel 
- 데이터의 양방향 통신으로 Stream과 유사하지만 동작 방식이 다르다.
- input, output을 구분하지 않으며, 기본적으로 buffer를 통해서 입출력을 수행한다. 
- 자바 기본 입출력방식인 stream 은 동기적 특성에 의해 block이 되고, Non-buffer 특징으로 입출력 속도가 느릴수 밖에 없다.
- 자바 4부터는 이 문제를 해결하고자 NIO(New Input Output) 가 java.io 패키지에 포함되어 등장하였다.

## 표준스트림
- 자바는 콘솔로부터 데이터를 입력받을때 System.in 을 사용하고, 콘솔에 데이터를 출력할때 System.out을 사용한다. 에러는 출력할때에 System.err 를 사용한다.

### System.in 필드
- 데이터를 입력받는 System 클래스의 in 정적 필드.
- InputStream 타입의 필드이므로 InputStream 변수로 참조가 가능.
```java
InputStream is = System.in;
```
- 키보드로 어떤 키가 입력되었는지 확인하려면 InputStream 의 read() 메소드로 한 바이트를 읽으면 된다. 리턴된 int 값에는 십진수 아스키 코드가 들어있다.
```java
int asciicode = is.read();
```

- InputStream 의 read() 메소드는 1바이트만 읽기 때문에 1바이트의 아스키 코드로 표현되는 숫자, 영어, 특수문자는 잘 읽을 수 있다.
- 하지만 한글과 같이 2바이트를 필요로 하는 유니코드 read() 메소드로 읽을 수 없다. 
- 키보드로 입력된 한글을 얻기 위해서는 우선 read(byte[] b) 나 read(byte[] b, int off, int len) 메소드로 전체 입력된 내용을 바이트 배열로 받고, 이 배열을 이용해서 String 객체를 생성하면 된다.
```java
byte[] byteData = new byte[15];
int readByteNo = System.in.read(byteData);

String str = new String(byteData, 0, readByteNo-2)

// -2 는 Enter 키에 해당하는 마지막 두 바이트를 제외하기 위해서다.

```


### System.out 필드
- PrintStream 이 OutputStream의 하위 클래스이므로 out 필드를 OutputStream 으로 변환해서 사용할 수 있다.
```java
OutputStream os = System.out;
```
- 콘솔로 1개의 바이트를 출력하려면 OutputStream 의 write(int b) 메소드를 이용하면 된다. 이때 바이트값은 아스키 코드인데, write() 메소드는 아스키 코드를 문자로 콘솔에 출력한다.
```java
byte b = 97;
os.write(b); // a 출력
os.flush();
```
- write(int b) 메소드는 1바이트만 보낼 수 있기 때문에 1바이트로 표현가능한 숫자, 영어, 특수 문자는 출력이 가능하지만, 2바이트로 표현되는 한글은 출력할 수 없다.
- write(byte[] b), write(byte[] b, int off, int len) 메소드로 한글을 출력할 수 있다. 
```java
String name = "홍길동";
byte[] nameBytes = name.getBytes();
os.write(nameBytes);
os.flush();
```

### System.err 필드
- PrintStream 이 OutputStream의 하위 클래스이므로 err 필드를 OutputStream 으로 변환해서 사용할 수 있다.
- 에러 출력을 하는 스트림으로서, 출력 데이터가 만들어지기 전에 생성된다.
```java
System.out.println("out")
System.err.println("err");

//err
//out 출력
```
