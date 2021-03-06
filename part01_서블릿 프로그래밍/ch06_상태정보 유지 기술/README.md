# 6. 상태정보 유지 기술
인터넷 웹 서비스에서는 클라이언트와 서버가 통신할 때 `HTTP` 프로토콜을 사용한다.  
HTTP는 `무연결`과 `무상태`로 동작하는 프로토콜이라 이전 연결 때의 상태정보를 서버가 유지해주지 않는다.  
그러므로 상태정보를 일정 시간 동안 유지해주는 기술이 필요한데, 이러한 기술을 `상태정보 유지 기술`이라 한다.

`클라이언트 측`에 저장하여 유지하는 기술과 `서버 측`에 저장하여 유지하는 기술이 있다.

<br/>

## 6.1. 상태정보 유지
정보를 유지하지 않는 `무상태` 통신 방식은 다수의 사용자를 상대로 하는 인터넷에서 네트워크나 서버의 `오버헤드를 줄일 수 있는 장점`이 있다.
그러나 이전 서비스 처리 결과를 사용해야 할 때는 서버나 클라이언트 측에 이전의 처리 결과를 저장하여 유지하는 추가 작업을 하지 않으면
`지속적인 서비스 처리가 어렵다는 단점`도 있다.

이처럼 클라이언트나 서버에 계속된 요청에서 사용할 수 있도록 저장한 정보들을 `상태정보(State Information)`라고 한다.

<br/>

### 6.1.1. 저장 위치 분류
- **클라이언트 측 저장 기술**  
  서블릿에서 쿠키(Cookie) 기능이 있는 객체
  ```java
  javax.servlet.http.Cookie
  ```

- **서버 측 저장 기술**  
  서블릿에서 서버 측에 상태정보를 저장할 수 있는 객체
  ```java
  javax.servlet.ServletContext
  javax.servlet.http.HttpSession
  javax.servlet.http.HttpServletRequest
  ```

<br/>

### 6.1.2. 유지 기간 분류
클라이언트나 서버 측에 저장된 정보들은 무한정 유지되는 것이 아니라 `기간이 한정`되어 있다.

- **웹 애플리케이션 단위 유지**  
  웹 애플리케이션이 서비스되고 있는 동안 유지
  ```java
  javax.servlet.ServletContext
  ```

- **클라이언트 단위 유지**  
  클라이언트별로 구분해서 상태정보 유지
  ```java
  javax.servlet.http.Cookie
  javax.servlet.http.HttpSession
  ```

- **요청 단위 유지**  
  클라이언트의 서비스 요청 단위로 유지
  ```java
  javax.servlet.http.HttpServletRequest
  ```

<br/>

## 6.2. ServletContext
웹 애플리케이션 단위로 서비스하는 웹서버에서 `서블릿 컨테이너`는 웹 애플리케이션 단위로 `Context`를 생성하여 관리한다.  
이 Context가 `ServletContext` 객체다.

<br/>

### 6.2.1. ServletContext 생성
ServletContext는 서블릿 컨테이너와 통신하기 위해서 사용되는 메소드를 지원하는 인터페이스이다.

서블릿 컨테이너가 시작될 때 웹서버에 등록된 웹 애플리케이션 단위로 하나의 ServletContext 객체가 자동 생성된다.  
그리고 웹 애플리케이션 서비스가 중지될 때 소멸한다.  
즉, `웹 애플리케이션과 생명 주기(life cycle)`가 같다.

웹 애플리케이션 단위로 컨텍스트가 생성되는 이유는 서블릿 컨테이너가 웹 애플리케이션 단위로 `모든 자원을 관리`할 수 있게 하기 위해서이다.
즉, 웹 애플리케이션 내에 있는 모든 서블릿과 JSP 간에 정보를 공유할 수 있고, 서블릿 컨테이너에 대한 정보를 추출할 수 있게 하는
기술이 바로 ServletContext이다.

웹 애플리케이션에 존재하는 서블릿들은 동일한 ServletContext 객체를 사용한다.
- **init() 메소드를 재정의하여 추출하는 방법**
  ```java
  public class Example extends HttpServlet {
    ServletContext sc;
    @Override
    public void init(ServletConfig config) throws ServletException{
      sc = config.getServletContext();
    }
  }
  ```

