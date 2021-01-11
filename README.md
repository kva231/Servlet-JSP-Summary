[루비페이퍼] `"처음 해보는 Servlet &amp; JSP 웹 프로그래밍"` 책을 읽고 내용을 정리한 것입니다.  
생략된 부분들이 있으니 자세한 내용은 책을 구입해서 보시길 바랍니다.

# 목차
- 서블릿 프로그래밍
  - 자바와 웹
    - 자바 기술의 동향과 종류
      - Java SE
      - Java EE
      - Java ME
    - 웹에서의 자바 기술
      - 애플릿(Applet)
      - 서블릿(Servlet)
      - JSP(JavaServer Page)
    - 웹 프로그램의 이해
      - 웹이란?
      - 웹서버와 웹 애ㅐ플리케이션 서버
      - 컨테이너
    - HTTP 프로토콜의 이해
      - HTTP란?
      - HTTP 요청정보
      - HTTP 응답정보
    - 개발 환경 구축
      - JDK 설치하기
      - 톰캣 설치하기
      - 이클립스 설치하기
      - 이클립스 사용하기

<br/>

# 01. 서블릿 프로그래밍
## 1. 자바와 웹
### 1.1. 자바 기술의 동향과 종류
### 1.1.1. Java SE
표준 자바 플랫폼으로 자바의 기본 개발 환경을 제공한다.  
jdk와 jre 두 가지로 나누어 제공한다.
- jdk: 자바 개발 환경. JVM과 컴파일러, 디버거 및 애플리케이션 개발을 위한 도구들이 포함되어 있다.
- jre: 자바 실행 환경. jdk의 일부로서 자바 애플리케이션이 실행되는 데 필요한 최소한의 요건을 제공한다.

### 1.1.2. Java EE
java se에 웹서버 역할을 추가한 것이다.

### 1.1.2. Java ME
모바일 장치나 내장형 장치(휴대전화, 셋톱 박스, PDA)와 같은 소형 장치에서 실행되는 자바 애플리케이션을 기원하기 위해 경량화된 기술들을 지원하는 플랫폼이다.

<br/>

### 1.2. 웹에서의 자바 기술
### 1.2.1. 애플릿(Applet)
동적인 데이터를 처리하기 위해 사용된 방식이다.  
서버에서 클라이언트 쪽으로 실행 파일을 내려받아서 실행된다.  
현재는 HTML5, CSS, JQuery 등의 기술들이 대신해 많이 사용된다.

### 1.2.2. 서블릿(Servlet)
애플릿의 클라이언트 측에 내려받아서 실행되는 약점을 극복한 서버 프로그램이다.  
클라이언트의 요청을 서버에서 처리하고 그 결과를 다시 클라이언트에게 전송하는 자바 프로그램 기술이다.  
주로 컨트롤러에서 사용한다.

### 1.2.3. JSP(Java Server Page)
태그를 이용하여 간단하게 서블릿을 작성할 수 있게 하는 기술이다.  
서블릿으론 웹 문서 만들기 어려워서 등장한 기술이다.  
주로 뷰에서 사용한다.

<br/>

### 1.3. 웹 프로그래밍의 이해
### 1.3.1. 웹이란?
**[1] 웹 문서**  
웹에서 클라이언트가 서버에 정보를 요청하면 응답하는 콘텐츠.  
정적인 웹 문서와 동적인 웹 문서로 구분된다.  
정적인 웹 문서는 요청 시마다 항상 같은 내용을 클라이언트로 전달한다. ex) HTML, GIF, JPG, PDF, PPT 등  
동적인 웹 문서는 요청 시마다 다른 웹 문서의 내용을 클라이언트로 전달한다. 동적인 웹 문서 작성 기술로는 CGI, ASP, PHP, Servlet, JSP 등이 있다.  

