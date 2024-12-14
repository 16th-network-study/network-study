### HTTP 1.0

특징

- 각 요청 안에 버전 정보가 포함됐다. (HTTP/1.0 이 GET 라인에 붙은 형태)
- 상태 코드 라인이 응답에 추가됐다. 브라우저가 요청에 대한 성공과 실패를 알 수 있고 그 결과에 대한 동작(예, 특정 방법으로 로컬 캐시를 갱신하거나 사용)을 할 수 있게 됐다.
- HTTP 헤더 개념은 요청과 응답 둘 다 도입되어 메타데이터 전송이 가능해졌고 프로토콜이 유연해지고 확장성이 높아졌다.
- Content-type이 추가되어 HTML뿐 아니라 다른 문서를 전송할 수 있게 됐다.

**Request**

```cpp
GET /mypage.html HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)

200 OK
Date: Tue, 15 Nov 1994 08:12:31 GMT
Server: CERN/3.0 libwww/2.17
Content-Type: text/html
<HTML>
A page with an image
  <IMG SRC="/myimage.gif">
</HTML>
```

**Response**

```cpp
GET /myimage.gif HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)

200 OK
Date: Tue, 15 Nov 1994 08:12:32 GMT
Server: CERN/3.0 libwww/2.17
Content-Type: text/gif
(image content)
```

- **단기커넥션 : connection 하나당 1 Request & 1 Response 처리 가능**

<img width="821" alt="2024-12-09_17-24-23" src="https://github.com/user-attachments/assets/b16525cc-0746-4182-a8d8-1e57fa049828">

HTTP 1.0은 단기 커넥션을 지원한다. 즉 하나의 Connection당 1개의 요청만 처리 가능하다. HTTP 1.0의 경우 TCP 위에서 동작하기 때문에 매번 요청마다 연결을 새로 수립하고 끊어야 하기 때문에 성능 저하가 크다.

### HTTP 0.9와 1.0의 변화점 요약
- Get 메서드 외의 다른 메서드들도 지원이 가능하게 되었다.
- HTTP 헤더나 상태 코드에 대해서도 다양하게 지원을 한다.
- HTML 파일 외의 멀티미디어 등의 전송도 지원하게 되었다.

---

### HTTP 1.1

특징

- 지속 연결 (Persistent Connection) 지원 : 지정한 timeout 동안 연속적인 요청 사이에 커넥션을 닫지 않고 재사용한다. 1.0과 다르게 매 요청마다 TCP 연결 수립을 할 필요가 없다.
    - HTTP 1.1부터 keep-alive가 기본 세팅되고 Persistent Connection 연결이 된다. HTTP 1.0의 경우 응답할 때마다 Connection: close를 헤더에 같이 보내서 연결을 끊는다.
      <img width="680" alt="2024-12-09_17-25-26" src="https://github.com/user-attachments/assets/d713b1e9-0697-45e9-bd65-5e08de552fd6">
-
    - max : keep-alive로 주고받을 수 있는 request 최대 개수
    - timeout : keep-alive 유지 시간

- 파이프라이닝 : 이전 요청에 대한 응답이 완전히 전송되기 전에 다음 전송을 가능하게 한다. 즉 여러 요청을 연속적으로 보내 그 순서에 맞춰 응답을 받는 방식으로 지연 시간을 줄인다. (사장된 기술)

<img width="810" alt="2024-12-09_17-25-45" src="https://github.com/user-attachments/assets/4f0637d1-c376-457f-8b89-a89a971abc59">

위 그림을 보면 Client에서 요청에 대한 응답이 오기 전에 다음 요청을 보내는 것을 볼 수 있다. 하지만 서버는 반드시 요청을 받은 순서대로 FIFO로 처리해야 한다. (L5 계층 애플리케이션 서버의 처리 순서가 FIFO인 것을 의미)

- FIFO로 처리하지 않으면 HTTP 1.1에서는 Request-Response를 매핑할 방법이 없다. (식별자가 없음.)
- HTTP 1.1은 TCP위에서 동작하기 때문에 Request의 순서는 보장된 상태로 L5로 올라간다.

→ 결국 먼저 보낸 요청이 오래 걸리면 뒤의 요청이 밀리는 문제가 발생한다. 이를 Head Of Line Blocking 문제라고 한다

<img width="864" alt="2024-12-09_17-26-09" src="https://github.com/user-attachments/assets/789df2c6-6b0b-4b4c-beb7-a9784d95c360">

