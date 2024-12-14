# 세션과 토큰과 쿠키 차이

세션과 토큰은 인증과 관련된 정보를 활용하기 위해서 사용하는 방식으로 가장 큰 차이는 누가 해당 정보를 관리하느냐의 차이가 가장 크다.

### 세션 방식

우선 세션은 서버에서 인증 정보를 관리를 하는 방식이다. 로그인을 하고 성공한다면 서버는 DB에 대해 누가 언제까지 만료되는 정보를 가지고 있는 지를 저장한 뒤 그 식별자를 사용자에게 내려준다. 그러면 앞으로 사용자는 해당 정보를 사용하여 인가 과정에서 사용할 수 있게 된다.

세션 방식의 장점은 회원에 관한 관리를 할 수 있다는 점이다. 서버가 회원에 관한 인증 정보를 가지고 있기 때문에 만약에 회원이 로그아웃을 한다면 해당 인증 정보를 만료시켜 버리고 이후에 만료된 정보를 들고 온 경우 다시 로그인 화면으로 보내버리는 등의 행위를 할 수 있다.

하지만 단점은 서버 내에 많은 정보를 저장해야 하고 특히나 서버를 다중화한 경우 세션 공유에 대한 추가적인 고민이 필요하다. 또한, 만약 단순히 메모리에 저장을 해버리는 경우 많은 정보를 한꺼번에 저장할 수록 사용자가 많아졌을 때 메모리에 대한 부하가 커진다.

### 토큰 방식

토큰 방식은 클라이언트가 인증 정보를 관리하는 방식이다. 서버에 대해 부담이 커지는 세션 방식에 비해서는 효율성이 높다. 보통 JWT 토큰을 사용하는데, JWT 토큰에서는 서명 기능을 통해 변조 여부를 확인할 수 있고, 권한이나 ID와 같은 다양한 정보를 JSON 형태로 저장한 뒤 BASE64로 인코딩하기 때문에 주고 받기도 쉽다. 특히, 서버 내에 저장하지 않는다는 특징 때문에 서버가 다중화되어도 토큰 방식을 사용하면 확장을 하는데에 문제가 없다. 왜냐하면 세션과 달리 인증 정보를 공유하지 않기 때문이다.

하지만 단점은 보안이 중요하고, 추가적인 처리를 해줘야 하는 경우엔 서버 내에 저장된 것이 아닌 클라이언트에 저장된 방식이니 서버는 손 쓸 방법이 없다. 예를 들면, 클라이언트에서 특정 회원의 인증 정보가 담긴 토큰을 탈취 당하는 경우 해당 토큰의 만료 시간이 지날 때까지 해당 토큰은 회원에서 인가를 내리는 용도로 계속해서 활용할 수 있다는 문제점이 있다.

### 쿠키

쿠키는 서버에서 클라이언트 측에 Key:Value 형태로 저장하고 싶은 데이터를 만료시간과 함께 보내는 데이터이다. 해당 쿠키를 사용해서 위에서 언급한 토큰이나 세션ID 같은 걸 저장을 해뒀다가 전송을 해준다. 

보통 보안상 중요하지 않고 편리성을 위해 필요한 정보이지만 서버에 저장하고 싶지 않을 때, 쿠키를 발급해 클라이언트에 저장하는데 사용이 된다. 이는 HTTP 프로토콜의 Stateless하다는 특징을 보완하기 위함이다. 예를 들면, 회원의 장바구니 로직, 아니면 사용자가 선택한 언어와 같은 체크박스로 표시할 수 있는 옵션이나 설정 등을 저장해서 사용한다.

이 때, 쿠키는 여러 가지 특징이 있다. 

- RFC 2109에 의하면 하나의 호스트나 도메인에서 최대 20개까지, 최대 크기는 4096Byte까지 만들 수 있다. 하지만, 브라우저마다 약간씩 다르므로 이는 검색해보는 걸 권장한다.
- 브라우저 내에 저장이 되며 호스트에 대해서 교차적인 쿠키는 발급할 수 없다. (www.networkStudy.com 에서 www.osStudy.com 으로 보내는 쿠키는 발급할 수 없다는 의미)
- 브라우저의 생명 주기와 연동된 생명 주기를 선택할 수도 있다.
- 모바일 환경에서는 브라우저를 지원하는 앱이라면 쿠키를 사용하지만 네이티브 앱에는 존재하지 않기 때문에 이 땐 단순 토큰을 사용해야 한다.
- 특정 도메인에 대한 쿠키가 브라우저에 있다면 Cookie 헤더를 통해 브라우저는 서버로 이전에 저장했던 모든 쿠키들을 회신한다.

--- 
쿠키 관련 옵션 정보 정리

- Expires, Max-Age: 브라우저에 언제까지 해당 쿠키를 저장하고 있을 지 알리는 옵션으로 만약에 설정하지 않는다면 사용자가 브라우저를 나갈 시 없어지게 설정된다.
- Secure: Secure 쿠키는 HTTPS 프로토콜 상에서 암호화된 요청일 경우에만 전송이 되며, HTTP 프로토콜엔 설정이 불가능하다.
- HttpOnly: HTTP Only는 자바 스크립트 상의 Document.cookie에 접근이 불가능하다는 의미이다.
- Domain: 서브도메인이 있는 경우 해당 도메인과 쿠키를 공유하고 싶은 경우 사용한다.
- Path: 사이트의 모든 경로가 아닌 특정 경로에서만 해당 쿠키를 사용하고 싶을 때 사용한다.


***


references.
- [cookies_and_sessions](https://www.youtube.com/watch?v=XgcCkcKGbys)
- [sessions_vs_token_authentication](https://f-lab.kr/insight/session-vs-token-authentication-20240821)
- [sessions_cookies_jwt](https://www.youtube.com/watch?v=tosLBcAX1vk)
- [http_cookies](https://developer.mozilla.org/ko/docs/Web/HTTP/Cookies)