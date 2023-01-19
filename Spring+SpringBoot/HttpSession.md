# HttpSession

## Java HttpSession이란?
- HttpSession은 Java의 인터페이스이며, 이를 사용하여 세션을 제어할 수 있다.
- 세션은 쿠키의 트래픽 이슈와 쿠키 변경으로 인한 보안 이슈를 해결하기 위해 등장했다.

---

## session
- session은 server와 client간에 반영구적으로 상호작용하는 정보 교환이다.
- **session은 server로 요청하는 client를 구별하기 위해 server에 저장되는 정보이다.**
- session은 client에 저장되는 쿠키와 다르게 **server에 저장**되므로 관리가 용이하고 효율적이며 보안에 강하다.
- server는 client request에 session-id를 생성하여 server와 client 브라우저 메모리에 쿠키로 저장한다.
  - 일반적인 쿠키가 아닌 session 쿠키이며, 인 메모리 또는 임시, 반영구 쿠키로 불린다.
- **session 쿠키는 server가 종료되거나, 유효기간이 만료하거나, client 브라우저가 종료되면 삭제된다.**
- session의 단점은 server resource를 사용하므로 server에 부담을 줄 수 있으며, 로드 밸런싱 시스템에서 session 처리가 쉽지 않다는 것이다.
  - 로드 밸런싱 : server가 처리해야 할 업무 혹은 요청(Load)을 여러 대의 서버로 나누어(Balancing) 처리하는 것

---

## session mechanics
1. client가 server에 resource를 요청한다.
2. **server는 client가 요청한 request-header의 session cookie를 통해 session-id를 확인한다.**
3. session-id가 존재하면, server는 session-id가 유효한지 확인 후 client의 요청을 처리하고 응답한다.
4. session-id가 존재하지 않으면, server는 set-cookie를 통해 **session-id를 생성한 후 response-header에 추가하여** client에 응답한다.
5. 위 4번 항목 처리 후, client는 server에 요청 시, server로부터 응답받은 session-id를 request-header에 추가하여 요청한다.

---

## session 종료 시기
- 타임아웃(timeout)
- session 객체의 `invalidate()` 호출
- 애플리케이션 또는 server의 종료

---

## HttpSession
- HttpSession은 **client request에서 client를 식별하고, 해당 client 정보를 저장하는 방법**을 제공하는 Java의 public interface이다. 
- HttpSession interface를 사용하는 **서블릿 컨테이너(servlet container)는 server와 client간의 session을 제어**한다.
    > - servlet container는 웹 container라고도 불리며, Tomcat, Jetty 등이 대표적이다.
    > - **servlet container와 servlet은 다르다.**
    >> - servlet container는 server에서 servlet 생명 주기(life cycle) 관리, request에 따른 스레드(thread) 생성, 동적 resource(JSP, servlet 등) 생성 등 servlet과 상호 작용하는 web server의 일부이다.
    >> - servlet은 javax.servlet package에 정의된 interface이며, JVM 내에서 실행되는 web application의 작은 조각이다.
- HttpSession Object 생성
  - `{httpServletRequestObject}.getSession()`
    - 기존 session이 있으면 기존 session Object를, 없으면 새로 생성 후 반환
  - `{httpServletRequestObject}.getSession(false)`
    - 기존 session이 있으면 기존  session Object를, 없으면 null 반환

- **Spring은 Servlet Container가 만든 HttpSession을 주입할 뿐, HttpSession을 생성하는 주체는 Servlet Container이다.**
- **SpringeSession을 이용한다면 Servlet Container가 생성한 구현체가 아니라 SpringSession이 생성한 구현체가 될 것이다.**

---

## HttpSession 생성 시기
- Session을 만드는 것 자체가 큰 부담이기 때문에, Session을 사용할 때만 생성한다.
- Spring Web MVC에서는 HttpSession을 주입해야 할 때, **내부적으로 Servlet Container에게 Session을 달라고 한다.**
1. @AutoWired로 주입받는 방식

      ```java
      @RestController
      public class TestController {
        @Autowired
        private HttpSession session;

        @GetMapping("/test")
        public ResponseEntity<String> test() {
            session.setAttribute("test", "test 성공");
            return ResponseEntity.status(HttpStatus.OK).body((String) session.getAttribute("test"));
        }
      }
      ```
    HttpSession을 주입받는다고 선언하는 것만으로는 Servlet Container에게 session을 달라고 요청하지 않는다. `setAttribute` or `getAttribute` 같은 api를 호출하는 시점에 요청/생성한다.  
    <br>
2. 메소드에서 주입받는 방식
    ```java
    @RestController
    public class TestController {

        @GetMapping("/test")
        public ResponseEntity<String> test(HttpSession session) {
            session.setAttribute("test", "test 성공");
            return ResponseEntity.status(HttpStatus.OK).body((String) session.getAttribute("test"));
        }
    }
   ```
   메소드에서 매개변수를 통해 주입받는 방식으로 구현한다면, 선언시에 Servlet Container에게 session을 달라고 요청한다. 따라서 test 메소드를 호출하는 즉시 session이 요청/생성된다.

---

## HttpSession 관련 메소드

- `HttpServletRequest's getSession(true) : HttpSession`
  - 이미 세션이 있다면 그 세션을 돌려주고, 세션이 없으면 새로운 세션을 생성한다.
- `HttpServletRequest's getSession(false) : HttpSession`
  - 이미 세션이 있다면 그 세션을 돌려주고, 세션이 없으면 null을 반환한다.
- `HttpSession's getId() : String` 
  - jSessionId 반환
- `HttpSession's setAttribute("key", value) : Void` 
  - "key"를 사용하여 객체를 세션에 바인딩한다. value의 자료형을 예측할 수 없기 때문에 Object형으로 업캐스팅하여 모두 받아낸다.
- `HttpSession's getAttribute("key") : Object` 
  - "key"로 바인딩된 객체를 돌려주고, "key"로 바인딩된 객체가 없으면 null을 반환한다. 
  - value는 세션에 저장할 때 Object형으로 업캐스팅을 했으므로, 가져올 땐 원래대로 다운캐스팅 해야 한다.
- `HttpSession's removeAttribute("key") : Void`
  - 값을 제거한다.
- `HttpSession's invalidate() : Void`
  - session 종료
  - session에 바인딩 된 객체들도 함꼐 제거
- `HttpSession's getMaxInactiveInterval(int second) : Void`
  - client가 설정 시간 동안 request가 없으면 session 만료

---

## Session VS Cookie
|       |                          Session                          |                       Cookie                        |
|:-----:|:---------------------------------------------------------:|:---------------------------------------------------:|
| Type  |       `javax.servlet.http.HttpSession (Interface)`        |         `javax.servlet.http.Cookie (Class)`         |
| 저장 위치 |                  Server의 메모리에 Object로 저장                  |                Client의 컴퓨터에 file로 저장                |
| 저장 형식 |           Object는 모두 가능 (일반적으로 Dto, List 등 저장)            |              file에 저장되기 때문에 String 형태               |
| 사용 예  |                   로그인 시 사용자 정보, 장바구니 등                    | 최근 본 상품 목록, 아이디저장(자동로그인), <br/>팝업메뉴에서 '오늘은 그만 보기' 등 |
| 용량제한  |                           제한 없음                           |                 도메인당 20개, 1쿠키당 4KB                  |
| 만료시점  | 알 수 없음(Client가 로그아웃하거나,<br/> 일정 시간동안 session에 접근하지 않을 경우) |         쿠키 저장 시 설정(설정이 없을 경우 브라우저 종료 시 만료)          |
