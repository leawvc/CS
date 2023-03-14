# 2.5 HTTP

- HTTP는 **애플리케이션 계층**으로서 웹 서비스 통신에 사용된다

## **2.5.1 HTTP/1.0**

- 기본적으로 한 연결당 하나의 요청을 처리하도록 설계되었다.
- 서버로부터 **파일을 가져올 때 마다** TCP의 3-웨이 핸드셰이크를 계속해서 열어야 하기에 RTT가 증가하는 단점이 있었다.

![https://blog.kakaocdn.net/dn/nZvtM/btrQj7Ciehv/yX2cPj1qGkdvtHqjxz1T8k/img.png](https://blog.kakaocdn.net/dn/nZvtM/btrQj7Ciehv/yX2cPj1qGkdvtHqjxz1T8k/img.png)

### **RTT의 증가를 해결하기 위한 방법**

- 이미지 스플리팅, 코드 압축, 이미지 Base64 인코딩을 사용

### 이미지 스플리팅

- 이미지가 합쳐져 있는 하나의 이미지를 다운받고, 이를 기반으로 background-image의 포지션을 이용해 이미지를 표기하는 방법

### **코드 압축**

- 코드를 압축해서 개행 문자, 빈칸을 없애서 코드의 크기를 최소화하는 방법

```jsx
<<before>>
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req,res) => {
  	res.send('Hello World!')
})

app.listen(port, () => {
	console.log('Example app listening on port ${port}')
})

<<after>>
const express=require('express'),app=express(),port=3e3;app.
get('/',(e,p)=>{p.send('Hello World!')}),app.listen(3e3,()=>{console.
log('Example app listening on port 3000')});
```

### **이미지 Base64 인코딩**

- 이미지 파일을 64진법으로 이뤄진 문자열로 인코딩하는 방법
- 서버와의 연결을 열고 이미지에 대해 http 요청을 할 필요가 없다.
- 다만, 크기가 약 37%정도 더 커지는 단점이 있다.
- 최근 S3나 azure 같은 이미지 호스팅 서비스를 이용해 큰 코스트 소비 없이 이미지를 불러올 수 있기에 위 방법을 사용하지 않는다.

## **2.5.2 HTTP/1.1**

- HTTP/1.0에서 발전한 것이 HTTP/1.1이다.
- 사용할 때 마다 TCP를 연결하는 것이 아닌 **한 번** TCP 초기화를 한 후 **keep-alive**라는 옵션으로 여러 개의 파일을 송수신할 수 있게 바뀌었다.
- 아래 그림처럼 최초 TCP 3-way handshake가 발생하면 다음부터는 발생하지 않는 것을 볼 수 있다.
- **하지만** 문서 안에 포함된 다수의 리소스를 처리하려면 요청할 리소스 개수에 비례해 대기 시간이 길어지는 단점이 있다.
    - **HTTP/1.1**는 기본적으로 Connection당 하나의 요청을 처리 하도록 설계했고, 그 때문에 동시전송이 불가능하고 요청과 응답이 순차적으로 이루어 진다.

![https://blog.kakaocdn.net/dn/bexxUK/btrQlHWJCq2/cI3vJ1RGvVgG4wXJF3Yzf1/img.png](https://blog.kakaocdn.net/dn/bexxUK/btrQlHWJCq2/cI3vJ1RGvVgG4wXJF3Yzf1/img.png)

---

### **HOL Blocking**

- 네트워크에서 같은 큐에 있는 패킷이 그 첫 번째 패킷에 의해 지연될 때 발생하는 성능 저하 현상

![https://blog.kakaocdn.net/dn/cKptUz/btrQjkWfwTX/UUdY2rdAwaYTat5ceTEHF0/img.png](https://blog.kakaocdn.net/dn/cKptUz/btrQjkWfwTX/UUdY2rdAwaYTat5ceTEHF0/img.png)

### **무거운 헤더 구조**

- HTTP/1.1의 헤더에는 쿠키 등 많은 메타데이터가 압축되지 않은 채 들어 있어 무겁다.

## **2.5.3 HTTP/2**

- 구글의 SPDY를 기반으로 HTTP/1.1 의 단점을 개선한 차세대 웹 프로토콜
- HTTP/1.x보다 지연 시간을 줄이고 응답 시간을 더 빠르게 할 수 있다.
- **멀티플렉싱, 헤더 압축, 서버 푸시, 요청의 우선순위 처리**를 지원하는 프로토콜이다.

![https://blog.kakaocdn.net/dn/5En1Y/btrQkt6gSFc/wxkKXzz49ZV8e5q6zCN4S0/img.png](https://blog.kakaocdn.net/dn/5En1Y/btrQkt6gSFc/wxkKXzz49ZV8e5q6zCN4S0/img.png)

### **멀티플렉싱**

- 여러 개의 스트림을 사용해 송수신한다.
- 특정 스트림의 패킷이 손실되었다 해도 해당 스트림에만 영향을 미친다. 나머지 스트림은 멀쩡하게 동작가능

![https://blog.kakaocdn.net/dn/bPwKs8/btrQlO2y5Zg/tNfMK60FBjDvnu7RpgwDMK/img.png](https://blog.kakaocdn.net/dn/bPwKs8/btrQlO2y5Zg/tNfMK60FBjDvnu7RpgwDMK/img.png)

- 애플리케이션에서 받은 메시지를 독립된 프레임으로 조각내 서로 송수신 후 다시 조립해 데이터를 주고받는다
- 단일 연결을 사용해 병렬로 여러 요청을 받고 응답을 줄 수 있고, 이와 같이 진행시 HOL Blocking을 해결가능하다.

![https://blog.kakaocdn.net/dn/cTm56n/btrQj4TcVUI/tUUJODyGY8spvStu0XMujk/img.png](https://blog.kakaocdn.net/dn/cTm56n/btrQj4TcVUI/tUUJODyGY8spvStu0XMujk/img.png)

### **헤더 압축**

- 허프만 코딩 압축 알고리즘을 사용하는 HPACK 압축 형식으로 헤더 압축 문제를 해결

  **허프만 코딩**

    - 문자열을 문자 단위로 쪼개 빈도수를 세어 빈도가 높은 정보는 적은 비트 수를 사용해 표현한다.
    - 빈도가 낮은 정보는 비트 수를 많이 사용해 표현해 전체 데이터의 표현에 필요한 비트양을 줄이는 원리이다.

### **서버 푸시**

- HTTP/1.1에서는 클라이언트가 서버에 요청을 해야 파일을 다운 받을 수 있었다면, HTTP/2는 클라이언트 요청 없이 서버가 바로 리소스를 푸시할 수 있다.
- 사전에 리소스 요청을 예측하여, 서버에서 푸쉬를 해줌으로써 성능을 개선

![https://blog.kakaocdn.net/dn/bAytA0/btrQkXyXnwc/fKxSmaSSAvfkGe7p8obOE0/img.png](https://blog.kakaocdn.net/dn/bAytA0/btrQkXyXnwc/fKxSmaSSAvfkGe7p8obOE0/img.png)

# 2.5.4 HTTPS

- HTTP/2는 HTTPS 위에서 동작한다.


![image](https://user-images.githubusercontent.com/81108344/210361196-44a26c9c-5118-4c89-b04e-2d572f5425db.png)
- HTTPS는 애플리케이션 계층과 전송 계층 사이에 신뢰 계층인 **`SSL/TLS 계층`**을 넣은 신뢰할 수 있는 HTTP 요청을 말하며 이를 통해 '**통신을 암호화**'한다.

## **SSL/TLS**

- 전송 계층에서 보안을 제공하는 프로토콜
- 클라이언트와 서버가 통신할 때 SSL/TLS를 통해 제3자가 메시지를 도청하거나 변조하지 못하도록 한다.

![https://blog.kakaocdn.net/dn/beKj22/btrQpFSTmN3/w6anv0XkDX74Kr6o6hFMfk/img.png](https://blog.kakaocdn.net/dn/beKj22/btrQpFSTmN3/w6anv0XkDX74Kr6o6hFMfk/img.png)

- 위처럼 SSL/TLS를 통해 공격자가 서버인 척하며 사용자 정보를 가로채는 네트워크상의 '인터셉터'를 방지할 수 있다.
- SSL/TLS는 **`보안 세션`**을 기반으로 데이터를 암호화하며 보안 세션이 만들어질 때 인증 메커니즘, 키 교환 알고리즘, 해싱 알고리즘이 사용된다.

### 보안 세션

- 보안이 시작되고 끝나는 동안 유지되는 세션으로, SSL/TLS는 **handshake**를 통해 보안 세션을 생성하고 이를 기반으로 상태 정보 등을 공유한다.

---

### CA 발급 과정(서버 ↔ CA)

- 자신의 서비스에 CA 인증서(SSL 인증서)를 발급받으려면 자신의 **사이트 정보와 공개키**를 CA에 제출해야한다.
- 이후 CA는 공개키를 해시한 값인 지문(finger print)을 사용하는 **CA 비밀키** 등을 기반으로 CA 인증서를 발급한다.

```diff
-개인키
  비밀키라고 하며, 개인이 소유하고 있는 키이자 반드시 자신만이 소유해야하는 키

-공개키
  공개되어 있는 키
```

### **TLS(1.3)의 handshake(1-RTT)**

![image](https://user-images.githubusercontent.com/81108344/210361205-67385c08-9734-46bc-a12b-6a28a93ef16a.png)
- 클라이언트와 서버와 키를 공유하고 이를 기반으로 인증, 인증 확인 등의 작업이 일어나는 단 한 번의 **1-RTT**가 생긴 후 데이터를 송수신한다.

- 클라이언트에서 **사이퍼 슈트(cypher suites)**를 서버에 전달한다**(Client Hello)**.
- 서버는 받은 사이퍼 슈트의 암호화 알고리즘 리스트를 제공할 수 있는지 확인한다.
    - 제공 가능하면 서버에서 클라이언트로 인증서를 보내는 **인증 메커니즘**이 시작되고 이후 해싱 알고리즘 등으로 암호화된 데이터의 송수신이 시작된다.

### **사이퍼 슈트**

- 프로토콜, AEAD 사이퍼 모드, 해싱 알고리즘이 나열된 규약

**종류**

- TLS_AES_128_GCM_SHA256
- TLS_AES_256_GCM_SHA384
- TLS_CHACHA20_POLY1305_SHA256
- TLS_AES_128_CCM_SHA256
- TLS_AES_128_CCM_8_SHA256

ex) `TLS_AES_128_GCM_SHA256`

TLS는 프로토콜, AES_128_GCM은 AEAD 사이퍼 모드, SHA256은 해싱 알고리즘을 뜻한다.

### **인증 메커니즘(certificate)**

- CA(Certificate Authorities)에서 발급한 인증서를 기반으로 이루어진다.
- **CA에서 발급한 인증서**는 안전한 연결을 시작하는데 있어 필요한 **'공개키'**를 **클라이언트**에 제공하고, 사용자가 접속한 '서버가 신뢰'할 수 있는 서버임을 보장한다. SSL인증서는 서비스 정보, 공개키, 지문, 디지털 서명 등으로 이뤄져 있다.
    - 우리가 사용하는 일반적인 브라우저는 `신뢰할 수 있는 CA 기관의 리스트`와 `해당 기관의 공개키`를 이미 가지고 있다. 그래서 클라이언트는 내장된 CA의 공개 키를 활용하여 인증서를 복호화함으로써 인증서를 검증한 뒤, 서버의 공개 키를 가져올 수 있다.
- CA는 신뢰성이 엄격한 공인 기업들만 참여 가능하며, 대표 기업으로는 Comodo, GoDaddy, GlobalSign, 아마존 등이 있다.

### 암호화 알고리즘(TLS 1.3)

**키 교환 암호화 알고리즘**

- 대수곡선 기반의 ECDHE(Elliptic Curve **Diffie-Hellman** Ephermeral)
- 모듈식 기반의 DHE(**Diffie-Hellman** Ephemeral)

**둘 다 디피-헬만의 방식을 근간으로 만들어짐**

- 키 교환 방식으로 RSA를 배제해서 옵션 또한 최대한 줄였다. 그래서 클라이언트는 시작부터 키 교환 방식으로 DH를 사용한다고 가정할 수 있게 됐다. RSA나 그 외의 방식까지 고려해서 서버에게 여러 가지 키 교환 방식의 지원 여부를 물어봐야 했던 과거에 비해 훨씬 간단하게 추측할 수 있다.
- 암호화 및 키 교환 방식 모두에서 줄어든 경우의 수를 **Client Hello 에서 한번에 전송**
  한다. 이를 key_share라고 한다. ⇒ 따라서 TLS 1.3 버전부터 1-RTT로 연결이 가능하다.

```
y = g^x mod p
```

- g와 x와 p를 알면 y는 쉽게 구할 수 있지만, g와 y와 p만 안다면 x를 구하기는 어렵다는 원리

![image](https://user-images.githubusercontent.com/81108344/210361210-f152ee5c-bd8e-4316-a3b7-71a15bef8310.png)
- 위처럼 처음 공개 값을 공유하고 각자의 비밀 값과 혼합 한 후 혼합 값을 공유한다.
- 이후 각자의 비밀 값과 또 혼합한다. 그 후에 공통의 암호키가 생성된다.
- 클라이언트와 서버 모두 개인키와 공개키를 생성하고 서로 공개키를 보내고 공개키와 개인키를 결합해 **PSK**(**pre_shared_key**, 사전 합의된 비밀키)가 생성된다. 공격자가 개인키/공개키를 가져도 PSK가 없기에 아무것도 할 수 없다.

### **TLS(1.3)의 0-RTT handshake**

- **세션 재개 시 0-RTT (zero-Round Trip Time)**를 지원한다. (단축 협상)
- 첫 TLS Handshake 완료 후 서버와 클라이언트에 공유된 암호 키 (psk)를 로컬에 저장하면,
  클라이언트는 이 키를 사용하여 첫 번째 요청에서 바로 http를 포함하여 서버로 전송한다.

![https://blog.kakaocdn.net/dn/dgDNyJ/btqR9naZ7m7/NBiJPQmJ9Jw9AplmFcKiok/img.png](https://blog.kakaocdn.net/dn/dgDNyJ/btqR9naZ7m7/NBiJPQmJ9Jw9AplmFcKiok/img.png)

- 1-RTT 와 Handshake 과정에서 보이는 차이는 **Client Hello내 확장 필드로 존재하는 early_data 의 유무**이다.
    - 0-RTT 의 경우 early_data 필드가 존재한다.

- 0-RTT를 통해 암호화 **연결 속도를 향상**시킬 수 있고, 리소스 절약이 가능하다는 장점이 있다.
- 그러나 **보안적인 측면에서는 허점**이 될 수 있는데, 예를 들어 **replay-attack**이 가능한 취약점이 될 수 있다.
- 공격자가 0-RTT 연결 재시작이 되었을 때의 요청을 가로챌 수 있다면, 암호화에 사용된 개인키  등을 알 수 없으므로 내용을 해독해서 사용자 정보를 얻을 수는 없지만 **동일 요청을 반복적으로 재현**하는 등의 공격이 가능해진다. (계좌에서 인출 반복 등)
- **0-RTT는 보안을 위한 옵션이 아니며, TLS1.3을 지원하는 웹 서버에서 0-RTT 설정은 디폴트가 off다.**

### **해싱 알고리즘**

- 데이터를 추정하기 힘든 더 작고, 섞여 있는 조각으로 만드는 알고리즘
- SSL/TLS는 해싱 알고리즘으로  SHA-256 알고리즘과 SHA-384 알고리즘을 사용한다.

### **SHA-256 알고리즘**

- 해시 함수의 결괏값이 256비트인 알고리즘
- 해싱을 해야 할 메시지에 1을 추가하는 등 전처리를 하고 전처리된 메시지를 기반으로 해시를 반환한다.

```diff
-해시
다양한 길이를 가진 데이터를 고정된 길이를 가진 데이터로 매핑(mapping)한 값

-해싱
임의의 데이터를 해시로 바꿔주는 일, 해시 함수가 이를 담당

-해시 함수
임의의 데이터를 입력받아 일정 길이의 데이터로 바꿔주는 함수
```

## **SEO에도 도움이 되는 HTTPS**

**SEO(Search Engine Optimization, 검색엔진 최적화)**

- 검색엔진으로 웹 사이트를 검색할 때 그 결과를 페이지 상단에 노출해 많은 사람이 볼 수 있도록 최적화하는 방법

### **SEO 방법**

- 캐노니컬 설정, 메타 설정, 페이지 속도 개선, 사이트맵 관리 등

### **캐노니컬 설정**

- 아래 태그가 설정된 페이지가 어떤 URL의 사본임을 검색엔진에게 알려주는 것

```
<link rel="canonical" href="https://example.com/page2.php" />
```

## **HTTPS 구축 방법**

**1. 직접 CA에서 구매한 인증키를 기반으로 HTTPS 서비스를 구축**

**2. 서버 앞단의 HTTPS를 제공하는 로드밸런서를 사용**

**3. 서버 앞단에 HTTPS를 제공하는 CDN을 두어서 구축**

**CDN이란** (Content Delivery Network, 콘텐츠 전송 네트워크)

- 지리적으로 분산된 여러 개의 서버
- 웹 페이지, 이미지, 비디오 등의 콘텐츠를 사용자의 물리적 위치와 가까운 프록시 서버에 캐싱

---

## **2.5.5 HTTP/3**

- HTTP/3는 HTTP/1.1 및 HTTP/2 와 함께 WWW에서 정보를 교환하는데 사용되는 HTTP의 세 번째 버전
- TCP 위에서 돌아가는 HTTP/2와 달리 HTTP/3은 **`QUIC`**라는 계층 위에서 돌아가며, TCP 기반이 아닌 **`UDP 기반`**으로 돌아간다.

![https://blog.kakaocdn.net/dn/kxepo/btrQqKr5QKy/qUVUExx0ugdeymVVCra3p1/img.png](https://blog.kakaocdn.net/dn/kxepo/btrQqKr5QKy/qUVUExx0ugdeymVVCra3p1/img.png)

### 초**기 연결 설정 시 지연 시간 감소**

- QUIC은 TCP를 사용하지 않기 때문에 통신을 시작할 때 번거로운 3-웨이 핸드셰이크 과정을 안거친다.


![image](https://user-images.githubusercontent.com/81108344/210361222-ef732f8d-f0a4-42dd-84aa-f162d950e60f.png)
- QUIC는 첫 연결 설정에 1-RTT만 소요되는데, 이는 클라이언트가 서버에 신호를 한 번 주면, 서버도 거기에 응해 바로 본 통신을 시작할 수 있다는 것이다.

### QUIC(Quick UDP Internet Connections)

- QUIC은 순방향 오류 수정 메커니즘(FEC, Forword Error Correction)이 적용된다.
- 전송한 패킷이 손실되면 수신 측에서 에러를 검출해 수정하는 방식이다.
  ⇒ 열악한 네트워크 환경에서도 낮은 패킷 손실률을 자랑한다.


```
FEC (순방향오류제어, Forward Error Correction)

ㅇ 송신측이 전송할 문자나 프레임에 부가적 정보(☞Redundancy)를 첨가하여 전송하고,
ㅇ 수신측이 에러를 발견 시 이 부가적 정보로 에러 검출 및 에러 정정을 하는 방식

ㅇ 주요 용도
- 송신측이 한 곳이고 수신측이 여러 곳일 때, 재전송/되돌려 보내는 피드백이 어려운 곳,
채널 환경이 열악한 곳에서, 또는 높은 신뢰성이 요구되는 곳 등

ㅇ 실시간 처리 및 높은 처리율을 제공
-오류가 발생하여도 재전송 요구 없이 오류 수정이 가능하므로


ㅇFEC (전진에러수정) : 수신측이 에러 자체 정정
ㅇBEC (후진에러수정) : 에러 발생 데이터에 대해 송신측에 재전송(ARQ)을 요구
```
