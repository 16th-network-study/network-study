### 1xx: 요청이 수신되어 처리 중이라는 의미를 가진다. 잘 사용되지 않지만 101 Switching Protocol의 경우 웹소켓 연결에 사용되기 때문에 간단하게 정리함.

Switching Protocols → 서버가 프로토콜을 전환하고 있다는 의미

1. 클라이언트가 upgrade 헤더에 프로토콜을 지정하고 요청을 보낸다.
2. 서버는 프로토콜 교체를 승인한다.

<img width="740" alt="2024-12-09_14-39-22" src="https://github.com/user-attachments/assets/1d658aa4-c6c7-41a7-aec7-053194781eb2">

웹소켓 사용을 위해 Upgrade 헤더에 프로토콜을 명시한다.
<img width="757" alt="2024-12-09_14-39-56" src="https://github.com/user-attachments/assets/b697326b-2ef1-4f72-b7a8-f604c8315c4b">

웹소켓을 지원할 경우 101 응답과 함께 웹소켓으로 프로토콜 스위치를 진행한다.
<img width="757" alt="2024-12-09_14-42-24" src="https://github.com/user-attachments/assets/9744cba0-5b10-450b-9766-3f0d69a07b98">

### 2xx: 요청이 정상적으로 처리되었다는 의미를 가진다.

- 200 OK : 클라이언트의 요청을 서버가 정상적으로 처리
- 201 Created : 클라이언트 요청을 정상 처리했고 새로운 리소스가 생성됨. 응답의 Location 헤더에 자원 경로 명시
- 204 No Content : 클라이언트의 요청이 정상적이고 제공할 컨텐츠가 업을 때 사용.
- etc..

### 3xx: 리다이렉션을 의미하고 요청을 완료하려면 추가적인 작업이 요구됨을 의미한다.

- 리다이렉션 : 클라이언트가 요청한 URL에 대해 다른 URL을 다시 지시하여 다른 주소로 이동할 수 있게 하는 기술. 총 3가지로 분류 가능
    1. 영구 리다이렉션 : 특정 리소스의 URL이 영구적으로 이동
    2. 일시 리다이렉션 : 특정 리소스의 URL이 일시적으로 이동
    3. 특수 리다이렉션 : 캐시를 활용할 것인지 여부

<img width="765" alt="2024-12-09_14-43-07" src="https://github.com/user-attachments/assets/9c06d870-95eb-41d4-a9a4-9cbd4f7fcf3d">

- 300 Multiple Choices : 요청에 대해서 둘 이상의 가능한 응답이 존재할 때 사용한다. 어떤 서버가 하나의 HTML 문서를 영어, 한국어 모두로 제공하는 경우가 있다. 응답 중 하나를 선택하는 표준이 없기 때문에 실무에서 거의 사용되지 않는다.
- 301 Moved Permanently : 요청된 자원이 Location 헤더가 지정한 URL로 이동되었음을 의미한다. 해당 URL로 리다이렉션 되면 요청 메서드가 GET으로 변경되고 본문이 제거될 수 있다.

<img width="723" alt="2024-12-09_14-43-28" src="https://github.com/user-attachments/assets/8b1500e5-fac3-4277-a044-9130a58b37a4">

301 GET 요청 흐름

1. 클라이언트가 URL : /event, Method : GET HTTP 요청을 한다.
2. 서버는 Location 헤더에 이동한 경로 (/new-event)와 함께 301 응답을 내린다.
3. 클라이언트는 이를 받아서 /new-event 경로로 GET요청을 하고 적절한 응답을 받는다

**301 POST 요청 흐름 (문제 상황)**

<img width="782" alt="2024-12-09_14-43-45" src="https://github.com/user-attachments/assets/cfe632a4-e045-444c-aea4-8bc70e940b7d">


1. 클라이언트가 URL : /event, Method : POST HTTP 요청을 보낸다.
2. 서버는 Location 헤더에 /new-event 값과 함께 301 응답을 내린다.
3. 클라이언트는 /new-event 로 GET 요청을 보내게 된다.
    1. 본문의 name=hello&age=20도 날아간다.
    2. POST 요청이 아닌 GET으로 요청이 날아간다.

   → 거의 대부분의 브라우저가 301에 대해서 GET으로 본문 제거하고 보내도록 구현되어 있다.


- 302 found : 요청된 리소스가 Location 헤더가 지정한 URL로 일시적으로 이동했음을 나타낸다. GET으로 변할 지 여부가 브라우저에 따라 다르기 때문에 GET으로 요청 변경을 원하는 경우 303을 쓰는게 좋다.

  → 301, 302차이에 대해서 검색 엔진, 검색 봇이 영향을 받는다.


PRG 패턴 (Post - Redirect - Get)
<img width="772" alt="2024-12-09_14-44-04" src="https://github.com/user-attachments/assets/fcffb277-ad77-408a-8ba3-4de1e79c33b0">

기존에는 POST 요청을 하고 사용자가 페이지를 Refresh하면 POST 요청이 한 번 더 들어가는 문제가 있었다. PRG 패턴에서는 POST 요청이 성공하면 301(2)로 응답하여 페이지를 Refresh 하더라도 POST 요청이 한 번 더 들어가는 것이 아니라 GET요청이 가게 함으로써 리소스를 중복 생성하는 문제를 방지했다.

