#### HTTP (HyperText Transfer Protocol)
텍스트 기반의 통신 규약으로 인터넷에서 데이터를 주고받을 수 있는 프로토콜이다.

#### REST 특징
- Server-Client(서버-클라이언트 구조)
자원이 있는 쪽이 Server, 자원을 요청하는 쪽이 Client가 된다.
REST Server: API를 제공하고 비즈니스 로직 처리 및 저장을 책임진다.
Client: 사용자 인증이나 context(세션, 로그인 정보) 등을 직접 관리하고 책임진다.
서로 간 의존성이 줄어든다.
- Stateless(무상태)
HTTP 프로토콜은 Stateless Protocol이므로 REST 역시 무상태성을 갖는다.
Client의 context를 Server에 저장하지 않는다.
즉, 세션과 쿠키와 같은 context 정보를 신경쓰지 않아도 되므로 구현이 단순해진다.
Server는 각각의 요청을 완전히 별개의 것으로 인식하고 처리한다.
각 API 서버는 Client의 요청만을 단순 처리한다.
즉, 이전 요청이 다음 요청의 처리에 연관되어서는 안된다.
물론 이전 요청이 DB를 수정하여 DB에 의해 바뀌는 것은 허용한다.
Server의 처리 방식에 일관성을 부여하고 부담이 줄어들며, 서비스의 자유도가 높아진다.

https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html

#### Rest API 
Request Method (요청의 종류)
GET : 자료를 요청할 때 사용
POST : 자료의 생성을 요청할 때 사용
PUT : 자료의 수정을 요청할 때 사용
DELETE : 자료의 삭제를 요청할 때 사용


REST의 정의
“Representational State Transfer” 의 약자
자원을 이름(자원의 표현)으로 구분하여 해당 자원의 상태(정보)를 주고 받는 모든 것을 의미한다.

REST는 약속 법칙이 아님


#### HTTP 2, 3

#### HTTP keep-alive
하나의 TCP connection을 활용해서 여러개의 HTTP request/response를 주고받을 수 있도록 해준다. keep-alive옵션은 설계상 여러 문제점(e.g. proxy 문제) 이 생기면서 **HTTP/1.1부터 사용되고 있지 않지만** 여전히 많은 웹 어플리케이션에서 사용하고 있기 때문에 이해해둘 필요가 있다.


#### HTTP2
 - Multiplexed Streams
한 커넥션으로 동시에 여러 개의 메세지를 주고 받을 있으며, 응답은 순서에 상관없이 stream으로 주고 받음
HTTP/1.1의 Connection Keep-Alive, Pipelining의 개선했다.
![](https://velog.velcdn.com/images/winckey0/post/d9eeaf41-d2ce-496f-87ee-1a2f5cf8ae87/image.png)

- Server Push
서버는 클라이언트의 요청에 대해 요청하지도 않은 리소스를 보내줄 수 있게 되었다.


- HTTP/2의 단점
하지만 HTTP2는 여전히 TCP를 이용하기 때문에 Handshake의 RTT(Round Trip Time)로인한 Latency(지연시간), TCP의 HOLB 문제는 해결 할 수 없다.

  - HOLB
    - Head Of Line Blocking
    - TCP를 사용하는 통신에서 패킷은 무조건 정확한 순서대로 처리되어야 한다. 수신 측은 송신 측과 주고받은 시퀀스 번호를 참고하여 패킷을 재조립 해야하기 때문.
    - 따라서 중간에 패킷이 손실될 경우 완전한 데이터로 재조립하기 어렵기 때문에 송신 측은 해당 패킷이 제대로 전달되지 않았을 경우 재전송 해야 한다.
    
#### HTTP3
HTTP/2와는 다르게 UDP 기반의 프로토콜인 QUIC 을 사용하여 통신하는 프로토콜이다.

UDP는 TCP에 비해 안정성이 떨어지지만 3-way handshake이 없기 때문에 속도가 빠릅니다. 그리고 User Datagram Protocol이라는 이름처럼 사용자가 데이터그램을 커스텀하기가 쉽게 되어있습니다.

따라서 커스텀하기 쉬운 UDP를 이용해 3-way handshake가 아닌 다른 방법으로 신뢰성을 확보하도록 프로토콜을 설계하여 지연 시간을 줄이도록 했습니다.

RTT 감소로인한 지연시간 단축
 - 첫 연결 설정에 1 RTT만 소요된다. 그 이유는 연결 설정에 필요한 정보와 함께 데이터도 보내버리기 때문