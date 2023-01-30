# STOMP(Simple Text Oriented Messaging Protocol)

## STOMP란?

- 메세지 전송을 효율적으로 하기 위해 탄생한 프로토콜
- 기본적으로 **pub / sub 구조**로 되어있어 메세지를 전송하고 메세지를 받아 처리하는 부분이 확실히 정해져 있기 떄문에 개발자 입장에서 명확하게 인지하고 개발할 수 있는 이점이 있다.
    - > ### pub / sub
      > - 메세지를 공급하는 주체와 소비하는 주체를 분리해 제공하는 메세징 방법이다.
      >> - **채팅방 생성** : pub / sub 구현을 위한 Topic이 생성됨
      >> - **채팅방 입장** : Topic 구독
      >> - **채팅방에서 메세지를 송수신** : 해당 Topic으로 메세지를 송신(pub), 메세지를 수신(sub)
- <span style="color:orange">WebSocket 위에서 동작하는 프로토콜로써 클라이언트와 서버가 전송할 메세지의 유형, 형식, 내용들을 정의하는 매커니즘이다.</span>
- **메세지의 헤더에 값을 줄 수 있어 헤더 값을 기반으로 통신 시 인증 처리를 구현하는 것**도 가능하다.
- TCP 또는 WebSocket 같은 양방향 네트워크 프로토콜 기반으로 동작한다.
- Text 지향 프로토콜이나, Message Payload에는 **Text or Binary 데이터**를 포함 할 수 있다.
- 클라이언트는 메세지를 전송하기 위해 **SEND, SUBSCRIBE COMMAND**를 사용할 수 있다.
    - **SEND, SUBSCRIBE COMMAND 요청 Frame**에는 메세지가 무엇이고, 누가 받아서 처리할지에 대한 Header 정보가 포함되어 있다.
    - 이런 명령어들은 `destination` 헤더를 요구하는데 이것이 어디에 전송할지, 혹은 어디에서 메세지를 구독할 것인지를 나타낸다.
- 위와 같은 과정을 통해 STOMP는 **Publish-Subscribe** 매커니즘을 제공한다. 즉 **Broker**를 통해 타 사용자들에게 메세지를 보내거나 서버가 특정 작업을 수행하도록 메세지를 보낼 수
  있게 된다.
- **Spring에서 지원하는 STOMP를 사용하면 Spring Websocket 애플리케이션은 STOMP Broker**로 동작하게 된다.
    - Simple In-Memory Broker를 이용해 SUBSCRIBE 중인 다른 클라이언트들에게 메세지를 보내준다.
    - Simple In-Memory Broker는 클라이언트의 SUBSCRIBE 정보를 자체적으로 메모리에 유지한다.
- **Spring은 메세지를 외부 Broker(RabbitMQ, ActiveMQ 등)에게 전달하고, Broker는 Websocket으로 연결된 클라이언트에게 메세지를 전달하는 구조가 될 수 있다.**
- **STOMP는 HTTP에서 모델링되는 Frame 기반 프로토콜이다.** Frame은 몇 개의 Text Line으로 지정된 구조인데 첫 번째 라인은 Text이고 이후 Key:Value 형태로 Header의 정보를
  포함한다. 그리고 다음 빈 라인을 추가하고 Payload가 존재한다.
    - > ### COMMAND
      >  #### header1:value1
      >  #### header2:value2
      >  #### <br>
      >  #### Body^@
      >> - COMMAND : SEND, SUBSCRIBE를 지시할 수 있다.
      >> - header : 기존의 Websocket으로는 표현이 불가능한 header를 작성할 수 있다.
           >>
        - destination : 이 헤더로 메세지를 보내거나(SEND), 구독(SUBSCRIBE)할 수 있다.
- **destination**은 의도적으로 정보를 불분명하게 정의하였는데, 이는 STOMP 구현체에서 문자열 구문에 따라 직접 의미를 부여하도록 하기 위함이다. 따라서 destination 정보는 STOMP 서버
  구현체마다 달라질 수 있기 때문에 각 구현체의 스펙을 살펴보아야 한다.
    - > ### 일반적으로 다음의 형식을 따른다.
      > #### "topic/.." --> publish-subscribe (1:N)
      > #### "queue/"   --> point-to-point (1:1)

