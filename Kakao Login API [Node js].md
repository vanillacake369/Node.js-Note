# Kakao Login API [Node.js]

Created: 2022년 8월 6일 오후 2:20
Last Edited Time: 2022년 8월 23일 오후 3:23
State: Done
Tag: node.js

이전 프로젝트에서 PHP로 사용자가 입력한 고유의 - PRIMARY KEY인 - 아이디 값을 세션에 유지하여 로그인 하는 기능만 구현해보았었는데, 새로운 프로젝트에서 Node.js를 배우면서 카카오 소셜 로그인을 공부해봐야겠다고 생각이 들었다.

여태까지는 구글링을 해가며 그저 “음..이 코드를 내 모듈과 인터페이스에 맞게끔 수정하면 돌아가겠군”하면서 이론 공부는 하나도 하지 않았는데, 제로초님 강의를 들으니 생각이 완전 바뀌었다.

멍청하게 앉아서 뚱땅뚱땅 코드를 짜기보다, 이론대로 구현하고, 그를 구현하기 위해서 생각하는 시간을 더 많이 해야하는 것이구나를 깨달았다. 특히, 내 옆 자리 동료를 내가 2주일 내내 끙끙대던 구현을 하루만에 하는 것을 보고 현타왔다.

사담은 이만하고, 카카오 소셜 로그인을 공부하기 앞서서 로그인을 구현하는 과정은 어떻게 되며, 관련 이론은 어떤 것이 있는지부터 하나하나 제대로 공부해나가보자.

# 세션 기반 인증 로그인 🗂️

---

> 세션에 관해서 헷갈린다면 이전에 정리해두었던 페이지를 살펴보자.
> 

[Cookie vs Session](Cookie%20vs%20Session%20f322cbdf68234ab8afbba1cbeb5e87b8.md)

![Untitled](Kakao%20Login%20API%20%5BNode%20js%5D%20dd8ae7485e1f45239f72ec0d76589c93/Untitled.png)

일반적인 로그인은 서버의 session을 통해 이루어지는데, 서버는 접속중인 클라이언트들을 이 session을 통해 관리합니다. 

1. 사이트에 접속하면 session에 해당 클라이언트가 기록
2. 로그인을 하게 되면 해당 클라이언트가 로그인한 것을 저장하게 됩니다. 
3. 이후 해당 클라이언트는 로그인이 요구되는 정보에 접근할 수 있게 됩니다. 

이 방식은 접속자수가 늘어나면 서버의 메모리 사용량이 증가하게 되고 성능에 영향을 미치게 됩니다. (서버를 증설하고 session관리용 서버를 분리하는 등의 작업을 통해 이를 극복할 수 있습니다.)

# REST API 사용 시의 로그인은 무엇이 다른가? 🛣️

---

