# 네트워크 
<br>


## 1. Protocol
통신 프로토콜 또는 통신 규약은 컴퓨터나 원거리 통신 장비 사이에서 메시지를 주고 받는 양식과 규칙의 체계이다. 

### 1-1프로토콜의 기본 요소
 

 
- 구문(Syntax)

  -  데이터의 형식 (아날로그 or 디지털), 부호화 (Unicode, ASCII), 신호크기 (0과 1의 전압 세기와, 어떻게 표현할지) 를 정하여 구문을 이룸.
  - 데이터 구조와 순서에 대한 표현
  - 예) 어떤 프로토콜에서 데이터의 처음 8비트는 송신지의 주소를 나타내고, 다음 8비트는 수신자의 주소를 나타낸다.

 
- 의미(Semantics)

  -  전송제어 (동기화, 전송정지 및 재개, 완료, 재전송, 등등의 신호를 정함), 오류수정(데이터 무결성 검사 방법, 패리티비트, CRC) 등을 정하여 의미를 이룸
  - 예) 주소부분 데이터는 메시지가 전달될 경로 혹은 최종 목적지를 나타낸다.

 
- 타이밍(Timing)

  - 두 객체간의 통신 속도 조정
  - 메시지의 전송 시간 및 순서 등에 대한 특성
  - 예) 송신자가 데이터를 10Mbps의 속도로 전송하고 수신자가 1Mbps의 속도로 처리를 하는 경우 타이밍이 맞지 않아 데이터 유실이 발생할 수 있다
  
<br>
위사항들을 정의하여 하나의 약속으로 데이터형태를 이루어 전송하는형태를 프로토콜이라 한다.


### 1-2프로토콜의 기능


#### 1. 분할과 재조립
 [패킷교환](https://better-together.tistory.com/110)을 예로 시분할 방식으로 회선을 사용하려면 패킷을 일정 크기로 잘라야 한다. 이것을 분할이라고 함.
 분할된 패킷을 받았을 때, 데이터를 사용하기 위해서 원래대로 하나로 붙이는 작업이 필요. 이 과정을 재조립 함.
 쉽게 말해서 쪼개서 보낸 후, 쪼개진 데이터를 받고 그것을 원래의 모양대로 복원.

#### 2. 캡슐화

 캡슐화란 (N+1)계층으로부터 PDU(Protocol Data Unit)를 받아 [(N+1)계층에서는 이를 (N+1) PDU라 하며 N계층에서는 이를 N SDU라 한다]N SDU에 N계층의 제어정보(PCI : Protocol Control Information)를 덧붙여 N PDU를 만들게 되는데 이같이 데이터가 상위계층에서 하위계층으로 내려가면서 데이터에 제어정보를 덧붙이는 과정을 캡슐화라하고 수신측에서는(또는 전송과정에서) 반대로 제어정보를 하나씩 떼어가면서 제어정보에 따라 필요한 기능을 수행함으로써 상위계층에 서비스를 제공하게 되는데 이를 비캡슐화라 한다
 
 
 ![PDU예제](https://velog.velcdn.com/images/winckey0/post/4d5e1c89-2c39-4edb-a64f-06286284b835/image.png)

 

#### 3. 연결제어 방식
 크게 연결형 방식과 비연결형 방식이 있습니다.
 연결형같은 경우는 세션이 만들어진 다음에 계속적으로 통신이 일어나는 형식이기 때문에, 순서가 보장됩니다. 예를 들어 전화, 인터넷 전화, 인터넷 화상전화를 예로 들 수 있습니다.
 비연결형 방식은 인터넷 www서비스를 예로 들 수 있으며, 순서보장이 되지 않습니다.

#### 4. 흐름제어
데이터를 보내라, 전송을 중지해라, 다시 보내라 등 데이터의 전송을 제어하는 부분입니다.

#### 5. 오류제어
 크게 두 가지 형태가 있는데, 오류가 발생 시 재 전송을 요구하는 형태와, 직접 오류를 복구하는 형태로 나눌 수 있습니다. 주로 우리는 인터넷 회선이 빠르기 때문에 재 전송을 주로 이용합니다.

 어떻게 오류를 찾아낼까요?
 데이터를 7비트씩 끊어서 끝에 한개의 숫자를 덧붙여서 8비트로 만들어준 후, 끝 비트를 오류검출 비트로 사용하는데, 짝수 패리티비트 방식과 홀수 패리티비트 방식이 있습니다.

패리티비트 오류검출방식
 7비트 데이터에 1의 개수가 짝수이면 끝에 1, 1의 개수가 홀수이면 끝에 0을 붙여서 데이터 오류 발생 시 재 전송을 요구하는 형태입니다. 또한, 이전 데이터(바이트 단위)들을 각 자리수 마다 비트를 검사하여 같은 방식(홀수 짝수 확인)으로 1byte짜리 오류 검출 코드를 만들어서 추가로 보냅니다. 이런 식으로 오류를 제어한다고 볼 수 있습니다.

#### 6. 순서제어
 패킷을 분할하여 보낼 때, 각 패킷에 시퀀스 번호를 부여하여, 순서대로 패킷이 도착하도록 하는 기능입니다.

#### 7. 주소 지정
 TCP/IP 구조에서 IP를 예로 들 수 있으며, 네트워크의 개체마다 서로 식별할 수 있도록 고유번호를 부여한 것이라고 볼 수 있습니다.

#### 8. 멀티플렉싱
 하나의 회선에 다수의 사용자가 동시에 사용하는 것이 가능하도록 가상회선을 설정하는 것이라고 볼 수 있습니다.







