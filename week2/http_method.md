## HTTP Method의 GET, POST, PUT, PATCH의 차이에 관해서 설명해주세요.

HTTP Method는 주어진 리소스에 대해서 수행하길 원하는 행동을 나타낸다.

- GET : 리소스를 조회할 때 사용한다. 일반적으로 요청 본문에 데이터를 포함하지 않는다.
- POST : 리소스를 생성하거나 특별한 처리를 할 때 사용된다. GET과 다르게 요청 본문을 포함하고 여기에 생성하거나 처리할 리소스 속성을 지정하게 된다.
- PUT : 리소스를 새로운 값으로 덮어쓸 때 사용된다. 요청 본문에 포함되지 않는 모든 필드, 속성은 삭제되고 모든 새 필드나 속성이 추가된다.
- PATCH : 기존 리소스를 업데이트할 때 사용된다. PUT과 비슷한데 PATCH를 사용하면 다른 속성을 덮어쓰지 않고도 리소스의 특정 속성을 변경할 수 있다.
- DELETE : 데이터를 삭제할 때 사용된다. (200, 204 응답을 자주 쓴다.)
- HEAD : GET과 동일한데 응답에 본문이 없고 헤더만 존재한다. 검사 용도로 주로 사용된다.
- OPTIONS : preflight에 사용된다. 본 요청을 보내기 전에 안전한지 검사하는 용도이다. (ex CORS)

## REST API에 대해서 설명해주세요.
REST API란 RESTful한 API를 말한다. RESTful하다는 것은 아래의 조건을 만족한다는 것이다.

1. Uniform-Interface

API에서 자원들은 각각의 독립적인 인터페이스를 가지며 각 자원들이 url 자원식별, 표현을 통한 자원조작, Self-descriptive messages, HATEOAS 구조를 가지는 걸 말한다.
Uniform-Interface라는 의미는 웹페이지의 버전, HTML의 버전과 같이 각각의 명세가 바뀌어도 웹페이지는 잘 돌아가게 해야 한다는 것을 의미한다.

※ URL 자원식별

자원은 url로 식별이 되어야 한다는 것을 의미한다. 이런 설계의 예시는 아래와 같다.
- /product/1, get -> 1번 상품을 가져옴.
- /product, post -> 상품을 등록함.

※ 표현을 통한 자원 조작

url과 GET, DELETE 등 HTTP 표준 메서드 등을 통해 자원의 조회, 삭제 등의 작업을 설명할 수 있는 정보가 담겨야 한다.

위의 설명대로 자원과 HTTP 메서드를 사용한 API를 설계하면 어떤 의도로 해당 자원을 사용할 지, url만 보고 예측이 가능하게 된다.
반면에 registerProduct, register/product와 같이 자원에 대해서 어떤 행위를 할 건지 명시하는 API의 형태의 경우에는
작성자에 따라 API를 작성하는 다양한 경우의 수가 나올 수 있으며 이는 다른 개발자와의 협업 과정에서 요청 혹은 응답만을 보고는 어떤 기능을 하는 API인지 에측하기 어렵게 만든다.

※ Self-descriptive messages

HTTP Header에 타입을 명시하고 자원들은 MIME types에 맞춰 표현되어야 한다.
만약에 .json을 반환하면 application/json으로 명시해야 하며 이는 IETF의 RFC6838에 정의 및 표준화되어 있다.

따라서, json 타입의 데이터를 보낼 때에는 헤더에 보내는 형태의 타입을 'Content-Type' = 'application/json'을 명시해야 한다.
<br>-> 해당 부분은 자동적으로 RESTController 같은 걸 Spring에서 지원해주기 때문에 별도로 설정하지 않아도 자동으로 해준다.

※ HATEOAS 구조

HATEOAS(Hypermedia as the Engine of Application State)는 하이퍼링크에 따라 다른 페이지를 보여줘야 하며 데이터마다 어떤 URL에서 원했는 지를 명시해주어야 하는 걸 말한다.
보통은 href, links, link, url 속성 중 하나에 해당 데이터의 URL을 담아서 표기해야 한다.
<br>-> 가장 무슨 말인지 이해가 안갔는데 예시를 보면 알 수 있다.

아래를 보면 대략적으로 특정한 자원에 관해서 연관되어 호출할 수 있는 게 무엇이 있는 지에 관한 정보를 "links" 혹은 "_links"에 포함하여 보낸다.
그런데 아래의 예시를 보듯이 이 방법의 문제는 나중에 API가 많아지고 변경이 잦아질수록 관리가 어렵다는 문제점이 있다. 
그래서 Spring에서는 HATEOAS라는 라이브러리를 제공하며 아래의 링크를 통해서 확인할 수 있다.