- 303 See Other : 302와 같은데 차이점은 요청 메서드 변경(GET)과 본문 제거를 무조건 하는 것을 보장한다.
- 304 Not Modified : 자원이 수정되지 않았으니 캐시를 사용하라는 의미이다. 클라이언트는 캐시에서 리소스를 재사용하면 되기 때문에 이를 통해서 네트워크 트래픽을 줄일 수 있다. (CSS, js, img와 같은 정적 콘텐츠를 효율적으로 전달하기 위해 CDN에서 자주 사용)
- 307 Temporary Redirect : 302와 동일한데 요청 메서드와 바디의 값을 유지한다.
- 308 Permanent Redirect : 301과 동일한데 메서드, 바디를 유지한다.

#### 300번대 응답 코드에 대한 정보 요약
- 검색 엔진은 HTTP 응답 코드를 통해서 색인을 결정한다. 301, 308번은 검색 엔진의 색인을 변경하며 302,303, 307번은 변경하지 않는다.
- 301번에서는 요청이 GET으로 바뀌고 본문이 제거되는 문제가 있으니 해당 응답 시 조심하자.
- 300번대 응답에서는 Redirect나 다른 리소스로 안내하는 경우가 많다. 이 때 지속적으로 다른 장소로의 호출이 반복될 수 있음에 주의하자.(무한 루프 문제 발생 가능)
- 오래된 캐시 데이터로 인해 데이터를 제대로 가지고 오지 않는 오류가 발생할 수 있다.(캐시 제어 헤더를 잘 사용하자.)


### 4xx: 클라이언트 오류를 의미하며 잘못된 메시지, 존재하지 않은 URL 요청 등이 있다.

- 400 Bad Request : 클라이언트가 요청을 잘못 보내서 서버가 이해하지 못하는 상태
- 401 Unauthorized : 요청이 인증되지 않아서 처리를 할 수 없는 상태
- 403 Forbidden : 인가되지 않은 상태
- 404 Not Found : 요청한 자원이 서버에 존재하지 않음.
- 405 Method Not Allowed : 요청 메서드가 허용되지 않는 경우
- 407 Proxy Authentication Required : 리소스에 대해 중개 프록시 서버에 의해 완료된 인증이 필요한 경우
- 408 Request Timeout : 요청이 너무 커 처리 시간이 초과되어 서버에서 요청을 처리하지 않고 연결을 닫는 경우
    - 응답 헤더에 Connection: close를 보내야 함.
- 415 Unsupported Media Type : 요청한 미디어 포맷을 서버에서 지원하지 않는 경우

#### 비공식 HTTP 응답을 만들어도 되지 않을까?
결론만 말하면 HTTP 응답 코드를 임의로 설정하는 것은 가능하다. 하지만, 이를 권장하지 않는다. 
그 이유는 응답 코드에 대하여 개발자 간의 규칙이 존재하는데 이를 무시하는 행위이므로, 
해당 업무에 대해서 모르는 타 개발자가 해당 코드를 보았을 때 의미를 파악하는 것이 불가능해진다.

#### 리소스가 없는 경우에는 HTTP 200 VS 404 중 어떤 게 옳은가?

※ HTTP 200이 적절하다는 주장

HTTP에서 말하는 자원을 서버 데이터와 엮어서는 안된다. 빈 값의 200을 응답하는 게 맞다.
반면, DELETE는 400으로 처리해야 하며 이런 처리를 통해 리소스와 데이터 자원을 분리하여 명확한 응답을 줄 수 있다.

요청한 데이터에 이상이 없고, 서버에서 식별 불가한 에러가 발생한 게 아니므로 200을 주는 게 맞다고 하나, 주문에는 실패했다는 내부 상태 코드를 body에 명시한다.
이를 통해 실제 서버에 이상 여부와 클라이언트에 대한 잘못된 요청 구분을 명확히하여 오류 원인을 찾는 용도로 사용한다.

※ HTTP 404가 적절하다는 주장

HTTP의 RFC7231 문서에 의하면 404는 아래와 같이 명시되어 있다.
대충 해석해보면 대상 자원을 못 찾았거나 드러내지 않고 싶을 때 표현하는 용도로 사용하라고 되어 있다.

```javascript
The 404 (Not Found) status code indicates that the origin server did not find a current representation for the target resource or is not willing to disclose that one exists.
```

리소스를 대상으로 요청을 보낸 것이니 서버가 정상 작동하는 것과 관계 없이 요청한 리소스에 대한 응답이 내려질 수 있는가 없는가를 기준으로 판별해야 하는 것이며
이럴 때는 404가 더 명확하다는 입장이다.

### reference
* [https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%A2%85%EB%A5%98-%ED%86%B5%EC%8B%A0-%EA%B3%BC%EC%A0%95-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%A2%85%EB%A5%98-%ED%86%B5%EC%8B%A0-%EA%B3%BC%EC%A0%95-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)
* [https://inpa.tistory.com/entry/HTTP-%F0%9F%8C%90-%EC%83%81%ED%83%9C-%EC%BD%94%EB%93%9C-1XX-5XX-%EC%B4%9D%EC%A0%95%EB%A6%AC%ED%8C%90-%F0%9F%93%96](https://inpa.tistory.com/entry/HTTP-%F0%9F%8C%90-%EC%83%81%ED%83%9C-%EC%BD%94%EB%93%9C-1XX-5XX-%EC%B4%9D%EC%A0%95%EB%A6%AC%ED%8C%90-%F0%9F%93%96)
* 김영한 HTTP 강의
* https://brainbackdoor.tistory.com/137