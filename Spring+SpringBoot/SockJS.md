# SockJS
## WebSocket의 문제점
1. 모든 클라이언트의 브라우저에서 WebSocket을 지원한다는 보장이 없다.
2. Server/Client 중간에 위치한 Proxy가 Upgrade 헤더를 해석하지 못해 서버에 전달하지 못할 수 있다.
3. Server/Client 중간에 위치한 Proxy가 유휴 상태에서 도중에 Connection을 종료시킬 수 있다.

---

## WebSocket Emulation
- 우선 WebSocket을 시도하고, 실패할 경우 HTTP Streaming, Long-Polling 같은 HTTP 기반의 다른 기술로 전환해 다시 연결을 시도하는 거을 말한다.
- **node.js**를 사용한다면 **Socket.io**를 이용하는 것이 일반적이다.
- **Spring**을 사용한다면 **SockJS**를 이용하는 것이 일반적이다.
- Spring은 Servlet 스택 위에서 Server/Client 용도의 SockJS 프로토콜을 모두 지원한다.

---

## SockJS
- SockJS는 어플리케이션이 WebSocket API를 사용하도록 허용하지만 **브라우저에서 WebSocket을 지원하지 않는 경우**에 대안으로 애플리케이션의 코드를 변경할 필요 없이 런타임에 필요할 때 대체하는 것이다.
- SockJS는 다양한 기술을 이용해 웹소켓을 지원하지 않는 브라우저에서 정상적으로 동작하도록 해준다.
  - **WebSocket**
  - **HTTP Streaming**
  - **HTTP Long Polling**
### SockJS의 구성
- `SockJS Protocol`
- `SockJS Javascript Client` - 브라우저에서 사용되는 클라이언트 라이브러리
- `SockJS Server` 구현 - Spring-websocket 모듈을 통해 제공
- `SockJS Java Client` - Spring-websocket 모듈을 통해 제공 (Spring v4.1~)

### WebSocket Emulation Process
- SockJS Client는 서버의 기본 정보를 얻기 위해 `GET /info`를 호출한다.
  - 이는 **서버가 WebSocket을 지원**하는지, 전송 과정에서 **Cookies 지원이 필요**한지 그리고 **CORS를 위한 Origin 정보** 등을 응답으로 전달받는다.
- 그 이후 SockJS는 어떤 전송 타입을 사용할 지 결정한다.

  ![sockjs_info](https://user-images.githubusercontent.com/76946536/213386945-c493abc4-541a-42bf-b836-e14016d39a59.png)
  ![sockjs_info_payload](https://user-images.githubusercontent.com/76946536/213387200-7a19ad9f-e78c-481c-b157-d4cb4f45a747.png)
- 모든 전송 요청은 다음의 URL 구조를 갖는다
  > `https://host:port/app/endpoint/{server-id}/{session-id}/{transport}`
  > - server-id : 클러스터에서 요청을 라우팅하는데 사용하나 이외에는 의미 없음
  > - session-id : SockJS session에 소속하는 HTTP 요청과 연관성 있음
  > - transport : 전송 타입 (예: websocket, xhr-streaming, xhr-polling)
  >   ![socket_header](https://user-images.githubusercontent.com/76946536/213389815-17abbfad-7243-43ed-87fe-264e0b2503a2.png)
- **WebSocket 전송**은 WebSocket Handshaking을 위한 하나의 HTTP 요청을 필요로 한다. 모든 메세지들은 그 이후 사용했던 Socket을 통해 교환된다.
- **HTTP 전송**은 보다 더 많은 요청을 필요로 한다.
  - **Ajax/XHR Streaming**은 서버 -> 클라이언트로의 메세지들을 위해 하나의 Long-running 요청이 있고, 추가적인 HTTP POST 요청은 클라이언트 -> 서버로의 메세지를 위해 사용된다.
    <figure align="center">
      <img src="https://user-images.githubusercontent.com/76946536/213390818-cc04831f-ab55-415e-b680-e5580fbcd867.png" alt="xhr-streaming"/>
        <figcaption >출처: https://dev-gorany.tistory.com/224</figcaption>
      </figure>
    
  - **Long Polling**은 서버 -> 클라이언트로의 응답 후 현재의 요청을 끝내는 것을 제외하고는 **XHR Streaming**과 유사하다.

### 메세지 전송 예시
- **SockJS**는 메세지 Frame의 크기를 최소화하기 위해 노력한다.
- `"O"(open frame)` : 초기에 전송하고, 메세지는 `["msg1", "msg2"]`와 같은 JSON-Encoded 배열로서 전달된다.
- `"h"(heartbeat frame)` : 기본적으로 25초간 메세지 흐름이 없는 경우에 전송한다.
- `"c"(close frame)` : 해당 세션을 종료한다.
  ![sockjs_ex](https://user-images.githubusercontent.com/76946536/213392120-33d69037-062c-4e68-998f-d6301520aec9.png)

---

## Heartbeats
- SockJS은 프록시가 연결이 끊겼다는 결론을 내린는 것을 방지하기 위해 서버가 Heartbeat 메세지를 보내도록 요구한다.
- Spring SockJS 구성에는 HeartbeatTime 빈도를 사용자 정의하는 데 사용할 수 있는 속성이 있다.
- 기본값은 해당 연결에 어떤 메세지도 없는 25초를 사용하고, 이는 IETF 권고안을 따른다.
- **STOMP를 이용해 Heartbeat를 주고 받는 경우 SockJS Heartbeat 설정은 비활성화**된다.


---

## 참고자료
- https://dev-gorany.tistory.com/224