- **HttpServlet을 통해 추출하는 방법**
  ```java
  public class Example extends HttpServlet {
    public void doGet(..., ...) throws ... {
      ServletContext sc = this.getServletContext();
    }
  }
  ```
  
  상속관계는 ServletConfig - GenericServlet - HttpServlet - Example 이렇게 이루어져 있다.  
  GenericServlet이 ServletConfig의 메소드들을 재정의 했으므로 Example에서 getServletConfig()를 사용할 수 있다.

<br/>

### 6.2.2. ServletContext 변수
ServletContext 객체가 갖고 있는 변수는 동일한 웹 애플리케이션에 속한 모든 페이지에서 사용할 수 있는 글로벌한 변수이다.

**ServletContext 변수 설정**  
**web.xml**
```xml
<context-param>
  <param-name>testName</param-name>
  <param-value>testValue</param-value>
</context-param>

<servlet>
  ...
```

**ServletContext 변수 추출**  
```java
  public class Example extends HttpServlet {
    public void doGet(..., ...) throws ... {
      ServletContext sc = this.getServletContext();
      String value = sc.getInitParameter("testName");
    }
  }
```

**`<context-param>` 태그의 실제 사용 용도**  
웹서버가 사용하는 환경설정 파일은 `web.xml`이지만 서비스 처리를 위해서는 각 페이지 수준의 환경설정 파일들도 읽어들이도록 설정해야 한다.
환경설정 파일들은 서버의 웹 애플리케이션 `서비스 시작과 동시에` 읽어 들여야 한다.

따라서 웹 애플리케이션 서비스 시작과 동시에 생성되는 ServletContext 객체에 `환경설정 파일`들에 대한 정보를 변수로 전달하고,
실제 환경설정을 하는 페이지에서는 ServletContext 객체를 통해 전달받은 변수를 사용해 환경설정 파일을 찾아가 설정 작업을 진행한다.

```xml
<context-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>
    /WEB-INF/presentation_layer.xml,
    /WEB-INF/service_layer.xml,
    /WEB-INF/persistent.xml,
  </param-value>
</context-param>
```

환경설정 파일들을 읽어 들여 처리하는 객체의 값을 추출한 후 콤마(,)를 구분자로 문자열을 나눈 다음 각 파일에 대한 정보를 얻어서 처리한다.

<br/>

### 6.2.3. 서버 정보 추출
웹 애플리케이션 단위로 만들어진 ServletContext 객체를 통해 웹 애플리케이션에 관한 정보를 추출할 수 있다.

```java
public class Example extends HttpServlet {
  public void doGet(..., ...) throws ... {
    ServletContext sc = this.getServletContext();
    
    String contextPath = sc.getContextPath(); // 웹 애플리케이션 경로
    String servletContextName = sc.getServletContextName(); // 웹 애플리케이션 이름
  }
}
```

getContextPath()는 웹 애플리케이션을 찾아가기 위해 사용하는 경로를 반환한다.  
getServletContextName()은 web.xml에 `<display-name>`와 `</display-name>`사이에 지정되어 있는 웹 애플리케이션 이름을 반환한다.

<br/>

### 6.2.4. 웹 애플리케이션 단위 정보 공유
- **void setAttribute(String name, Object value)**  
  웹 애플리케이션 범위에서 공유할 데이터를 ServletContext 객체에 `등록`하는 메소드이다.  
  첫 번째 인자는 등록하는 `데이터의 이름`, 두 번째 인자는 공유하기 위해 등록하는 `데이터`이다.
  ```java
  // ShareObject 클래스가 있다고 가정하자.
  ShareObject obj = new ShareObject();
  servletContext.setAttribute("data", obj);
  ```
  
- **Object getAttribute(String name)**  
  ServletContext 객체에 등록한 데이터를 `추출`하는 메소드이다.  
  인자값으로 찾으려는 데이터의 등록된 이름을 지정하면 동일한 이름으로 등록된 데이터를 찾아서 값을 반환한다.  
  `반환 값이 Object`이니 데이터를 추출한 다음에는 항상 원래 데이터 타입으로 `캐스팅`해야 한다.
  ```java
  ShareObject obj = (ShareObject) servletContext.getAttribute("data");
  ```