이 문제 외에도 HTTP 1.1의 파이프라이닝은 여러 문제가 있어서 사장되고 HTTP 2.0의 멀티플렉싱 알고리즘으로 대체됐다.

- Domain sharing : 파이프라이닝을 대체하기 위해서 나온 기술로 하나의 도메인에 대해 여러 개의 connection을 생성하여 병렬로 요청을 보내고 받는 방식이다. 도메인당 6~13개의 TCP 연결을 동시 생성하여 여러 리소스를 한번에 받는 방식이다.

이 방식의 경우 결국 TCP 연결을 추가로 여러개 만들어야 하고 도메인당 연결 수 제한도 있기 때문에 임시 방편에 불가하다.

### HTTP 1.0과 HTTP 1.1의 Keep-alive
- HTTP 1.0에서도 TCP 연결의 지속을 위한 Keep-alive를 커넥션 과정에서 넣을 수 있다. HTTP 1.1은 단지 default 옵션인 것 뿐이다.
- Keep-alive를 계속 유지하고 있는 건 커넥션 낭비일 수 있다. 따라서, Connection을 유지하는 동안엔 Keep-alive를 보내나, 끝났을 땐 재사용이 필요없음을 close로 알려준다.

---

### HTTP 2.0

- Binary Framing Layer : HTTP 1.1에서는 text로 데이터가 전송됐는데 HTTP 2.0에서는 binary frame으로 인코딩되서 전송된다.
    - 기존 text 방식으로 HTTP 메세지를 보내는 방식은 본문은 압축이 되지만 헤더는 압축이 되지 않으며 헤더 중복값이 있다는 문제 때문에 HTTP 2.0에서는 바이너리로 변경 되었다. 또한 기존에는 헤더와 바디를 \r 이나\n 과 같은 개행 문자로 구분했는데 HTTP/2.0에서 부터는 헤더와 바디가 layer로 구분된다.이로인해 데이터 파싱 및 전송 속도가 증가하였고 오류 발생 가능성이 줄어들었다.

<img width="785" alt="2024-12-09_17-27-04" src="https://github.com/user-attachments/assets/f9acace7-3a75-4a1d-a81a-5f354780479e">

- Stream 과 Frame : HTTP 1.1에서는 전체 요청, 응답이 Message (그냥 Text)로 구성됐는데 HTTP 2.0에서는 Frame, Stream 이라는 단위가 추가됐다.
    - Frame 은 통신 최소 단위로 헤더, 데이터가 들어있다.
    - Stream은 Connection 내에서 양방향으로 Message를 주고 받는 흐름이다. (고유 식별자가 부여됨, 이를 통해서 Request - Repsonse를 매핑할 수 있음)


<img width="812" alt="2024-12-09_17-27-24" src="https://github.com/user-attachments/assets/ac6d932e-fc34-445d-8960-1a9c8f97978b">

위 그림을 보면 알 수 있듯이 하나의 Connection에 여러 스트림이 존재하고 데이터를 병렬적으로 주고 받을 수 있다. 그리고 Stream은 고유 식별자가 있기 때문에 요청, 응답을 구분할 수 있다.)

- HTTP 1.1에서 Request - Response를 매핑하지 못했던 것과는 대조됨.
- 클라이언트(request)는 홀수, 서버(response)는 짝수이다. 이를 통해 요청, 응답을 구분
- 한번 사용한 스트림은 재사용 불가하고 식별자가 고갈되면 새로 커넥션을 맺는다.

- Multiplexing

<img width="827" alt="2024-12-09_17-27-57" src="https://github.com/user-attachments/assets/a848258b-7d1b-4fb2-96ce-19eb6169334d">


HTTP 헤더 메시지를 바이너리 형태의 프레임으로 나누고 하나의 커넥션으로 동시에 여러 메시지 스트림을 응답 순서에 상관없이 주고 받는 것을 멀티플렉싱이라고 한다.

- 이게 가능한 이유는 스트림에 고유 번호가 붙기 때문이다. 기존 HTTP 1.1 에서는 Request-Response를 서버에서 순서대로 처리하지 않으면 응답이 왔을 때 어떤 요청에 대한 것인지 알 방법이 없다.

→ HTTP 1.1 보다는 성능 개선을 했지만 본질적으로 TCP 위에서 동작하기 때문에 L4 계층에서 TCP 패킷이 손실되거나 오류가 생기면 HOL Blocking 문제가 발생한다.

