
### 만약 사용자가 검색한 URL이 불완전한 URL일 경우?
우선 브라우저의 자동 보완 기능 (URL Autocomplete) 을 통해 불완전한 URL도 파악 가능
→ 기본 통신 규약(Protocol) 설정으로 인해, 통신 규약을 생략해도 브라우저가 보완해준다.

브라우저는 기본적으로 HTTPS(HTTP Secure)를 우선적으로 사용하며, 사용자가 통신 규약(예: https://) 생략했을 때, 먼저 HTTPS로 연결을 시도하고, 실패하면 HTTP로 시도한다.
대부분의 웹 사이트는 HTTPS를 기본적으로 지원하며, HTTP 요청을 받아도 HTTPS로 리디렉션한다.

## www.naver.com 을 검색했을 때 일어나는 일?
### 1. DNS 조회
사용자가 브라우저에 www.naver.com 을 입력하면, 브라우저는 이 도메인에 해당하는 IP 주소를 확인하기 위해 DNS(Domain Name System) 서버에 요청을 보낸다.


만약 캐시에 IP 주소가 있다면, 캐시된 값을 사용하며, 없다면 DNS 서버에 질의하여 도메인의 IP 주소를 반환받는다.


이 과정에서 DNS 기반 로드 밸런싱이 일어난다.


네이버는 DNS 서버에서 특정 도메인 www.naver.com 에 대해 여러 IP 주소를 반환하도록 설정할 수 있다.


이 때, 로드 밸런서가 사용자의 지리적 위치나 서버의 부하 상태 등을 고려하여 가장 적합한 서버의 IP를 클라이언트에게 제공하게 된다.


현재 네이버는 HAProxy라는 Reverse Proxy 형태로 동작하는 오픈소스 로드밸런서를 사용중이다.


### 2. TCP 연결
DNS 조회를 통해 얻은 IP 주소로 브라우저는 서버와 3-way handshake 과정을 통해 TCP 연결을 설정한다.


네이버의 경우 HTTP/2 와 HTTP/3을 모두 지원하는데, 메인 페이지를 개발자도구로 확인하면 h2 프로토콜을 사용중이다. (h3은 서치 기능 일부에서 사용)


### 3. HTTP 요청 전송 (특정 페이지 정보를 가져오기 위한 요청 전송)
네이버 메인 페이지 로딩을 위해, HTML, CSS, JavaScript 파일, 이미지 등 다양한 리소스를 요청한다. (GET 요청)


이 요청에는 사용자의 브라우저 정보, 쿠키, 요청 헤더 등이 포함된다.


### 4. 서버 처리 & 응답
네이버 웹 서버는 요청을 받은 후, 요청된 리소스를 처리하고 클라이언트에게 응답한다.
HTTP 응답 코드와 함께 요청한 데이터를 보낸다.


네이버는 CDN(Content Delivery Network)을 활용하여 지역적으로 더 가까운 서버에서 리소스를 제공하여 속도를 최적화하고 있다.


### 5. 페이지 렌더링
브라우저는 서버로부터 받은 HTML 문서를 분석하고, CSS와 JavaScript 파일을 다운로드하여 DOM(Document Object Model)과 CSSOM(CSS Object Model)을 생성한다.


DOM과 CSSOM을 결합하여 렌더 트리를 생성한 뒤, 화면에 페이지를 그린다. (Painting)
JavaScript는 이후에 실행되며, 페이지를 동적으로 변경하거나 추가적인 사용자 상호작용을 처리한다.


***


references.
- [로드 밸런서](https://www.ncloud.com/product/networking/loadBalancer#detail)
- [네이버 클라우드의 HAProxy와 대량의 HTTP 요청 처리에 대하여](https://www.youtube.com/watch?v=8-0dTvCsasY&t=448s)