# REST? RESTful API?

Created: 2022년 8월 10일 오전 11:38
Last Edited Time: 2022년 8월 23일 오후 3:23
State: Done
Tag: network

# **Representational State Transfer**

---

API 작동 방식에 대한 조건을 부과하는 **소프트웨어 아키텍처. 자원을 자원의 표현, 즉 이름으로 구분하여 해당 자원의 상태(정보)를 주고 받는다.**

![REST_information_model.png](REST%20RESTful%20API%2003ce298d63c1468ea11fbcf89f85287c/REST_information_model.png)

- HTTP URI를 통해 자원을 명시
// URI : **Uniform Resource Identifier ↔** URL : Uniform Resource Link
- `HTTP Method (POST, GET, PUT, DELETE)`를 통해 해당 자원에 대한 `CRUD OPERATION`을 적용
    - Client가 자원의 상태 (정보)에 대한 조작을 요청하면 Server는 이에 적절한 응답 (Representation)을 보낸다
    - REST에서 하나의 자원은 JSON, XML, TEXT, RSS 등 여러 형태의 Representation으로 나타낼 수 있다.
    - 현재는 JSON으로 주고 받는 것이 대부분이다.
- 즉, REST는 자원 기반의 구조 (ROA: Resource Oriented Architecture) 설계의 중심에 Resoure가 있고 HTTP Method를 통해 Resource를 처리하도록 설계된 아키텍쳐를 의미한다.
- **웹의 모든 자원에 고유한 ID인 HTTP URI 를 부여한다.**

# REST가 필요한 이유

---

- RESTful APIs 개발하는 가장 큰 이유는 **Client Side를 정형화된 플랫폼이 아닌 모바일, PC, 어플리케이션 등 플랫폼에 제약을 두지 않는 것을 목표로 했기 때문 입니다.**
- 즉, 2010년 이전만 해도 Server Side에서 데이터를 전달해주는 Client 프로그램의 대상은 PC 브라우저로 그 대상이 명확 했다. 그렇다 보니 그냥 JSP ASP PHP 등을 잉요한 웹페이지를 구성하고 작업을 진행하면 됐다.
- 하지만 스마트 기기들이 등장하면서 TV, 스마트 폰, 테블릿 등 Client 프로그램이 다양화 되고 그에 맞춰 Server를 일일이 만다는 것이 꽤 비효율적인 일이 되어 버렸다.
- 이런 과정에서 개발자들은 Client Side를 전혀 고려하지 않고 메시지 기반, XML, JSON과 같은 **Client에서 바로 객체로 치환 가능한 형태의 데이터 통신을 지향하게 되면서 Server와 Client의 역할을 분리하게 되었다.**

# REST 제약조건

---

**a. 클라이언트 / 서버 구조**

- 클라이언트는 유저와 관련된 처리를, 서버는 REST API를 제공함으로써 각각의 역활이 확실하게 구분되고 일괄적인 인터페이스로 분리되어 작동할 수 있게 한다
- REST Server: API를 제공하고 비지니스 로직 처리 및 저장을 책임진다.
- Client: 사용자 인증이나 context (세션, 로그인 정보) 등을 직접 관리하고 책임진다.
- 서로 간 의존성이 줄어든다.

**b. 무상태성 (Stateless)**

- REST는 HTTP의 특성을 이용하기 떄문에 무상태성을 갖는다.
- 즉 서버에서 어떤 작업을 하기 위해 상태정보를 기억할 필요가 없고 들어온 요청에 대해 처리만 해주면 되기 때문에 구현이 쉽고 단순해진다.

**c. 캐시 처리 가능 (Cacheable)**

- HTTP라는 기존 웹표준을 사용하는 REST의 특징 덕분에 기본 웹에서 사용하는 인프라를 그대로 사용 가능하다.
- 대량의 요청을 효율적으로 처리하기 위해 캐시가 요구된다.
- 캐시 사용을 통해 응답시간이 빨라지고 REST Server 트랜잭션이 발생하지 않기 때문에 전체 응답시간, 성능, 서버의 자원 이용률을 향상 시킬 수 있다.

**d. 자체 표현 구조 (Self - descriptiveness)**

- JSON을 이용한 메시지 포멧을 이용하여 직관적으로 이해할 수 있고 REST API 메시지만으로 그 요청이 어떤 행위를 하는지 알 수 있다.

**e. 계층화 (Layered System)**

- 클라이언트와 서버가 분리되어 있기 때문에 중간에 프록시 서버, 암호화 계층 등 중간매체를 사용할 수 있어 자유도가 높다

**f. 유니폼 인터페이스 (Uniform)**

- Uniform Interface는 Http 표준에만 따른다면 모든 플랫폼에서 사용이 가능하며, URI로 지정한 리소스에 대한 조작을 가능하게 하는 아키텍쳐 스타일을 말한다
- URI로 지정한 Resource에 대한 조작을 통일되고 한정적인 인터페이스로 수행한다.
- **즉, 특정 언어나 기술에 종속되지 않는다.**

# 세부 규칙

---

**1. 슬래시 구분자 ( / )는 계층 관계를 나타내는데 사용한다.**

**2. URI 마지막 문자로 슬래시 ( / )를 포함하지 않는다.**

- 즉 URI에 포함되는 모든 글자는 리소스의 유일한 식별자로 사용되어야 하며 URI가 다르다는 것은 리소스가 다르다는 것
- 역으로 리소스가 다르면 URI도 달라져야 한다.

**3. 하이픈 ( - )은 URI 가독성을 높이는데 사용한다.**

**4. 밑줄 ( _ )은 URI에 사용하지 않는다.**

**5. URI 경로에는 소문자가 적합하다.**

- URI 경로에 대문자 사용은 피하도록 한다.

