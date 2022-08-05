# URI 설계 패턴

## URI (Uniform Resource Identifier)

- 인터넷에서 특정 자원을 나타내는 주소 값. 해당 값은 유일하다. (응답은 달라질 수 있다.)
- 요청 : https://www.fastcampus.co.kr/resource/sample/1
- 응답 : fastcampus.pdf, fastcampus.docx

## URL(Uniform Resource Locator)

- 인터넷 상에서의 자원, 특정 파일이 어디에 위치하는지 식별하는 주소
- 요청 : https://www.fastcampus.co.kr/fastcampus.pdf

**URL은 URI의 하위 개념입니다.**

---

## URI 설계 원칙 (RFC-3986)

- 슬래시 구분자 ( / ) 는 계층 관게를 나타내는 데 사용한다.
  - https://fastcampus.co.kr/classes/java/curriculums/web-master
- URI 마지막 문자로 ( / ) 는 포함하지 않는다.
  - https://fastcampus.co.kr/classes/java/curriculums/web-master/ (X)
- 하이픈 ( - ) 은 URI 가독성을 높이는데 사용한다.
  - https://fastcampus.co.kr/classes/java/curriculums/web-master
- 밑줄 ( \_ ) 은 사용하지 않는다.
  - https://fastcampus.co.kr/classes/java/curriculums/web_master (X)
- URI 경로에는 소문자가 적합하다.
  - https://fastcampus.co.kr/classes/JAVA/curriculums/web-master (X)
  - https://fastcampus.co.kr/classes/java/curriculums/web-master (O)
- 파일 확장자는 URI에 포함하지 않는다.
  - https://fastcampus.co.kr/classes/java/curriculums/web-master.jsp (X)
- 프로그래밍 언어에 의존적인 확장자를 사용하지 않는다.
  - https://fastcampus.co.kr/classes/java/curriculums/web-master.do (X)
- 구현에 의존적인 경로를 사용하지 않는다.
  - https://fastcampus.co.kr/servlet/classes/java/curriculums/web-master (X)
- 세션 ID를 포함하지 않는다.
  - https://fastcampus.co.kr/classes/java/curriculums/web-master?session-id=abcdef (X)
- 프로그래밍 언어의 Method명을 이용하지 않는다.
  - https://fastcampus.co.kr/classes/java/curriculums/web-master?action-intro (X)
- 명사에 단수형 보다는 복수형을 사용해야 한다. 컬렉션에 대한 표현은 복수로 사용
  - https://fastcampus.co.kr/classes/java/curriculums/web-master
- 컨트롤러 이름으로는 동사나 동사구를 사용한다.
  - https://fastcampus.co.kr/classes/java/curriculums/web-master/re-order
- 경로 부분 중 변하는 부분은 유일한 값으로 대체한다.
  - https://fastcampus.co.kr/classes/java/curriculums/web-master/lessons/{lesson-id}/users/{user-id}
  - https://fastcampus.co.kr/classes/java/curriculums/web-master/lessons/2/users/100
- CRUD 기능을 나타내는 것은 URI에 사용하지 않는다.
  - GET : 생략.../curriculums/web-master/lessons/2/users/100/READ (X)
  - DELETE : 생략.../curriculums/web-master/lessons/2/users/100 (O)
- URI Query Parameter 디자인
  - URI 쿼리 부분으로 컬렉션 결과에 대해서 필터링 할 수 있다.
  - 생략.../curriculums/web-master?chapter=2
- URI 쿼리는 컬렉션의 결과를 페이지로 구분하여 나타내는데 사용한다.
  - 생략.../curriculums/web-master?chapter=2&page=0&size=10&sort=asc
- API에 있어서 서브 도메인은 일관성 있게 사용해야 한다.
  - https://api.fastcampus.co.kr
  - https://api-fastcampus.co.kr
- 클라이언트 개발자 포탈 서브 도메인은 일관성 있게 만든다.
  - https://dev-fastcampus.co.kr
  - https://developer-fastcampus.co.kr
