### HTTP

- HTTP는 하이퍼텍스트 전송 프로토콜(HyperText Transfer Protocol)이다.
- 웹 페이지를 방문할 때마다 컴퓨터는 HTTP(Hypertext Transfer Protocol)를 사용하여 인터넷 어딘가에 있는 다른 컴퓨터에서 해당 페이지를 다운로드한다.

### ❗️HTTP의 특징
- 클라이언트 서버 구조
- 무상태 프로토콜(Stateless)
- 비 연결성(Connectionless)
- HTTP 메세지 (블로그에 포함 안됨)
- 단순함, 확장 가능 (블로그에 포함 안됨)

상태를 저장하지 않는 특징때문에 클라이언트와 서버가 통신을 하기위해 서로가 누구인지 확인하기 어렵다
이를 해결하기 위하여 쿠키와 세션을 이용한다.

### Cookie
로그인 여부와 로그인 정보 유지를 위해서는 쿼리 파라미터(자원)를 사용하는 방법이 있는데, 이는 너무 번거롭고 힘든 작업이다. 따라서 쿠키를 사용해 사용자의 정보를 저장하기로 했다. 아래의 쿠키의 사용법을 살펴보자.

![](https://velog.velcdn.com/images/winckey0/post/4202f888-790b-4f7d-b69d-782bbd77508c/image.png)


사용자가 로그인을 하기위해 아이디와 비밀번호를 입력해 서버에 요청하면, 서버는 로그인 정보를 회원저장소에서 찾아 memberId를 key로 하는 Cookie를 생성해 웹브라우저에게 응답한다.

- 쿠키의 구조 : key memberId value 1

웹브라우저는 서버의 응답 메시지를 받아 Cookie를 쿠키 저장소에 저장한다.

#### 쿠키 전달

![](https://velog.velcdn.com/images/winckey0/post/ee116ba5-902c-4f02-a853-88451e2aca72/image.png)

Cookie를 쿠키 저장소에 저장했으니, 이제는 서버에 요청을 할 때마다 쿠키 저장소에서 쿠키를 찾아서 요청메세지에 담는다. 여기서 사용하는 쿠키는 세션 쿠키로 로그인 상태에서, 웹브라우저가 종료되지 않은 상태라면 계속 유지 된다.


### Session
#### 로그인

![](https://velog.velcdn.com/images/winckey0/post/e627be10-9ffb-43ef-b568-5de5ed1b81af/image.png)

사용자가 loginId , password 정보를 요청 메시지에 담아 서버에 전달하면 서버에서 회원 저장소를 통해 해당 사용자가 맞는지 확인한다.

#### 세션 생성
![](https://velog.velcdn.com/images/winckey0/post/b7a43643-c22d-4880-b8d3-a5c659d3ea55/image.png)


추정 불가능한 세션id를 생성해야하므로 UUID를 생성한다.
생성된 세션ID와 세션에 보관할 값인 memberA를 서버의 세션 저장소에 보관한다.
이 때 세션 저장소는 key와 value로 구성되어 있으며 key는 sessionId이고 value는 객체로 이루어져 있다.


#### 세션 id를 응답 쿠키로 전달

![](https://velog.velcdn.com/images/winckey0/post/bfd824cb-430a-4eff-af2a-0bfdc34b1aa4/image.png)



서버는 클라이언트에 mySessionId라는 이름으로 세션ID만 쿠키에 담아 전달하고 클라이언트는 받은 mySessionId를 쿠키 저장소에 보관한다.

쿠키의 구조 : key(name) mySessionId value(id) sessionId

따라서 클라이언트와 서버는 결국 쿠키로 연결이 되어야한다.

여기서 중요한 것은 서버는 세션id만 클라이언트에게 전달하므로 쿠키에는 중요한 회원정보등 회원과 관련된 아무런 정보도 담겨있지 않다는 것이다.
오직 추정 불가능한 세션 ID만 쿠키를 통해 클라이언트에 전달한다.

이때 주의해야할 점이 있는데 바로 쿠키와 세션저장소의 key와 value를 헷갈리지 말아야한다는 것이다. 쿠키에는 sessionId를 value값으로 보내는데 세션저장소에서는 sessionId가 key값이 된다. 이를 헷갈리지 말자.

쿠키의 구조 : key SessionName value SessionId
세션 저장소의 구조 : key SessionId value Object
