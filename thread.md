## 동시요청 - 멀티쓰레드
- Thread
	- 어플리케이션 코드를 순차적으로 실행하는 것을 말하는데 자바 메인 메서드 처음 실행하면 main 이라는 Thread가 실행된다.
	- Thread는 한번에 하나의 코드라인만 수행된다.
	- 동시처리가 필요하면 Thread 를 추가로 생성한다.
- 다중요청 - 단일 Thread 일때	
	- 요청시 Thread 가 지연되면 다른 요청들도 중단된다.
- 다중요청 - 다수 Thread 일때
	- 리소스가 허용될때까지 동시 요청이 가능하며, 하나가 지연되어도 나머지 Thread는 정상작동한다
	- 다만 Thread 생성비용이 매우 비싸다. 
	- 요청이 올때마다 쓰레드를 생성하면 응답속도가 늦어진다.
	- 컨텍스트 스위칭 비용이 발생한다.
	- 요청이 많을시 CPU, 메모리, 임계점을 넘어 서버가 다운될수도 있다.
- Thread Pool
	- 작동 순서
		-  WAS 들은 내부에 Thread Pool 을 미리 만들어 놓은 Thread 로 요청이 올 때 대응을 한다.
	
		- 사용된 Thread 는 다시 Thread Pool 에 남겨놓는다. 
		- 많은 요청으로 미리 만들어진 Thread 가 다 사용되었을때는 Thead 대기나 거절 상태로 바뀐다. 
	- 특징 
		- 필요한 쓰레드를 쓰레드 풀에 보관하고 관리한다.
		- 쓰레드 풀에 생성가능한 쓰레드의 최대치를 관리하는데, 톰캣을 최대 200개가 기본 설정이다
	- 장점 
		- 쓰레드가 미리 생성되어 있어, 쓰레드를 생성하고 종료하는 비용이 절약되고 응답시간이 빠르다.
		- 생성가능한 쓰레드의 최대치가 있어 너무 많은 요청이 들어와도 기존 요청은 안전하게 처리 가능하다.
## Thread Pool 성능 관리
- 주요 튜닝 포인트는 max thread 
	- 낮은 max thread  :서버 리소스는 여유롭지만 클라이언트는 금방 지연된다.
	- 높은 max thread : 메모리, 리소스 임계점 초과로 서버 다운된다.
	- 장애가 발생하면 클라우드의 경우 서버를 늘리고 튜닝을 해야한다. 
- 아파치ab, 제이미터, nGrinder 등 성능테스트를 하면서 리소스를  파악한다.
- WAS 는 멀티스레드에 대한 부분은 WAS가 처리되므로, 개발자가 멀티쓰레드 관련 코드를 신경쓰지 않아도 되므로, 싱글 스레드 프로그래밍 하듯 편리하게 소스코드를 개발하면 된다. 
- 다만 싱글톤객체(서블릿, 스프링빈) 은 주의해서 사용한다.

## SSR (Server Side Rendering)
- HTML 최종 결과를 서버에서 만들어서 웹브라우저에 전달
- 정적화면에 사용하며, JSP, 타임리프 뷰템플릿을 이용한다
## CSR(Client Side Rendering)
- HTML 결과를 자바스크립트를 사용해 웹브라우저에서 동적으로 생성해서 적용한다.
- 주로 동적인 화면에 사용되며 웹 환경을 마치 앱처럼 필요한 부분만 변경가능하다.