**[2] 웹 애플리케이션**  
웹에서 수행되는 애플리케이션.  
수행되는 위치에 따라 웹서버에서 수행되는 기술(Servlet Side)과 브라우저에서 수행되는 기술(Client Side)로 나뉘며 구현하는 방식에 따라 컴파일 방식과 비컴파일 방식(스크립트 방식)으로 나누어진다.
- Client Side: 웹 애플리케이션이 클라이언트에 전송된 다음 클라이언트 자원을 이용하고 클라이언트에 의해 수행되는 경우
- Server Side: 웹 애플리케이션이 서버에서 일차 수행되면서 서버의 자원을 이용하고 그 결과를 클라이언트에 전송하는 경우
- 컴파일 방식: 웹 애플리케이션이 미리 컴파일되고 실행 파일이 만들어진 후 사용되는 경우
- 비컴파일 방식: 요청이 있을 때마다 해석되거나 컴파일이 필요 없는 경우

**[3] 웹 서비스**  
- SOAP 기반 웹 서비스  
  SOA(Service Oriented Architecture) 개념을 실현하기 위한 대표적인 기술.  
  서비스 제공자가 공유 혹은 서비스하려는 자원을 UDDI라는 전역 서비스 저장소에 등록(publich)하여 공개하면 서비스 요청자가 검색한 후 서비스 제공자와 HTTP의 응용 프로토콜인 SOAP를 이용하여 메시지를 주고받는 방식.  
  SOAP 메시지는 송수신 시 헤더와 바디를 인코딩/디코딩해야 하는 단점이 있다.

- RESTful 기반 웹 서비스  
  ROA(Resource Oriented Architecture) 개념을 실현하기 위한 리소스 중심의 표현, 전달, 접근 방식의 기술.  
  SOAP의 단점을 보완. 서비스를 등록하기 위한 저장소가 필요하지 않고 서버와 클라이언트로만 분리되어 있다.  
  리소스 접근을 위해 단순 URI로 표현하며, GET, POST, PUT, DELETE만으로 접근 가능 하다.  
  클라이언트에서 XML, JSON, HTML, 텍스트,, 이미지 등 원하는 형식으로 표현할 수 있다.  

### 1.3.2. 웹 서버와 웹 애플리케이션 서버
**[1] 웹서버**  
웹에서 서버 기능을 수행하는 프로그램.  
클라이언트로부터의 요청을 받는 일과 처리된 결과를 클라이언트로 응답하는 일로 나뉜다.  

**[2] 웹 애플리케이션 서버**  
여러 클라이언트의 요청을 웹서버 단독으로 처리하면 서버의 처리량이 많아지고 속도 및 보안 같은 성능에 문제가 발생한다.  
이러한 이유로 웹서버의 기능을 분리해서 처리하려는 목적으로 웹 애플리케이션 서버를 사용한다.  
클라이언트로부터 요청받은 일과 화면에 표현하는 로직(Presentation Logic)까지만 웹서버에서 담당하고, 다양한 기능을 수행하는 로직(Business Logic)은 컨테이너가 담당하도록 WAS에서 일을 나누어 역할을 분담한다.  
WAS는 크게 웹서버 기능과 컨테이너 기능으로 구성된다.  

### 1.3.3. 컨테이너
서블릿과 JSP와 같은 웹서버 애플리케이션들은 동적 콘텐츠를 생성하는 웹 컴포넌트인데 이러한 웹 컴포넌트를 저장하는 저장소 역할, 메모리 로딩, 객체 생성 및 초기화 등 서블릿의 생명주기를 관리하고 JSP를 서블릿으로 변환하는 기능을 수행하는 프로그램.

**[1] 서블릿 컨테이너**  
클라이언트의 요청에 따라 서블릿을 수행하는 프로그램. 

**[2] JSP 컨테이너**  
JSP를 서블릿으로 변환하는 프로그램. JSP 파일을 서블릿 소스로 변환 및 컴파일까지만 담당하며 변환된 서블릿의 수행은 서블릿 컨테이너가 담당한다.

