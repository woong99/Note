# REST-API

## REST (Representational State Transfer : 자원의 상태 전달) - 네트워크 아키텍처

1.  Client, Server : 클라이언트와 서버가 서로 독립적으로 분리 되어 있어야 한다.
2.  Stateless : 요청에 대해서 클라이언트의 상태를 서버에 저장하지 않는다.
3.  Cache : 클라이언트는 서버의 응답을 Cache(임시저장) 할 수 있어야 한다. 클라이언트가 Cache를 통해서 응답을 재사용할 수 있어야 하며, 이를 통해서 서버의 부하를 낮춘다.
4.  계층화 (Layered System) : 서버와 클라이언트 사이에, 방화벽, 게이트웨이, Proxy 등 다양한 계층 형태로 구성이 가능해야 하며, 이를 확장 할 수 있어야 한다.
5.  인터페이스 일관성 : 인터페이스의 일관성을 지키고, 아키텍처를 단순화시켜 작은 단위로 분리하여, 클라이언트, 서버가 독립적으로 개선 될 수 있어야 한다.
6.  Code on Demand(Optional) : 자바 애플릿, 자바스크립트, 플래시 등 특정한 기능을 서버로부터 클라이언트가 전달받아 코드를 실행 할 수 있어야 한다.

---

## 다음의 인터페이스 일관성이 잘 지켜졌는지에 따라, REST를 잘 사용했는지 판단을 할 수 있다.

1. 자원의 식별

   - 웹 기반의 REST에서는 리소스 접근을 할 때 URI를 사용합니다.
   - Ex) https://foo.co.kr/<span style="color:tomato">user</span>/<span style="color:blue">100</span>
   - Resource: user
   - 식별자 : 100

2. 메시지를 통한 리소스 조작

   - Web에서는 다양한 방식으로 데이터를 전달 할 수 있습니다.
   - 그 중에서 가장 많이 사용하는 방식은 HTML, XML, JSON, TEXT 등이 있습니다.
   - 이 중에서 어떠한 타입의 데이터인지를 알려주기 위해서 HTTP Header 부분에 content-type을 통해서 데이터의 타입을 지정해 줄 수 있습니다.
   - 또한, 리소스 조작을 위해서 데이터 전체를 전달하지 않고, 이를 메시지로 전달합니다.
   - Ex) 서버의 user라는 정보의 전화번호를 처음에는 number라 결정했고, 이 정보를 Client와 주고 받을 때, 그대로 사용하고 있었다면, 후에 서버의 resource 변경으로 phone-number로 바뀌게 된다면 Client는 처리를 하지 못 하고 에러가 납니다.
   - 이러한 부분을 방지하기 위하여, 별도의 메시지의 형태로 데이터를 주고 받으며, client-server가 독립적으로 확장 가능하도록 합니다.

3. 자기 서술적 메시지
   - 요청하는 데이터가 어떻게 처리돼야 하는지 충분한 데이터를 포함할 수 있어야 한다.
   - HTTP 기반의 REST에서는 HTTP Method와 Header 정보, 그리고 URI의 포함되는 정보로 표현할 수 있습니다.
   - GET : https://foo.co.kr/user/100, 사용자의 정보 요청
   - POST : https://foo.co.kr/user, 사용자 정보 생성
   - PUT : https://foo.co.kr/user, 사용자 정보 생성 및 수정
   - DELETE : https://foo.co.lr/user/100, 사용자 정보 삭제
   - 그 외에 담지 못한 정보들은 URI의 메시지를 통하여 표현한다.
4. 애플리케이션 상태에 대한 엔진으로써 하이퍼미디어

- REST API를 개발할 때 단순히 Client 요청에 대한 데이터만 응답해주는 것이 아닌 관련된 리소스에 대한 Link 정보까지 같이 포함되어져야 한다.

이러한 조건들을 잘 갖춘 경우 <span style="color:tomato">REST Ful</span>하다고 표현하고, 이를 <span style="color:tomato">REST API</span>라고 부릅니다.