- **void removeAttribute(String name)**  
  ServletContext 객체에 등록한 데이터를 `삭제`한다.  
  인자값으로 삭제할 데이터의 등록된 이름을 지정한다.
  ```java
  servletContext.removeAttribute("data");
  ```

<br/>

## 6.3. 쿠키(Cookie)
**쿠키? or 세션?**  
- 상태정보의 유지 기간이 브라우저가 종료될 때까지인지의 여부
- 유지하려는 정보의 저장 위치(서버, 클라이언트)
- 유지하려는 정보가 공개되어도 되는지의 여부

**쿠키와 세션의 차이점**  
구분 | 쿠키 | 세션
|:---:|:---:|:---:|
저장 위치 | 클라이언트 | 서버
저장 데이터 타입 | 텍스트 | 객체
저장 데이터 크기 | 제한 있음 | 서버에서 수용할 수 있는 만큼

<br/>

### 6.3.1. 쿠키 속성
`쿠키`란, 서버가 클리이언트에 저장하는 정보로서 클라이언트 쪽에 필요한 정보를 저장해놓고 필요할 때 추출하는 것을 지원하는 기술이다.
클라이언트와의 연결이 끊어져도 클라이언트마다 개별적으로 상태정보를 유지하고자 할 때 쿠키 기술을 활용할 수 있다.

클라이언트에 저장된 쿠키 정보는 이후 다시 서버에 방문할 때 자동으로 요청정보의 헤더 안에 포함되어 전달된다.  
쿠키는 `name`과 `value`로 구성된 정보이다.

쿠키는 브라우저에 `저장되는 용량에 한계`가 있고, 클라이언트에 저장된 쿠키 정보를 클라이언트가 직접 점검해볼 수 있어  
`보안상 문제`가 있다.

<br/>

### 6.3.2. 쿠키 생성
- **쿠키 생성: Cookie(String name, String value)**  
  ```java
  javax.servlet.http.Cookie
  ```
  첫 번째 인자는 쿠키의 `이름`, 두 번째 인자는 쿠키의 `값`이다.

- **쿠키 유효 시간 설정: setMaxAge(int expiry)**  
  - **0**: 쿠키 삭제
  - **음수**: 기본값. 브라우저 종료 시, 쿠키 자동 삭제
  - **양수**: 쿠키의 유효 시간의 초(second)

- **쿠키 경로 설정: setPath(String uri)**  
  서버의 모든 요청에 대하여 쿠키가 서버 쪽으로 전송되는 것이 아니라, `특정 경로`의 요청에서만 쿠키를 전송하고자 할 때 사용한다.
  경로를 지정하면 지정된 경로와 그것의 하위 경로의 요청에 대해서만 클라이언트로부터 쿠키가 전송된다.

- **쿠키 도메인 설정: setDomain(String domain)**  
  쿠키의 도메인을 설정하면 하나의 서버에서 클라이언트로 전송된 쿠키를 `다른 서버`에서 읽어 들여 사용할 수 있다.
  
  "www.edu.com"으로 지정하지 않고 ".edu.com"처럼 지정하면 "it.edu.com"이나 "math.edu.com"처럼  
  ".edu.com"이 포함된 모든 도메인 서버에서 쿠키를 읽어 들일 수 있다.

- **쿠키 전송: addCookie(Cookie cookie)**  
  생성된 쿠키를 클라이언트로 보낼 때는 HttpServletResponse 객체의 addCookie() 메소드를 이용한다.  

```java
Cookie c = new Cookie("id", "guest");   // 쿠키 생성

c.setMaxAge(60 * 60 * 24 * 10);   // 쿠키 유효 시간 설정. 10일
c.setPath("/");   // 쿠키 경로 설정.

response.addCookie(c);  // 쿠키 전송.
```

<br/>

### 6.3.3. 쿠키 추출
- **쿠키 추출: Cookie[] getCookies()**  
  클라이언트로 전송된 쿠키를 서버 쪽에서 읽어 들이려면 HttpServletRequest 객체의 getCookie() 메소드를 이용한다.

- **쿠키 검색: String getName()**  
  쿠키를 검색할 때는 쿠키의 이름으로 검색하며, 쿠키의 이름만 추출할 때는 Cookie 객체의 getName() 메소드를 사용한다.