하지만 개인적으로는 이렇게까지 과도하게 표현을 해야할 필요가 있을까?라는 생각이 들며 사실상 완벽한 RESTful API는 참 설계가 불가능한 게 아닌가 싶긴하다.

- Spring REST 설계 관련 링크 : https://spring.io/guides/tutorials/rest

```javascript
{
  "id": 123,
  "name": "John Doe",
  "email": "john.doe@example.com",
  "links": [
    {
      "rel": "self",
      "href": "https://api.example.com/users/123",
      "method": "GET"
    },
    {
      "rel": "update",
      "href": "https://api.example.com/users/123",
      "method": "PUT"
    },
    {
      "rel": "delete",
      "href": "https://api.example.com/users/123",
      "method": "DELETE"
    },
    {
      "rel": "orders",
      "href": "https://api.example.com/users/123/orders",
      "method": "GET"
    },
    {
      "rel": "create-order",
      "href": "https://api.example.com/users/123/orders",
      "method": "POST"
    }
  ]
}
```
2. Stateless

HTTP 자체가 Stateless해서 HTTP를 사용하는 것만으로도 만족하나. REST API를 제공하는 서버는 세션을 해당 서버 쪽에 유지하지 않는다는 걸 의미한다.
단순히 세션ID를 사용한 방식을 사용하지 않으면 만족한다고는 하는데 로그인을 구현하는 과정에서 사용성을 위해서 재발급용 토큰을 위해선 어쩔 수 없는 부분이다보니 이 부분도 어느 정도 타협이 필요한 것 같다.

3. Cacheable

HTTP는 GET 메서드에 대해서 'Cache-Control:max-age=100' 이런 식으로 한정된 시간을 정할 수 있다.
또한 캐싱된 데이터가 유효한지를 판단하기 위해서 'Last-modified', 'Etag'라는 헤더값을 사용하여 변경이 되어 캐시를 쓸 수 없는지와 캐싱되는 자원인지를 확인시켜준다.

4. Client-Server 구조 

클라리언트와 서버가 서로 독립적인 구조를 가져야 한다는 것을 의미한다. 이는 HTTP 표준만 지킨다면 문제가 없는 부분이다.


### REST API의 URL 6가지 규칙

1. 동작은 HTTP 메소드로만 하고 url에 동작 관련 내용이 들어가서는 안된다. 수정은 put, 삭제는 delete, 추가는 post, 조회는 get을 사용해야 하며,
URI로 '/product/delete/1' 이런 식으로 url을 설계해서는 안된다는 것이다.
2. .jpg, .png 등 확장자 표시를 하지 않는다.
3. 1번과 이어지는 말이기도 한데 동사가 아닌 명사로만 표기해야 한다. 
한 예시에선 유저가 책을 소유한다는 걸 표현하고 싶다면 '유저/유저아이디/inclusion/책/책아이디'로 표현해야 한다. 
4. 계층적인 구조를 담아야 해서 '/집/아파트/전세' 이런 식으로 내려가는 구조로 API를 설계해야 한다.
5. 대문자가 아닌 소문자로 쓰며 긴 경우 언더바가 아닌 그냥 바를 써서 연결해야 한다.
6. HTTP 응답 상태 코드를 적재적소에 사용해야 한다. 이는 http_resposne.md를 참고하기 바람.

자주 물어보는 예시라서 넣기는 했는데, 개인적으로 이걸 다 지키는 건 불가능하다고 생각함, 
어느정도는 서버 프레임워크에서 방법을 제공해주지만 더 많은 기능이 필요한 경우, 자원만으로는 식별이 불가능한 URL이 있을 수도 있고, 
HATEOAS의 경우엔 필수로 지키기 어려울 수 있다는 생각이 들어서 어느정도 절충안인 HTTP API로 설계를 하는 게 맞지 않나 싶긴 하다.

하지만 개발자 사이에서 API 설계에 대한 표준을 어느정도 제공해줄 방법에 대한 고민 정도라고 생각을 하면 의미가 있지 않을까 싶다.

### 참고자료
* [https://blog.postman.com/what-are-http-methods/](https://blog.postman.com/what-are-http-methods/)
* [inpa HTTP Method](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%A2%85%EB%A5%98-%ED%86%B5%EC%8B%A0-%EA%B3%BC%EC%A0%95-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)
* https://www.youtube.com/watch?v=7LbylTMlj8M
* https://www.youtube.com/watch?v=xWA1eTPSzDE
* https://www.youtube.com/watch?v=Nxi8Ur89Akw