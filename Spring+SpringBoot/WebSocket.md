# WebSocket

## WebSocket이란?

- 기존의 단방향 HTTP 프로토콜과 호환되어 양방향 통신을 제공하기 위해 개발된 프로토콜
- 일반 Socket통신과 달리 **HTTP 80 Port를 사용**하므로 방화벽에 제약이 없으며 통상 WebSocket으로 불린다.
- 접속까지는 HTTP 프로토콜을 이용하고, 그 이후 통신은 자체적인 WebSocket 프로토콜로 통신하게 된다.

> ### Socket 통신
>- 소켓(Socket)은 TCP/IP 기반 네트워크 통신에서 데이터 송수신의 마지막 접점을 의미한다. 소켓통신은 이러한 소켓을 통해 서버-클라이언트간 데이터를 주고받는 양방향 연결 지향성 통신을 말한다.

- 웹 소켓은 **HTTP를 사용하는 네트워크 데이터 통신의 단점을 보완하는데 그 목적**이 있다.
- **모든 HTTP를 사용한 통신은 클라이언트가 먼저 요청을 보내고, 그 요청에 따라 웹 서버가 응답하는 형태이며 웹 서버는 응답을 보낸 후 웹 브라우저와의 연결을 끊는다.**
    - 양쪽이 데이터를 통시에 보내는 것이 아니기 때문에 이러한 통신 방식을 반이중 통신(Half Duplex)라고 한다.
    - > ### 반이중 통신
      > - 양방향 전송이 가능하지만 동시에 양쪽 방향에서 전송할 수 없는 방식
      > - 예) 무전기, 모뎀을 이용한 데이터 통신

- 웹 소켓이 존재하기 전에는 Polling이나 Long Polling, Streaming 등의 방식으로 해결했었다.

---

## Polling

<figure align="center">
<img src="https://user-images.githubusercontent.com/76946536/213354309-9b9f902f-00a3-487b-a489-1bd192b01c4f.gif" alt="polling"/>
<figcaption >출처:https://rubberduck-debug.tistory.com/123</figcaption>
</figure>

- 클라이언트가 평범한 HTTP Request를 서버로 계속 요청해 이벤트 내용을 전달받는 방식
- 가장 쉬운 방법이지만 클라이언트가 지속적으로 Request를 요청하기 때문에 클라이언트의 개수가 많아지면 **서버의 부담이 급증**한다.
- HTTP Request Connection을 맺고 끊는 것 자체가 부담이 많은 방식이고, 클라이언트에서 실시간 정도의 빠른 응답을 기대하기 어렵다.

---

## Long Polling

<figure align="center">
<img src="https://user-images.githubusercontent.com/76946536/213355115-6b6f3c91-99c9-4158-9612-d06922498fd2.gif" alt="longPolling"/>
<figcaption >출처:https://rubberduck-debug.tistory.com/123</figcaption>
</figure>

- 클라이언트에서 서버로 일단 HTTP Request를 요청한다.
- 이 상태로 계속 기다리다가 서버에서 해당 클라이언트로 전달할 이벤트가 있다면 그 순간 Response 메시지를 전달하며 연결이 종료된다.
- 곧이어 클라이언트가 다시 HTTP Request를 요청해 서버의 다음 이벤트를 기다리는 방식
- Polling 방식보다 서버의 부담이 줄겠으나, 클라이언트로 보내는 이벤트들의 시간간격이 좁다면 Polling과 별 차이가 없게 되며, **다수의 클라이언트에게 동시에 이벤트가 발생될 경우 서버의 부담이
  급증한다.**

---

## Streaming

<figure align="center">
<img src="https://user-images.githubusercontent.com/76946536/213355498-35665e16-f85a-4195-9bd4-9e8fbdff5047.gif" alt="streaming"/>
<figcaption >출처:https://rubberduck-debug.tistory.com/123</figcaption>
</figure>

- Long Polling과 마찬가지로 클라이언트 -> 서버로 HTTP Request를 요청한다.
- 서버 -> 클라이언트로 이벤트를 전달할 때 해당 요청을 해제하지 않고 필요한 메세지만 보내기(Flush)를 반복하는 방식
- Long Polling과 비교하여 **서버에 메세지를 보내지 않고도 다시 HTTP Request 연결을 하지 않아도 되어 부담이 경감**

---

## WebSocket

- HTTP의 특징인 **연결 -> 연결 해제** 때문에 효율이 많이 떨어지게 되고, 웹 브라우저말고 외부 플러그인이 항상 필요하게 되었다.
- 이런 상황을 극복하고자 **2014년 HTML5에 웹 소켓이 포함**되었다.
- **웹소켓은 클라이언트가 접속 요청을 하고 웹 서버가 응답한 후 연결을 끊는 것이 아닌 Connection을 그대로 유지하고 클라이언트의 요청 없이도 데이터를 전송할 수 있는 프로토콜이다.**
- 프로토콜의 요청은 **"ws://~**"로 시작한다.
- 웹소켓은 HTTP환경에서 전이중 통신(Full Duplex, 2-way communication)을 지원하기 위한 프로토콜
    - > ### 전이중 통신
      > - 동시에 양방향 전송이 가능한 방식으로, 전송량이 많고, 전송 매체의 용량이 클 때 사용
      > - 예) 전화, 전용선을 이용한 데이터 통신
- 웹소켓이 기존의 TCP 소켓과 다른 점은 **최초 접속이 일반 HTTP Request를 통해 HandShaking 과정**을 통해 이뤄진다는 점이다.
- HTTP Request를 그대로 사용하기 때문에 기존의 80, 443 포트로 접속을 하므로 **추가 방화벽을 열지 않고도 양방향 통신이 가능**하고, HTTP 규격인 CORS 적용이나 인증 등 과정을 기존과
  동일하게 가져갈 수 있는 것이 장점이다.
- 실시간성을 보장해야 하고, 변경 사항의 빈도가 잦다면, 또는 짧은 대기 시간, 고주파수, 대용량의 조합인 경우 웹소켓이 좋은 해결책이 될 수 있다.

---

## WebSocket 접속 과정

<figure align="center">
<img src="https://user-images.githubusercontent.com/76946536/213358552-ea294032-043a-45f7-b7b3-d15b02fb1bff.png" alt="websocket"/>
<figcaption >출처: https://blog.naver.com/eztcpcom/220070508655</figcaption>
</figure>

- 웹소켓을 이용하여 서버와 클라이언트가 통신을 하려면 먼저 웹소켓 접속 과정을 거쳐야 한다.
- 웹소켓 접속 과정은 **TCP/IP 접속** 그리고 **웹소켓 열기 HandShake 과정**으로 나눌 수 있다.
- 웹소켓도 TCP/IP 위에서 동작하므로, 서버와 클라이언트는 웹소켓을 사용하기 전에 서로 TCP/IP 접속이 되어있어야 한다.

### 웹소켓 열기 Handshake

- 웹소켓 열기 핸드셰이크는 **클라이언트가 먼저 핸드셰이크 요청을 보내고 이에 대한 응답을 서버가 클라이언트로 보내는 구조**이다.
- 서버와 클라이언트는 **HTTP 1.1 프로토콜**을 사용하여 요청과 응답을 보낸다.

---

## 참고자료

- https://dev-gorany.tistory.com/212