- **쿠키 값 추출: String getValue()**  
  쿠키 검색을 통해 찾은 쿠키의 값을 추출하며, 쿠키의 값을 추출할 때는 Cookie 객체의 getValue() 메소드를 사용한다.

```java
Cookie[] list = request.getCookies();
for (int i = 0; list != null && i < list.length; i++) {
  String name = list[i].getName();
  String value = list[i].getValue();
}
```

<br/>

## 6.4. 세션(Seesion)
HTTP 기반으로 동작하는 클라이언트가 서버에 정보를 요청할 때 생성되는 `"상태정보"`를 세션이라 한다.

HttpSession 객체는 서버에 생성되며, 클라이언트에는 세션 ID가 쿠키 기술로 저장되어 각 클라이언트에 대하여  
생성되는 HttpSession 객체를 클라이언트마다 개별적으로 유지 및 관리한다.

**세션 트래킹(session tracking)**  
클라이언트마다 상태정보를 일정 시간 동안 개별적으로 유지하여 사용하는 기술
1. 이전에 클라이언트를 위해 생성된 세션이 이미 존재하면 `존재하는 세션`을 추출하고 아니면 `새로 생성`한다.  
  세션이 새로 생성될 때는 `고유한 ID`가 하나 부여되며, 이 ID는 클라이언트에 `쿠키` 기술로 저장된다.  
  
2. 유지하고자 하는 정보를 저장할 목적의 객체를 생성하여 `세션에 등록`한다.  

3. 클라이언트가 요청을 전달할 때마다 세션에 등록된 정보를 담고 있는 `객체를 추출`하여 원하는 기능에 사용한다.  

4. 세션이 더 이상 필요없는 시점에서 `세션을 삭제`한다.

<br/>

### 6.4.1. HttpSession 생성
- **HttpServletRequest의 getSession()**  
  클라이언트가 갖고 있는 `세션 ID와 동일한 세션 객체`를 찾아서 주솟값을 반환한다.  
  만일 세션이 존재하지 않으면 새로운 HttpSession `객체를 생성`하여 반환한다.

- **HttpServletRequest의 getSession(boolean create)**  
  클라이언트가 갖고 있는 `세션 ID와 동일한 세션 객체`를 찾아서 주솟값을 반환한다.  
  만일 세션이 존재하지 않으면 인자값의 값이 true인지 false인지에 따라 다르게 동작한다.
  - **true**: 새로운 HttpSession 객체를 생성하여 반환한다.
  - **false**: 새로운 HttpSession 객체를 생성하지 않고 `null`을 반환한다.

<br/>

### 6.4.2. HttpSession 메소드
- **public Object getAttribute(String name)**  
  HttpSession 객체에 등록된 정보 중 인자값으로 지정된 데이터의 값을 반환한다.

- **public Enumeration`<String>` getAttributeNames()**  
  HttpSession 객체에 등록되어 있는 모든 정보의 이름만 반환한다.

- **public void invalidate()**  
  현재의 세션을 삭제한다.

- **public boolean isNew()**  
  서버 측에서 새로운 HttpSession 객체를 생성한 경우에는 true를, 아니면 false를 반환한다.

- **public void setAttribute(String name, Object value)**  
  HttpSession 객체에 name으로 지정된 이름으로 value 값을 등록한다.

- **public void removeAttribute(String name)**  
  HttpSession 객체에서 name으로 지정된 객체를 삭제한다.

```java
HttpSession session = request.getSession();   // 세션 얻기.
session = request.getSession(false);   // 세션 얻기. 기존 세션이 존재하면 null 반환.
session = request.getSession(true);   // 세션 얻기. getSession()과 동일.

boolean isNew = session.isNew();    // 새로운 세션 여부 파악하기.

session.setAttribute("msg", "메시지");   // 세션 등록하기.
session.removeAttribute("msg");   // session에 등록된 객체 중에서 msg로 지정된 객체 삭제하기.
session.invalidate();   // 세션 삭제하기
```

<br/>

## 6.5. HttpServletRequest
클라이언트가 요청한 페이지가 실행되다가 다른 페이지로 이동하는 것을 `요청 재지정`이라고 한다.  
즉, 클라이언트로부터의 요청에 대하여 서버에 존재하는 다른 자원으로 요청을 재지정하는 것이다.

