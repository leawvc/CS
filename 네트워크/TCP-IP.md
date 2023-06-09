# 2.2 TCP/IP 4계층 모델

- 인터넷 프로토콜 스위트(internet protocol suite)는 인터넷에서 컴퓨터들이 서로 정보를 주고받는 데 쓰이는 통신규약(프로토콜)의 모음이다.
- 본 교재에서는 **TCP/IP 4계층 모델**을 중심으로 설명하며, 각 계층들은 프로토콜의 네트워킹 범위에 따라 네 개의 추상화 된 계층으로 구성된다.

## 2.2.1 계층 구조

- TCP/IP 4계층 모델과 OSI 7계층 모델

![image](https://user-images.githubusercontent.com/81108344/210358874-69ce70b4-ee56-41bb-b3e3-f33d52bd3f0f.png)
  - OSI 계층은 응용 계층을 세 개로 쪼개고, 네트워크 접근 계층을 물리 계층과 링크 계층으로 나눠서 표현하는 것이 다르다.
  - 각 계층이 변경될 때 다른 계층이 영향을 받지 않도록 설계되었다.


### 애플리케이션 계층(응용 계층)

- **FTP, HTTP, SSH, SMTP, DNS** 등 **응용 프로그램**이 사용되는 프로토콜 계층
- 웹 서비스, 이메일 등 서비스를 실질적으로 사람들에게 제공하는 계층

<aside>
💡 FTP
- 장치와 장치 간 파일을 전송하는 데 사용되는 통신 프로토콜

SSH
- 보안되지 않은 네트워크에서 네트워크 서비스를 안전하게 운영하기 위한 암호화 네트워크 프로토콜

HTTP
- World Wide Web을 위한 데이터 통신의 기초이자 웹 사이트를 이용하는 데 쓰는 프로토콜

SMTP
- 전자 메일 전송을 위한 인터넷 표준 통신 프로토콜

DNS
- 도메인 이름과 IP 주소를 매핑해주는 서버

</aside>

---

### 전송 계층

- 송신자와 수신자를 연결하는 **통신 서비스**를 제공하며 연결 지향 데이터 스트림 지원, 신뢰성, 흐름 제어를 제공할 수 있다.
- 어플리케이션 계층(응용 계층)과 인터넷 계층 사이의 데이터가 전달될 때 중계 역할을 한다.
- TCP
  - 패킷 사이의 순서를 보장하고 연결지향 프로토콜을 사용해 수신 여부를 확인하며 ‘***가상회선 패킷 교환 방식***’을 사용한다.
- UDP
  - 순서를 보장하지 않으며 수신 여부를 확인하지 않는다. 단순히 데이터만 주는 ‘***데이터그램 패킷 교환 방식***’을 사용한다.

**가상회선 패킷 교환 방식**

- 각 패킷에는 가상회선 식별자가 포함되며 모든 패킷을 전송하면 가상회선이 해제되고 패킷들은 전송된 **‘순서대로’** 도달한다.

**데이터그램 패킷 교환 방식**

- 패킷이 독립적으로 이동하며 최적의 경로를 선택하여 간다.
- 하나의 메시지에서 분할된 여러 패킷은 서로 다른 경로로 전송될 수 있으며 **도착한 순서가 다를수도 있다**.

### TCP 연결 성립 과정

- TCP는 신뢰성을 확보할 때 `3-way handshake` 작업을 진행한다.


![image](https://user-images.githubusercontent.com/81108344/210358890-718b3b0f-2558-40e4-b39a-4672654c3341.png)
1. SYN 단계: 클라이언트는 서버에 클라이언트의 ISN(Initial Sequence Numbers)을 담아 보낸다.
   ISN은 새로운 TCP 연결마다 첫 번째 패킷에 할당된 임의의 시퀀스 번호를 의미한다.
2. SYN + ACK 단계: 서버는 클라이언트의 SYN을 수신하고 서버의 ISN을 보내며 승인번호로 클라이언트의 ISN + 1을 보낸다.
3. ACK 단계: 클라이언트는 서버의 ISN + 1한 값인 승인번호를 담아 ACK를 서버에 보낸다.

### TCP 연결 해제 과정

- 해제할 때는 `4-way handshake` 과정이 발생한다.

![image](https://user-images.githubusercontent.com/81108344/210358898-24d4da8e-e056-4339-ba3c-aace8607c2ad.png)
  **1번**
  클라이언트가 연결을 닫으려고 할 때 **FIN**으로 설정된 세그먼트를 보낸다.
  이후 클라이언트는 FIN_WAIT_1 상태로 들어가고 서버의 응답을 기다린다.

  **2번**
  서버는 클라이언트로 **ACK**라는 승인 세그먼트를 보낸다. 이후 CLOSE_WAIT 상태에 들어간다. 클라이언트가 세그먼트를 받으면 FIN_WAIT_2 상태에 들어간다.

  **3번**
  서버는 **ACK**를 보내고 일정 시간 이후에 클라이언트에 FIN이라는 세그먼트를 보낸다.

  **4번**
  클라이언트는 `TIME_WAIT` 상태가 되며, 다시 서버로 ACK를 보내 서버는 CLOSED 상태가 된다. 이후 클라이언트는 어느 정도 시간을 대기한 후 연결이 닫히고 클라이언트와 서버의 모든 자원의 연결이 해제된다.

  `TIME_WAIT`
  지연 패킷이 발생할 경우를 대비할 수 있으며, 두 장치가 연결이 닫혔는지 확인할 수 있다.
    
  ---


### 인터넷 계층

- 장치로부터 받은 네트워크 패킷을 IP 주소로 지정된 목적지로 전송하기 위해 사용되는 계층
- **IP, ARP, ICMP** 등이 있으며 패킷을 수신해야 할 상대의 주소를 지정하여 데이터를 전달한다.
- 상대방이 제대로 받았는지에 대해 보장하지 않는 **비연결형적**인 특징을 가지고 있다.



---

### 링크 계층

- 전선, 광섬유, 무선 등으로 실질적으로 데이터를 전달하며 장치 간 신호를 주고받는 ‘규칙’을 정하는 계층
- **물리 계층**과 **데이터 링크 계층**으로 나눌 수 있으며 **물리 계층**은 무선 LAN과 유선 LAN을 통해
  0과 1로 이루어진 데이터를 전송한다.
- **데이터 링크 계층**은 ‘이더넷 프레임’을 통해 물리계층의 에러 확인, 흐름 제어, 접근 제어를 담당하는 계층이다.

### 유선 LAN(IEEE802.3)

- 유선 LAN을 이루는 이더넷은 IEEE802.3이라는 프로토콜을 따르며 **전이중화 통신**을 사용한다.

### 전이중화 통신

- 양쪽 장치가 동시에 송수신할 수 있는 방식
- 현대의 고속 이더넷은 이 방식을 기반으로 통신한다.

![image](https://user-images.githubusercontent.com/81108344/210358909-024bf5ae-2971-4c2a-81c5-585c3d81ed46.png)
### CSMA/CD

- 예전 유선 LAN에 사용한, 반이중화 통신 방식
- **데이터를 보낸 이후** 충돌이 발생한다면 일정 시간 이후 재전송하는 방식
- 수신로, 송신로가 각각 존재하지 않고 한 경로를 기반으로 데이터를 보내므로 데이터를 보낼 때 충돌에 대해 대비해야 한다.

## 유선 LAN을 이루는 케이블

- 유선 LAN 케이블로는 TP 케이블(트위스트 페어 케이블), 광섬유 케이블이 존재한다.

### 트위스트 페어 케이블

- 하나의 케이블처럼 보이지만 실제로는 여덟개의 구리선을 두 개씩 꼬아서 묶은 케이블
- 별도의 피복으로 감싸지 않은 UTP 케이블과 실드처리한 STP 케이블로 나뉜다.
- UTP 케이블을 흔히 볼 수 있으며 LAN 케이블이라고 한다.




### 광섬유 케이블

- 레이저를 이용해 통신하므로 장거리, 고속 통신이 가능하다.
- 100Gbps 데이터를 전송한다.

## 무선 LAN(IEEE802.11)

- 무선 LAN 장치는 수신과 송신에 같은 채널을 사용하므로 반이중화 통신을 사용한다.

### 반이중화 통신

- 양쪽 장치는 서로 통신할 수 있지만, **동시에는 통신할 수 없으며** 한 번에 한 방향만 통신할 수 있는 방식

![image](https://user-images.githubusercontent.com/81108344/210358918-338ea658-5d7d-4b7d-bd7f-2a61beb59113.png)
- 장치가 신호를 수신하기 시작하면 전송이 완료될 때까지 기다려야 한다.
- 둘 이상의 장치가 전송 시 충돌이 발생하므로 **충돌 방지 시스템**이 필요하다.

### CSMA/CA

- 데이터를 보내기 전에 캐리어 감지를 해서 사전에 가능한 한 충돌을 방지하는 방식
  1. 데이터를 송신하기 전에 무선 매체를 살핀다.
  2. 캐리어 감지: 회선이 비어 있는지 판단한다.
  3. IFS(Inter FrameSpace): 랜덤 값을 기반으로 정해진 시간만큼 기다리며, 만약 무선 매체가 사용중이면 점차 그 간격을 늘려가며 기다린다.
  4. 데이터 송신

### 와이파이

- 전자기기들이 무선 LAN 신호에 연결할 수 있게 하는 기술
- 사용하려면 무선 접속 장치(AP, Access Point = 공유기)가 있어야 한다.
  - 이를 통해 유선 LAN에 흐르는 신호를 무선 LAN 신호로 바꿔주어 신호가 담는 범위 내에서 무선 인터넷을 사용할 수 있게 된다.

### BSS(Basic Service Set)

- 기본 서비스 집합으로, 단순 공유기를 통해 네트워크에 접속하는 것이 아닌 동일 BSS 내에 있는 AP들과 장치들이 서로통신이 가능한 구조를 말한다.
- 근거리 무선 통신을 제공하고, 하나의 AP만을 기반으로 구축이 되어 있어 사용자가 한 곳에서 다른 곳으로 자유롭게 이동하며 네트워크에 접속하는 것은 불가능하다.

### ESS(Extended Service Set)

- ESS는 하나 이상의 연결된 BSS 그룹이다.
- 장거리무선 통신을 제공하며 BSS보다 더 많은 가용성과 이동성을 지원한다.
- 즉, 사용자는 한 장소에서 다른 장소로 이동하며 중단 없이 네트워크에 계속 연결할 수 있다.


![image](https://user-images.githubusercontent.com/81108344/210358927-a51278b8-c776-42b4-9e22-bb96e5ff68af.png)
### 이더넷 프레임

- **데이터 링크 계층**은 이더넷 프레임을 통해 전달받은 데이터의 에러를 검출하고 **캡슐화**하며 다음과 같은 구조를 가진다.


![image](https://user-images.githubusercontent.com/81108344/210358946-cee4e113-69b8-4e22-96c1-0c2467173f06.png)
- Preamble: 이더넷 프레임이 시작임을 알린다.
- SFD(Strart Frame Delimiter): 다음 바이트부터 MAC 주소 필드가 시작됨을 알린다.
- DMAC, SMAC: 수신, 송신 MAC 주소
- EtherType: 데이터 계층 위의 계층인 IP 프로토콜을 정의한다. ex) IPv4, IPv6
- payload: 전달받은 데이터
- CRC: 에러 확인 비트

### 계층 간 데이터 송수신 과정

- 컴퓨터를 통해 다른 컴퓨터로 데이터를 요청하는 경우


![image](https://user-images.githubusercontent.com/81108344/210358961-1737400e-c3be-417d-b253-e6a714dca16f.png)
1. 애플리케이션 계층에서 전송 계층으로 사용자가 보내는 요청 값이 캡슐화 되어 전달된다.
2. 링크 계층을 통해 해당 서버와 통신
3. 해당 서버의 링크 계층으로부터 애플리케이션 계층까지 비캡슐화 과정을 거쳐 데이터가 전송된다.

### 캡슐화 과정

- 상위 계층의 헤더와 데이터를 하위 계층의 데이터 부분에 포함시키고 해당 계층의 헤더를 삽입하는 과정


![image](https://user-images.githubusercontent.com/81108344/210358974-ab018b87-c3d6-416d-a532-da46e218cebf.png)
- 애플리케이션 계층의 데이터가 전송 계층으로 전달되면서 ‘세그먼트’ 또는 `데이터그램화` 되며 TCP(L4) 헤더가 붙고, 인터넷 계층으로 가면서 `패킷`화되며, 이후 링크 계층으로 전달되면서
  `프레임화` 된다.

### 비캡슐화 과정

- 위의 캡슐화 과정의 역순으로 진행된다. 각 계층의 헤더 부분을 제거하는 과정
- 최종적으로 사용자에게 애플리케이션의 **`PDU`**인 메시지로 전달된다.

## 2.2.2 PDU(Protocol Data Unit)

- 네트워크의 어떠한 계층에서 계층으로 데이터가 전달될 때 한 덩어리의 단위를 PDU라고 한다.
- 제어 관련 정보가 담긴 `헤더`와 데이터를 의미하는 `페이로드`로 구성되어있다.
- 각 계층마다 부르는 명칭이 다르다.
  - 애플리케이션 계층: 메시지
  - 전송 계층: 세그먼트(TCP), 데이터그램(UDP)
  - 인터넷 계층: 패킷
  - 링크 계층: 프레임(데이터 링크 계층), 비트(물리 계층)

- 애플리케이션 계층은 `메시지`를 기반으로 데이터를 전달한다. ⇒ 대표적으로 HTTP의 헤더는 문자열이다.
  - 헤더에 authorization 값 등 다른 값들을 넣는 확장이 용이하다.
- PDU 중 비트로 송수신하는 것이 가장 빠르고 효율성이 높다.