- Server Push : 클라이언트 요청에 대해 나중에 필요할 거 같은 리소스를 함께 보내준다.
    - index.html을 파싱하여 클라이언트가 따로 요청하지 않아도 서버가 알아서 내부의 JS나 CSS 같은 필요한 자원을 미리 보내준다.
- HTTP Header Data Compression : HTTP 2.0 에서는 HTTP 메시지 헤더를 압축해서 전송한다. 그리고 중복되는 헤더를 필터링해서 보내지 않음으로써 데이터를 절약했다.


#### HTTP 2.0으로의 변화와 한계점 요약
- 기존의 body 부분 뿐만 아니라, Header 부분도 압축할 수 있게 바뀌었다.
- Stream과 Multiplexing을 지원하여 데이터를 병렬적으로 주고 받을 수 있다.
- 전송 계층으로 TCP를 사용한다는 것 때문에 상위 계층에서 Frame을 개선하려고 해도 전송 계층 상에서 문제가 생기면 HOL Blocking이 일어나는 건 동일하다.

---

### HTTP 3.0

HTTP2.0 까지와는 다르게 HTTP 3.0은 UDP 기반의 프토콜인 QUIC을 사용하여 통신한다.
<img width="701" alt="2024-12-12_00-32-06" src="https://github.com/user-attachments/assets/582f2a16-fc92-4eb9-b822-c78e52b52be9" />

배경

HTTP 1.1, 2.0의 경우 TCP 위에서 동작하기 때문에 HoL Blocking 문제가 여전히 발생한다. 중간에 segment 하나가 제대로 전송되지 못한 경우 뒤의 segment도 처리되지 못하기 때문이다. 이는 TCP를 채택하는 이상 어쩔 수 없다. → 전송 계층에서 UDP를 쓰고 응용 계층에서 TCP에서 제공하는 기능들을 구현함. QUIC

장점

- 연결 시 레이턴시 감소

최초 연결

<img width="703" alt="2024-12-12_00-32-31" src="https://github.com/user-attachments/assets/746185b3-35b3-4ae9-8015-12bfd0d9058a" />


기존 TCP + TLS 1.3 의 경우 2RTT가 요구됐는데 UDP 기반의 QUIC 프로토콜을 사용하면 1RTT로 가능하다.

→ QUIC 안에 TLS 인증서를 내포하고 있기 때문에 최초 연결 설정에 필요한 인증 정보와 함께 데이터를 전송하고 요청에 대한 응답을 클라이언트가 받으면 다음부터 바로 통신 가능.

이 때 최초 연결할 때는 서버의 Connection ID를 사용하여 생성한 Initial Key를 사용하여 통신을 암호화한다.

이후 재연결

<img width="701" alt="2024-12-12_00-32-51" src="https://github.com/user-attachments/assets/14834157-3f91-40fe-ac66-aeb12e41732c" />

두번째 연결부터는 캐싱해둔 키를 사용해서 바로 전송 가능하다. (0RTT)

- 독립 스트림 (TCP의 단일 스트림의 문제점을 해결)

<img width="649" alt="2024-12-12_00-33-10" src="https://github.com/user-attachments/assets/cce1ac4a-42a8-4308-8848-0d1f006fee50" />

TCP 위에서 동작하는 HTTP/2의 경우 2번에 문제가 발생하면 뒤에 전송된 (4,5,6), (7,8)도 지연된다.

→ (TCP HoL Blocking 문제)

QUIC의 경우 2번에 문제가 발생해도 (4,5,6), (7,8)은 지연없이 전송된다.

---

#### 참고자료
* [inpa http 0.9~1.1](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-09-HTTP-30-%EA%B9%8C%EC%A7%80-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0)
* [inpa http 2.0](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-20-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0-%EC%9D%B4%EC%A0%9C%EB%8A%94-%ED%99%95%EC%8B%A4%ED%9E%88-%EC%9D%B4%ED%95%B4%ED%95%98%EC%9E%90)
* [inpa http 3.0](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-30-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0-%EC%9D%B4%EC%A0%9C%EB%8A%94-%ED%99%95%EC%8B%A4%ED%9E%88-%EC%9D%B4%ED%95%B4%ED%95%98%EC%9E%90)
* [https://developer.mozilla.org/ko/docs/Web/HTTP/Evolution_of_HTTP](https://developer.mozilla.org/ko/docs/Web/HTTP/Evolution_of_HTTP)
* [https://yozm.wishket.com/magazine/detail/1686/](https://yozm.wishket.com/magazine/detail/1686/)