이때 클라이언트에서는 서버에 보낸 요청을 다른 자원으로 재지정하는 것을 알 수 없다.  
`클라이언트 모르게` 서버에서 요청을 재지정하기 때문이다.

<br/>

### 6.5.1. HttpServletResponse 요청 재지정
HttpServletResponse 객체에서 제공하는 메소드를 사용하여 요청 재지정할 때는 요청을 재지정하는 자원이 현재 자원과
`동일한 웹 애플리케이션에 속하지 않아도` 상관없고, `동일한 서버에 존재하지 않아도` 상관없다.

- **public void sendRedirect(String location)**  
  location에 설정된 자원으로 요청을 재지정한다.

  ```java
  response.sendRedirect("https://www.naver.com");
  ```

<br/>

### 6.5.2. RequestDispatcher 요청 재지정
RequestDispatcher 객체에서 제공하는 메소드를 사용하여 요청 재지정할 때는 요청을 제지정하는 자원이 반드시
`현재 자원과 동일한 웹 애플리케이션`에 있어야만 한다.

**[1] RequestDispatcher 객체 생성**  
인터페이스인 RequestDispatcher 객체를 생성할 때는 다음과 같이 `팩토리 메소드`를 사용한다.
- **ServletContext 객체에서 제공하는 메소드**  
  - RequestDispatcher getNamedDispatcher(String name)
  - RequestDispatcher getRequestDispatcher(String path)
- **ServletRequest 객체에서 제공하는 메소드**
  - RequestDispatcher getRequestDispatcher(String path)

인자값을 path나 name 어떤 것으로 지정하는가만 다를 뿐 대상을 지정하면서 RequestDispatcher 객체를 추출하는 기능은 같다.  
다만, `ServletRequest` 객체의 getRequestDispatcher()에서 path를 지정할 때 절대 경로뿐만 아니라 상대 경로도 가능하지만,  
`ServletContext` 객체의 팩토리 메소드에서는 `절대 경로만 지정`할 수 있다.

<br/>

**[2] forward(ServletRequest request, ServletResponse response)**  
RequestDispatcher 객체의 forward()는 클라이언트의 요청으로 생성되는 HttpServletRequest와 HttpServletResponse 객체를
다른 자원에 전달하고 `수행 제어를 완전히 넘겨서` 다른 자원의 수행 결과를 클라이언트로 응답하는 기능의 메소드이다.

**forward() 메소드의 수행 흐름**  
client -> 요청 -> 요청 페이지 ->  RequestDispatcher의 forward() -> 포워딩 페이지 -> 응답 -> client

```java
RequestDispatcher rd = servletContext.getRequestDispatcher("/forwardingPage");
rd.forward(request, response);
```

<br/>

**[3] include(ServletRequest request, ServletResponse response)**  
RequestDispatcher 객체의 include()는 클라이언트의 요청으로 생성되는 HttpServletRequest와 HttpServletResponse 객체를
다른 자원에 전달하고 수행한 다음, 그 결과를 클라이언트에서 요청한 `서블릿 내에 포함하여` 클라이언트로 응답하는 기능의 메소드이다.

**include() 메소드의 수행 흐름**  
client -> 요청 -> 요청 페이지 -> RequestDispatcher의 include() -> include 페이지 -> 돌아옴 -> 요청 페이지 -> 응답 -> client

```java
RequestDispatcher rd = servletContext.getRequestDispatcher("/includePage");
rd.include(request, response);
```

<br/>

**[4] Request 단위 정보 공유**  
forward()나 include() 메소드를 이용해 다른 페이지로 이동할 때는 현재 페이지가 사용하는 HttpServletRequest와 HttpServletResponse 객체를
`그대로 전달`하면서 이동하므로 이전 페이지나 이동한 페이지나 `같은 객체`를 사용한다. 그래서 한 번의 요청으로 실행된 페이지끼리
정보를 공유하고자 할 때 `HttpServletRequest를 통해 공유`할 수 있다.

```java
// page1
request.setAttribute("nowPage", "page1");

RequestDispatcher rd = servletContext.getRequestDispatcher("page2");
rd.forward(request, response);
```
```java
//page2
String nowPage = (String) request.getAttribute("nowPage");
```