[REST API](https://www.a-mean-blog.com/ko/blog/%ED%86%A0%EB%A7%89%EA%B8%80/_/REST%EC%99%80-RESTful-API)에서는 서버가 session을 가지지 않습니다. 물론 REST API서버에도 session을 추가하여 사용할 수 있지만 이는 REST가 지향하는 바가 아닙니다. REST는 stateless 특징을 가지고 있기 때문이다. 대신 **REST API는 토큰(token) 인증방식**을 사용하게 됩니다.

아래는 REST의 창조자 Roy Fielding의 [Principled Design of the Modern Web Architecture by Roy T. Fielding and Richard N. Taylor](http://www.ics.uci.edu/~fielding/pubs/webarch_icse2000.pdf) 에서의 client-server interaction이다. 아래 두 가지 원칙을 보면 세션을 사용하지 말아야하는 이유를 알 수 있다.

- *1st*: it **removes any need for the connectors to retain application state between requests**, thus reducing consumption of physical resources and improving scalability;
- *3rd*: it allows an intermediary to view and **understand a request in isolation**, which may be necessary when services are dynamically rearranged;

로그인 API로 아이디와 패스워드가 일치함이 확인되면 서버는 토큰을 발행하고, 로그인 후 이용가능한 API들에는 유효한 토큰이 있는 경우에만 사용할 수 있게 됩니다. 이때 토큰은 당연하게도 위조하기가 어려워야 하며 사용자를 인식할 수 있는 정보가 들어있어야 합니다. 

# 토큰 기반 인증 🪙

---

토큰 기반 인증이란 사용자가 자신의 아이덴티티를 확인하고 고유한 [액세스 토큰](https://www.okta.com/identity-101/access-token/)을 받을 수 있는 프로토콜을 말합니다. 사용자는 토큰 유효 기간 동안 동일한 웹페이지나 앱, 혹은 그 밖에 해당 토큰으로 보호를 받는 리소스로 돌아갈 때마다 자격 증명을 다시 입력할 필요 없이 토큰이 발급된 웹사이트나 앱에 액세스할 수 있습니다.

인증 토큰은 도장이 찍힌 티켓과 같습니다. 토큰이 유효하다면 사용자는 계속해서 액세스할 수 있습니다. 사용자가 로그아웃하거나 앱을 종료하면 토큰도 무효화됩니다.

토큰 기반 인증은 비밀번호 기반 또는 서버 기반 인증 기법과는 다릅니다. 토큰이 두 번째 보안 계층을 제공하여 관리자가 각 작업과 트랜잭션을 정밀하게 제어할 수 있습니다.

## 세 가지 인증 토큰

---

- **Connected : Keys, discs, drives, and other physical items plug into the system for access.**
- Contactless **: form a logical connection to the client computer but do not require a physical connection**
- **Disconnected : neither a physical nor logical connection to the client computer. They typically do not require a special input device, and instead use a built-in screen to display the generated authentication data, which the user enters manually themselves via a keyboard or keypad**

## 토큰 인증 4단계

---

![https://www.okta.com/sites/default/files/styles/tinypng/public/media/image/2020-12/TokenBasedAuthentication.png?itok=zXMogDjG](https://www.okta.com/sites/default/files/styles/tinypng/public/media/image/2020-12/TokenBasedAuthentication.png?itok=zXMogDjG)

- **요청:** 사용자가 서버 또는 보호되는 리소스에 대한 액세스를 요청합니다. 이때 비밀번호를 이용한 로그인이나 그 밖에 지정된 프로세스가 개입될 수 있습니다.
- **확인:** 서버가 해당 사용자의 액세스 여부를 확인합니다. 이때는 사용자 이름에 대한 비밀번호 확인 또는 그 밖에 지정된 프로세스가 개입됩니다.
- **토큰:** 서버가 링, 키, 휴대전화와 등의 인증 디바이스와 통신합니다. 확인을 마치면 서버가 토큰을 발급하여 사용자에게 전달합니다.
- **저장:** 작업이 지속되는 동안 토큰이 사용자의 브라우저에 저장됩니다.

## JWT : JavaScript Object Notation Web Token

---

JSON Web Token(JWT)은 [개방형 표준](https://tools.ietf.org/html/rfc7519)입니다. 완성된 코딩 결과물은 발신자와 수신자 사이에서 안전한 통신을 지원합니다. 데이터는 디지털 서명을 통해 확인하며, HTTP를 통해 전송되는 경우에는 데이터를 암호화하여 안전하게 보호합니다.

JWT에는 세 가지 중요한 구성요소가 있습니다.

1. **헤더:** 토큰 유형을 비롯해 관련된 서명 알고리즘을 정의합니다.
2. **페이로드:** 토큰 발급자, 토큰 유효기간 등을 정의합니다.
3. **서명:** 보안 서명을 통해 메시지가 전송 과정에서 바뀌지 않은 것을 확인합니다.

다만 JWT는 단일 키를 사용하기 때문에 유출되면 시스템 전체가 위험에 노출될 수 있다.

# OAuth 👐

---

**Open Authorization.**

인터넷 사용자들이 비밀번호를 제공하지 않고 다른 웹사이트 상의 자신들의 정보에 대해 웹사이트나 애플리케이션의 접근 권한을 부여할 수 있는 공통적인 수단으로서 사용되는, 접근 위임을 위한 개방형 표준이다.

// 카카오와 네이버 로그인 인증 서비스가 이를 구현하여 사용된다.

> The OAuth 2.0 authorization framework enables a third-party application to obtain limited access to an HTTP service, either on behalf of a resource owner by orchestrating an approval interaction between the resource owner and the HTTP service, or by allowing the third-party application to obtain access on its own behalf.  This specification replaces and obsoletes the OAuth 1.0 protocol described inRFC 5849.
> 

기존에는 id와 password를 직접 입력받아 저장하여 필요할 때마다 불러와서 사용을 해야 했는데, 이런 방식을 사용하게 되면 다음과 같은 문제가 발생할 수 있다.

- 사용자의 신뢰성
: id,password 공개성에 대한 신뢰성
- 내 서버
: id,password를 받았기에 보안 문제 발생 시, 모든 책임을 져야함.
- 다른 사이트 (google,naver 등등..)
: 내 서버에 대해 신뢰를 할 수 없다.

위와 같은 문제를 해결하기 위해 2006년 11월 트위터 개발자와 Ma.gnolia의 개발자가 안전한 인증방식에 대한 논의를 하면서 OAuth가 등장했고,2007년 10월 3일, OAuth 코어 1.0의 최종 초안이 발표되었다. 현재는 OAuth 1.0a를 거쳐 OAuth2.0이 많이 사용되고 있다

## OAuth1.0 vs OAuth2.0

---

| 비교 | OAuth1.0 | OAuth2.0 |
| --- | --- | --- |
| 참여자구분 | 이용자
소비자
서비스 제공자 | 자원 소유자
클라이언트
권한 서버
자원 서버 |
| 토큰 | 요청 토큰 (Request Token)
접근 토큰 (Access Token) | 접근 토큰 (Access Token)
재발급 토큰 (Refresh Token) |
| 유효기간 | 접근 토큰의 유효기간 없음 | 접근 토큰 유효기간 부여
만료 시 재발급 토큰 이용 |
| 클라이언트 | 웹 서비스 | 웹, 앱 등등 |

## OAuth2.0 roles

---

```
   resource owner
      An entity capable of granting access to a protected resource.
      When the resource owner is a person, it is referred to as an
      end-user.

   resource server
      The server hosting the protected resources, capable of accepting
      and responding to protected resource requests using access tokens.

   client
      An application making protected resource requests on behalf of the
      resource owner and with its authorization.  The term "client" does
      not imply any particular implementation characteristics (e.g.,
      whether the application executes on a server, a desktop, or other
      devices).

   authorization server
      The server issuing access tokens to the client after successfully
      authenticating the resource owner and obtaining authorization.

The authorization server may be the same server as the resource server or a separate entity. A single authorization server may issue access tokens accepted by multiple resource servers.
```

## OAuth2.0 인증 흐름도

---

```
     +--------+                               +---------------+
     |        |--(A)- Authorization Request ->|   Resource    |
     |        |                               |     Owner     |
     |        |<-(B)-- Authorization Grant ---|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(C)-- Authorization Grant -->| Authorization |
     | Client |                               |     Server    |
     |        |<-(D)----- Access Token -------|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(E)----- Access Token ------>|    Resource   |
     |        |                               |     Server    |
     |        |<-(F)--- Protected Resource ---|               |
     +--------+                               +---------------+

								Figure 1: Abstract Protocol Flow
```

# Kakao REST API 사용하여 로그인 구현하기 🍫

---

아래는 공식문서에서의 카카오 로그인 과정 시퀀스 다이어그램이다.

![Untitled](Kakao%20Login%20API%20%5BNode%20js%5D%20dd8ae7485e1f45239f72ec0d76589c93/Untitled%201.png)

# Node.js로 카카오 로그인 연동하기 🔒

---

1. 라우터를 통해 /kakao 요청이 서버로 온다.
2. passport.authenticate(’kakao’)를 통해 카카오 로그인 페이지로 이동 
// passport.authenticate()에 대해서는 [여기를](https://www.passportjs.org/concepts/authentication/middleware/) 참고하자.
3. 카카오 로그인을 하면, 카카오 로그인 developer에서 설정한 redirect url 경로에 따라 식별값을 전달한다
// Redirect URL : 해당 클라이언트를 식별하고 식별값(Access token)을 전달할 통로
4. /kakao 로그인 요청
    
    ![img.png](Kakao%20Login%20API%20%5BNode%20js%5D%20dd8ae7485e1f45239f72ec0d76589c93/img.png)
    

`app.js`

![Untitled](Kakao%20Login%20API%20%5BNode%20js%5D%20dd8ae7485e1f45239f72ec0d76589c93/Untitled%202.png)

1. 카카오 로그인 페이지로 이동
    
    ![Untitled](Kakao%20Login%20API%20%5BNode%20js%5D%20dd8ae7485e1f45239f72ec0d76589c93/Untitled%203.png)
    

`router/authRouter.js`

```jsx
const express = require("express");
const router = express.Router();
const passport = require("passport");
const loginMiddleware = require("../middleware/authCheck"); //로그인 확인 여부 미들웨어
// router.get("/kakao", (req, res) => {
//   res.send("test");
// });
// router.get("/oauth", passport.authenticate("kakao"));

/*
 * @query
 * ?email=
 *
 */
router.get(
  "/kakao",
  loginMiddleware.isLoggedIn,
  (req, res, next) => {
    // console.log(req.query);
    req.session.email = req.query.email;
    next();
  },
  passport.authenticate("kakao")
);
```

1. 카카오에서 설정한 redirect url을 통해 요청 재전달 [(공식문서)](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#redirect-uri)

`router/authRouter.js`

```jsx
router.get(
  // TODO: 실제 서비스에서 수정 필요
  "/redirect",
  passport.authenticate("kakao", {
    successRedirect: "http://localhost:7001/",
    failureRedirect: "/error",
    successFlash: true,
    failureFlash: true,
  }),
  (req, res) => {
    console.log(`redirect 실행`);
    res.redirect("/");
  }
);
// (req, res) => {
//   res.redirect("http://localhost:7001/");
//   // console.log(req);
// }
// router.get("/", (req, res) => {
//   // consoel.log(req);
//   res.send("mypage");
// });
module.exports = router;
```

1. passport.authenticate(’kakao’)에서 kakaoStrategy로 인증 전략 시행. 전략에는 카카오 서버에서 보낸 카카오 계정 정보가 들어있다. [(공식문서)](https://www.passportjs.org/packages/passport-kakao/)

![Untitled](Kakao%20Login%20API%20%5BNode%20js%5D%20dd8ae7485e1f45239f72ec0d76589c93/Untitled%204.png)

`kakao.js`

```jsx
const passport = require("passport");
const KakaoStrategy = require("passport-kakao").Strategy;

const User = require("../models/User");
const { raw } = require("express");

module.exports = () => {
  passport.use(
    new KakaoStrategy(
      {
        clientID: process.env.KAKAO_REST_KEY,
        callbackURL: "/login/redirect",
        passReqToCallback: true,
      },

      async (req, accessToken, refreshToken, profile, done) => {
        // console.log(req);
        // console.log("kakao profile", profile);
        try {
          const findUser = await User.findAll({
            where: {
              // kakao_nickname: profile._json.nickname,
              kakao_nickname: profile.username,
              // kakao_account_email: profile._json.kakao_account_email,
            },
            raw: true,
          });
          if (findUser.length == 1) {
            // console.log(findUser);
            // console.log(req.query);
            // console.log(req);
            // console.log(req.session.email);
            console.log("해당 회원 가입");
            // console.log(req.params);
            // console.log(req.session);
            // console.log(findUser);
            done(null, findUser);
          } else {
            // let email = JSON.stringify(req.session.email.email);
            let email = req.session.email;
            console.log(req.session.email);
            const newUser = User.create({
              // kakao_account_email: profile._json.kakao_account_email,
              kakao_nickname: profile.username,
              kakao_account_email: email,
            });
            done(null, newUser);
          }
        } catch (err) {
          console.log(err);
          done(null, null);
        }
      }
    )
  );
};
```

1. 카카오 전략에서 DB에서 가입 이력 조사

`kakao.js`

```jsx
...

async (req, accessToken, refreshToken, profile, done) => {
        // console.log(req);
        // console.log("kakao profile", profile);
        try {
          const findUser = await User.findAll({
            where: {
              // kakao_nickname: profile._json.nickname,
              kakao_nickname: profile.username,
              // kakao_account_email: profile._json.kakao_account_email,
            },
            raw: true,
          });

...
```

1. 가입 이력이 있으면 성공 done()을 보내고, 없다면 바로 회원가입 시키고 성공 done()을 보냄
    
    ![img.png](Kakao%20Login%20API%20%5BNode%20js%5D%20dd8ae7485e1f45239f72ec0d76589c93/img%201.png)
    

`kakao.js`

```jsx
...

 if (findUser.length == 1) {
            // console.log(findUser);
            // console.log(req.query);
            // console.log(req);
            // console.log(req.session.email);
            console.log("해당 회원 가입");
            // console.log(req.params);
            // console.log(req.session);
            // console.log(findUser);
            done(null, findUser);
          } else {
            // let email = JSON.stringify(req.session.email.email);
            let email = req.session.email;
            console.log(req.session.email);
            const newUser = User.create({
              // kakao_account_email: profile._json.kakao_account_email,
              kakao_nickname: profile.username,
              kakao_account_email: email,
            });
            done(null, newUser);
          }

...
```

1. 클라이언트에 세션 쿠키를 보냄으로서 로그인 인증 완료

# router

---

**로컬 로그인과 다른 점은 passport.authenticate 메서드에 콜백 함수를 제공하지 않는다는 점이다.** 카카오 로그인은 로그인 성공 시내부적으로 req.login을 호출하므로, 우리가 직접 호출할 필요가 없다.콜백 함수 대신 로그인에 실패했을 때 어디로 이동할지를failureRedirect속성에 적어준다.성공했을 때에도 어디로 이동할지를 미들웨어에 적어준다.

```jsx
router.get(
  // TODO: 실제 서비스에서 수정 필요
  "/redirect",
  passport.authenticate("kakao", {
    successRedirect: "http://localhost:7001/check",
    failureRedirect: "/error",
    successFlash: true,
    failureFlash: true,
  }),
  (req, res) => {
    console.log(`redirect 실행`);
    res.redirect("/");
  }
);
```

# Reference

---

1. [https://www.a-mean-blog.com/ko/blog/Node-JS-API/_/JWT-JSON-Web-Token-로-로그인-REST-API-만들기](https://www.a-mean-blog.com/ko/blog/Node-JS-API/_/JWT-JSON-Web-Token-%EB%A1%9C-%EB%A1%9C%EA%B7%B8%EC%9D%B8-REST-API-%EB%A7%8C%EB%93%A4%EA%B8%B0)
→ 일반적인 세션을 이용한 로그인
2. [https://blog.jdriven.com/2014/10/stateless-spring-security-part-2-stateless-authentication/](https://blog.jdriven.com/2014/10/stateless-spring-security-part-2-stateless-authentication/)
→ Stateless Spring Security Part 2: Stateless Authentication
3. [https://www.a-mean-blog.com/ko/blog/토막글/_/REST와-RESTful-API](https://www.a-mean-blog.com/ko/blog/%ED%86%A0%EB%A7%89%EA%B8%80/_/REST%EC%99%80-RESTful-API)
→ REST? RESTful?
4. [https://stackoverflow.com/questions/13916620/rest-api-login-pattern](https://stackoverflow.com/questions/13916620/rest-api-login-pattern)
→ RESTful API에서 세션을 사용하지 않아야 하는 이유
5. [https://velog.io/@cada/토근-기반-인증에서-bearer는-무엇일까](https://velog.io/@cada/%ED%86%A0%EA%B7%BC-%EA%B8%B0%EB%B0%98-%EC%9D%B8%EC%A6%9D%EC%97%90%EC%84%9C-bearer%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C)
→ Token 기반 인증이란 1
6. [https://www.okta.com/kr/identity-101/what-is-token-based-authentication/](https://www.okta.com/kr/identity-101/what-is-token-based-authentication/)
→ Token 기반 인증이란 2
7. [https://stackoverflow.com/questions/38986005/what-is-the-purpose-of-a-refresh-token](https://stackoverflow.com/questions/38986005/what-is-the-purpose-of-a-refresh-token)
→ Refresh Token의 목적
8. [https://www.rfc-editor.org/rfc/rfc6749#section-1.4](https://www.rfc-editor.org/rfc/rfc6749#section-1.4)
→ The OAuth 2.0 Authorization Framework 공식문서
9. [https://velog.io/@kjhxxxx/OAuth-OAuth-2.0-이란](https://velog.io/@kjhxxxx/OAuth-OAuth-2.0-%EC%9D%B4%EB%9E%80)
→ OAuth란
10. [https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api)
→ 카카오 로그인 시퀀스
11. [https://inpa.tistory.com/entry/NODE-📚-카카오-로그인-Passport-구현](https://inpa.tistory.com/entry/NODE-%F0%9F%93%9A-%EC%B9%B4%EC%B9%B4%EC%98%A4-%EB%A1%9C%EA%B7%B8%EC%9D%B8-Passport-%EA%B5%AC%ED%98%84)
→ Node.js 카카오 로그인 Passport 사용하기