**※ JSP 처리 과정**  
JSP 수행 요청 -> JSP의 서블릿 객체 존재 O -> 수행  
JSP 수행 요청 -> JSP의 서블릿 객체 존재 X -> JSP의 서블릿 클래스 생성 O -> 객체 생성 -> 수행  
JSP 수행 요청 -> JSP의 서블릿 객체 존재 X -> JSP의 서블릿 클래스 생성 X -> JSP를 서블릿 소스로 변환 -> 컴파일 -> 객체 생성 -> 수행

<br/>

### 1.4. HTTP 프로토콜의 이해
### 1.4.1. HTTP
TCP/IP 4계층에서 애플리케이션 계층에 해당하는 프로토콜로서, 전송 계층에서 TCP를 사용하여 웹 브라우저와 웹 서버 간에 통신하는 프로토콜

**※ OSI 7계층, TCP/IP 4계층**  
- OSI 7계층: Application, Presentation, Session, Transport, Network, Data link, Physical  
- TCP/IP 4계층: Application, Transport, Internet, Network Access Layer  

**HTTP 특징**  
- 무연결(Connectionless): 요청과 응답이 이루어지면 연결을 끊는 방식. 요청이 있을 때마다 독립적으로 연결하여 통신하는 방식이다.  
- 무상태(Stateless): 무연결 특징으로 인해 요청마다 서로 다른 연결로 인식되어 요청간에 정보 공유가 불가한 상태. 상태정보가 유지되지 않는 특성.  
- 요청, 응답(Request, Response): 클라이언트가 서비스를 요청하면 서버는 요청을 받고 처리한 후 응답함으로써 서비스가 완료되는 방식.

### 1.4.2. HTTP 요청정보
크게 헤드와 바디로 이루어져 있다. 헤더의 가장 첫 번째 줄은 요청 줄(Request-Line)이다.  
헤더 정보는 name : content 형식으로 정보들이 들어가며 서비스 처리를 위한 웹 클라이언트와 웹 서버 간에 공유할 정보들이다.

**[1] HTTP 요청방식**  
- GET: 클라이언트에서 서버로 보내는 모든 문자열 정보들이 헤더에 포함되므로 크기에 제한이 있다. 빠른 속도로 요청된다.  
- POST: 정보들이 바디에 포함된다. 데이터 크기에 제한이 없다. 클라이언트에서 보낼 때 인코딩하고 서버에서 디코딩하므로 처리속도가 느리다.  
- PUT: 파일 업로드 할 때 이용한다. POST와 같은 작업을 할 수 있으나 보통 리소스를 생성할 때 POST, 리소스를 수정할 때 PUT 방식을 사용한다.  
- DELETE: 서버의 리소스를 삭제하는 작업을 요청할 때 사용하는 방식이다.

**[2] 요청 URI**  
- URL(Uniform Resource Locator): 네트워크 상에 존재하는 자원을 찾아가기 위한 정보이다. 프로토콜 서버주소 포트번호 서버자원정보(Request URI)로 이루어져 있다.  
- URI(Uniform Resource Identifier): 서버에서 서비스하는 서버의 자원 정보이다. URL에서 포트번호 이후에 나오는 정보.

### 1.4.3. HTTP 응답정보
**[1] 상태 코드**  
클라이언트의 요청에 대한 처리 결과를 의미한다.  

- 1xx: 조건부 응답
- 2xx: 성공
- 3xx: 리다이렉션 완료
- 4xx: 요청 오류
- 5xx: 서버 오류

<br/>

## 1.5. 개발 환경 구축
### 1.5.1. JDK 설치하기
**[1] JDK 내려받기**  
**[2] JDK 설치**  
**[3] Java 환경 변수 설정**

### 1.5.2. 톰캣 설치하기

### 1.5.3. 이클립스 설치하기

### 1.5.4. 이클립스 사용하기
**[1] 웹 프로젝트 생성하기**  
**[2] 웹 문서 실행하기**