**6. 파일확장자는 URI에 포함하지 않는다.**

- REST API 에서는 메시지 바디 내용의 포맷을 나타내기 위한 파일 확장자를 URI 안에 포함시키지 않는다.
- 대신 Accept Header 를 사용한다.
- ex) `GET`: `http://restapi.exam.com/orders/2/Accept: image/jpg`

**7. 리소스 간에 연관 관계가 있는 경우**

- /리소스명/리소스ID/관계가 있는 다른 리소스 명
- ex) GET: /users/2/orders (일반적으로 소유의 관계를 표현할 때 사용)

![Untitled](REST%20RESTful%20API%2003ce298d63c1468ea11fbcf89f85287c/Untitled.png)

# REST의 단점

---

1. REST는 point-to-point 통신모델을 기본으로 한다. 따라서 서버와 클라이언트가 연결을 맺고 상호작용해야하는 어플리케이션의 개발에는 적당하지 않다.
2. REST는 URI, HTTP 이용한 아키텍처링 방법에 대한 내용만을 담고 있다. 보안과 통신규약 정책 같은 것은 전혀다루지 않는다. 따라서 개발자는 통신과 정책에 대한 설계와 구현을 도맡아서 진행해야 한다.
3. HTTP에 상당히 의존적이다. REST는 설계 원리이기 때문에 HTTP와는 상관없이 다른 프로토콜에서도 구현할 수 있기는 하지만 자연스러운 개발이 힘들다. 다만 REST를 사용하는 이유가 대부분의 서비스가 웹으로 통합되는 상황이기에 큰 단점이 아니게 되었다.
4. CRUD 4가지 메소드만 제공한다. 대부분의 일들을 처리할 수 있지만, 4가지 메소드 만으로 처리하기엔 모호한 표현이 있다.

# RESTful?

---

> A web API that obeys the [REST constraints](https://en.wikipedia.org/wiki/Representational_state_transfer#Architectural_constraints) is informally described as *RESTful*.
REST 제약조건을 따르는 웹 API를 통틀어 RESTful이라고 지칭한다.
> 
- HTTP와 URI 기반으로 자원에 접근할 수 있도록 제공하는 애플리케이션 개발 인터페이스이다. 기본적으로 개발자는 HTTP 메소드와 URI 만으로 인터넷에 자료를 `CRUD` 할 수 있다.
- 'REST API'를 제공하는 웹 서비스를 **'RESTful'** 하다고 할 수 있다.
- RESTful은 REST를 REST 답게 쓰기 위한 방법으로, 누군가가 공식적으로 발표한 것은 아니다.

# RESTful API 개발 시 유의사항

---

- 이 REST API를 개발하다보면 **HTTP의 Response 규약을 지키지 않고 본인들이 만들어넨 JSON 컨벤션으로 응답하는 경우를 많이 확인 할 수 있는데 그것은 옳지 않은 개발 방향이다.**
- 왜냐하면 **Client Side가 정형화 되어있지 않은 환경에서 개발 속도를 저하하는 가장 큰 이유는 표준을 지키지 않았기 때문이다.**

# RESTful API 개발 원칙

---

**a. 자원을 식별할 수 있어야 한다.**

- URL (Uniform Resource Locator) 만으로 내가 어떤 자원을 제어하려고 하는지 알 수 있어야 한다. 자원을 제어하기 위해서, 자원의 위치는 물론 자원의 종류까지 알 수 있어야 한다는 의미이다.
- Server가 제공하는 정보는 JSON 이나 XML 형태로 HTTP body에 포함되어 전송 시킨다.

**b. 행위는 명시적이어야 한다.**

- REST는 아키텍쳐 혹은 방법론과 비슷하다. 따라서 이런 방식을 사용해야 한다고 강제적이지 않다. 기존의 웹 서비스 처럼, GET을 이용해서 UPDATE와 DELETE를 해도 된다.
- 다만 REST 아키텍쳐에는 부합하지 않으므로 REST를 따른다고 할 수는 없다.

**c. 자기 서술적이어야 한다.**

- 데이터에 대한 메타정보만 가지고도 어떤 종류의 데이터인지, 데이터를 위해서 어떤 어플리케이션을 실행 해야 하는지를 알 수 있어야 한다.
- **즉, 데이터 처리를 위한 정보를 얻기 위해서, 데이터 원본을 읽어야 한다면 자기 서술적이지 못하다**

**d. HATEOS (Hypermedia as the Engine of Application State)**

- 클라이언트 요청에 대해 응답을 할 때, 추가적인 정보를 제공하는 링크를 포함할 수 있어야 한다.
- REST는 독립적으로 컴포넌트들을 손쉽게 연결하기 위한 목적으로도 사용된다. 따라서 서로 다른 컴포넌트들을 유연하게 연결하기 위해선, 느슨한 연결을 만들어줄 것이 필요하다.
- 이때 사용되는 것이 바로 `링크`이다. 서버는 클라이언트 응용 애플리케이션에 하이퍼 링크를 제공한다.
- 클라이언트는 이 하이퍼 링크를 통해서 전체 네트워크와 연결되며 HATEOAS는 서버가 독립적으로 진화할 수 있도록 서버와 서버, 서버와 클라이언트를 분리 할 수 있게 한다.

# Reference

---

1. [https://aws.amazon.com/ko/what-is/restful-api/](https://aws.amazon.com/ko/what-is/restful-api/)
2. [https://en.wikipedia.org/wiki/Representational_state_transfer#Architectural_concepts](https://en.wikipedia.org/wiki/Representational_state_transfer#Architectural_concepts)
3. [https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)
4. [https://velog.io/@somday/RESTful-API-이란](https://velog.io/@somday/RESTful-API-%EC%9D%B4%EB%9E%80)

→ REST란 무엇인가