---

## 예시

### ClientA가 5번 채팅방에 대해 구독을 하는 예시

> #### SUBSCRIBE
> #### destination : /topic/chat/room/5
> #### id : sub-1
> #### <br>
> #### ^@

### ClientB가 채팅 메세지를 보내는 예시

> #### SEND
> #### destination : /pub/chat
> #### content-type : application/json
> #### <br>
> #### {"chatRoomId" : 5, "type" : "MESSAGE", "writer" : "clientB"}^@

### 서버는 모든 구독자에게 메세지를 Broadcasting하기 위해 MESSAGE COMMAND를 사용할 수 있다. 서버는 내용을 기반(ChatRoomId)으로 메세지를 전송할 broker에게 전달한다.

> #### MESSAGE
> #### destination: /topic/chat/room/8
> #### message-id : d1fc0152-1
> #### subscription : sub-1
> #### <br>
> #### {"chatRoomId" : 5, "type" : "MESSAGE" , "writer" : "clientB"}^@

<figure align="center">
<img src="https://user-images.githubusercontent.com/76946536/213593407-74f86dee-7dbb-465d-82bb-054975edd08c.jpg" alt="longPolling"/>
<figcaption >출처: https://supawer0728.github.io/2018/03/30/spring-websocket/
</figcaption>
</figure>

**서버는 불분명한 메세지를 전송할 수 없다.** 그러므로 서버의 모든 메세지는 특정 클라이언트의 구독에 응답하여야 하고, <span style="color:red"> 서버 메세지의 "subscription-id"
헤더는 클라이언트 구독의 "id" 헤더와 일치해야 한다.</span>
---

## Client Frames

### SEND

SEND Frame은 destination의 메세징 시스템으로 메세지를 보낸다. **어디로 보낼지에 대한 "destination"은 필수 헤더이다.**
SEND Frame은 body가 있는 경우 **"content-length"** 와 **"content-type"** 헤더를 반드시 가져야만 한다.
> #### SEND
> #### destination : /queue/a
> #### content-type : text/plain
> #### <br>
> #### hello queue a
> #### ^@

### SUBSCRIBE

SUBSCRIBE Frame은 주어진 destination에 등록하기 위해 사용한다. SEND Frame과 마찬가지로 Subscribe는 client가 구독하기 원하는 목적지를 가리키는 "destination"
헤더를 필요로 한다. 가입된 대성에서 수신된 모든 메세지는 이후 MESSAGE Frame으로서 서버에서 클라이언트에게 전달된다. 단일 연결은 여러 개의 구독을 할 수 있으므로 구독 ID를 고유하게 식별하기 위해
**"id"헤더**가 프레임에 포함되어야 한다.
> #### SUBSCRIBE
> #### id : 0
> #### destination : /queue/foo
> #### ack : client
> #### <br>
> #### ^@

---

## STOMP Benefits

Spring framework 및 Spring Security는 STOMP를 사용하여 WebSocket만 사용할 때보다 더 다채로운 모델리을 할 수 있다.

- Messaging Protocol을 만들고 메세지 형식을 커스터마이징 할 필요가 없다.
- WebSocket 기반으로 각 Connection마다 WebSocketHandler를 구현하는 것 보다 `@Controller`된 객체를 이용해 조직적으로 관리할 수 있다.
    - 즉, 메세지는 STOMP의 "destination" 헤더를 기반으로 `@Controller` 객체의 `@MethodMapping` 메서드로 라우팅된다.
- STOMP의 "destination" 및 Message Type을 기반으로 메세지를 보호하기 위해 Spring Security를 사용할 수 있다.

---

## 참조자료

- https://dev-gorany.tistory.com/235
- https://stomp.github.io/stomp-specification-1.2.html#Frames_and_Headers