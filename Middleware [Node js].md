# Middleware [Node.js]

Created: 2022년 8월 22일 오후 1:42
Last Edited Time: 2022년 8월 25일 오전 9:28
State: In progress
Tag: node.js

# Express 처리 순서

---

1. app 생성
2. app 관련 설정 처리
3. 미들웨어 처리
4. 라우팅
5. 에러 미들웨어 처리
6. listen을 통한 서버 실행

<aside>
📖 Middleware? Router?

Middleware 
: Middleware functions are functions that have access to the request object (req), the response object (res), and the next middleware function in the application’s request-response cycle.
Router
: Routing refers to how an application’s endpoints (URIs) respond to client requests

</aside>

# Middleware

---

요청과 응답의 중간(Middle)에 위치하여 미들웨어라고 부름. 라우터와 에러핸들러 또한 미들웨어 일종임.

미들웨어는 app.use()와 함게 사용됨. 보통 app.use() 안에 미들웨어 함수를 집어넣어서 사용한다.

```jsx
const express = require('express');
const path = require('path');
const app = express();

app.use((req, res, next) => {
    console.log('모든 요청에 실행');
		next();
});
```

아래와 같이 next()를 통해 하나씩 여러 개의 미들웨어를 실행할 수 있다.

```jsx
app.use((req, res, next) => {
    console.log('1번째 미들웨어 실행');
		next();
}, (req,res,next) => {
    console.log('2번째 미들웨어 실행');
		next();
},(req,res,next) => {
    console.log('3번째 미들웨어 실행');
		next();
},(req,res,next) => {
    console.log('4번째 미들웨어 실행');
		next();
});
```

# 에러 미들웨어

---

```jsx
app.use((req,res,next)=>{
    res.send('404지롱');
});

// 에러 미들웨어에는 next 매개변수를 꼭 넣어주어야 함.
app.use((err,req,res,next)=>{
    console.error(err);
    res.status(404).send('에러났지롱 안 알려주지롱');
})
```

- 에러 처리 미들웨어 생성
    - 꼭 네 개의 인자가 있어야만 에러처리 미들웨어 동작함
        - err / req / res / next
- 모든 라우팅 이후에도 에러 핸들링이 되지 않을 때 담당하는 404 처리 미들웨어를 생성
    - 라우터들과 에러 처리 미들웨어 사이에 생성

<aside>
📖 express모듈에서는 res 자체의 속 의미는 res.status(200)가 내장되어있다. 따라서 404 및 에러 핸들링 미들웨어 작성 시에는, `res.send( … )`보다 `**res.status(404).send( … )`를 작성하여 해커가 오류에 대한 파악을 불가능하게 해주자.**

</aside>

# `Error: Can't render headers after they are sent to the client.` 오류

---

- res.send 이후 res.writeHead() 호출하는 경우
- 한 라우터 안에서 res에 send를 여러 번 호출하는 경우

위 두 가지 케이스로 인해 헤더 오류가 발생하니 잊지 말기를 바란다.

# Express에서의 send와 json 요청 방식

---

HTTP 모듈을 사용하여 아래 처럼 헤더를 통해 응답 및  json을 보낼 수 있었다. 

다만 express 모듈에서는 편리하게 send() 과 json()으로 한 번에 보낼 수 있다.

```jsx
// resonse
app.get('/',(req,res)=>{
	res.setHeader('Content-Type','text/html');
	res.end('안녕하세요');
});
// json
app.get('/',(req,res)=>{
	res.writeHead(200, {'Content-Type' : 'application/json'});
	res.end(JSON.stringify({hello:'zerocho'}));
});
```

⬇️

```jsx
// resonse
app.get('/',(req,res)=>{
	res.status(200).send('안녕하세요');
});
//json
app.get('/',(req,res)=>{
	res.json({hello:'zerocho'});
});
```

# next()안에 인수 넣기

---

![Untitled](Middleware%20%5BNode%20js%5D%209fc12ce223e946adb64fe9435114d48b/Untitled.png)

- next() : 다음 미들웨어로
- next(’route’) : 다음 라우터로
- next(error) : 에러 핸들러로

```jsx
app.use((req,res,next)=>{
	...
	next(); // 다음 미들웨어로 이동
}, (req,res,next)=>{
	try{
		...
	} catch(error){
		next(error); // 에러 핸들러 미들웨어로 이동
	}
});
```

위 예제를 살펴보자.

첫 미들웨어 통과 이후 next()를 통해 다음 미들웨어로 넘어갔다. // 3번째 라인

두 번째 미들웨어에서 try-catch문을 통해 실행 이후 에러 발생을 잡아냈다. 잡아낸 에러를 next에 인수로 집어넣어 next(error); 호출 시, 에러 핸들러 미들웨어로 곧장 이동한다. // 8번째 라인

# morgan 미들웨어 사용하기

---

morgan은 **요청과 응답에 대한 정보를 콘솔에 기록.** 위 코드에서는 인수로dev를 넣었는데 이 외에combined, common, short, tiny 등을 넣을 수 있다. 개발 환경에서는dev를, 배포 환경에서는combined를 이용하면 좋다.

- `app.use(morgan(’dev’);`
- `app.use(morgan(’combined’);`  // combined를 하면 로그가 더 자세해진다.

# cookie-parser 미들웨어 사용하기

---

```jsx
app.use(cookieParser());

app.get('/',(req,res,next)=>{
    req.cookies;
		// 암호화된 쿠키 사용!
    req.signedCookies;
    // 'Set-Cookie' : `name=${encodeURIComponent(name)}; Expires=${expires.toGMTString()}; HttpOnly;Path=\`,
    req.cookies('name',encodeURIComponent(name),{
        expires: new Date(),
        httpOnly: true,
        path:'/',
    })
})
```

# body-parser 미들웨어 사용하기

---

본문에 있는 데이터를 해석하여 req.body 객체로 만들어주는 미들웨어.

express 모듈 안에 내장 되어있음 (예전에는 따로 설치해주었어야함)

body-parser를 사용하지 않고 기존 Http모듈에서는 파싱을 다음 과정을 거쳤어야함.

1. 요청의 body를 stream 형식으로 받음
2. body를 다 받은 후 JSON.parse를 통해 json으로 파싱

```jsx
if (req.method === 'POST') {
      if (req.url === '/user') {
        let body = '';
        // 요청의 body를 stream 형식으로 받음
        req.on('data', (data) => {
          body += data;
        });
        // 요청의 body를 다 받은 후 실행됨
        return req.on('end', () => {
          console.log('POST 본문(Body):', body);
          const { name } = JSON.parse(body);
          const id = Date.now();
          users[id] = name;
          res.writeHead(201, { 'Content-Type': 'text/plain; charset=utf-8' });
          res.end('ok');
        });
      }
```

이를 아래와 같이 간단하게 바꿔준다.

```jsx
app.use(express.json());
// form 파싱할 때 쿼리스트링 파싱 : ture이면 qs,false이면 querystring
app.use(express.urlencoded({extended:true}));

app.get('/',(req,res,next)=>{
    var name = req.body.name;
    res.send(name);
})
```

# 미들웨어 간 데이터 넘기기

---

함부로 데이터를 1)변수에 넣는다거나, 2)app.set&app.get을 사용 시, 공유데이터 취급되어버리기 때문에 보안에 취약해진다. 따라서 다음 방법을 통해 미들웨어 간에 데이터 넘겨주자

1. req.session.data에 넣기

```jsx
app.use((req,res,next)=>{
	req.session.data = 'zerocho';
});
app.get('/',(req,res,next)=>{
	req.session.data ...
});
```

위처럼 req.session을 사용 시, 다음 세션 접근 시에도 데이터가 존재할 수 있다.

1. req.data에 넣기

```jsx
app.use((req,res,next)=>{
	req.data = 'zerocho';
});
app.get('/',(req,res,next)=>{
	req.data ...
});
```

이렇게하면 다음에 접근 시에 데이터가 존재하지 않을 수도 있다.

# 미들웨어 안에 미들웨어를 넣기

---

기존의 미들웨어 기능을 확장하기 위해, 그리고 조건에 따라 다른 미들웨어를 적용하는 패턴이다.

```jsx
app.use(morgan('dev')); // 보통 이렇게 바로 app.use()에 미들웨어를 넣어서 사용하지만 

// app.use()의 콜백 함수 내에 미들웨어를 넣는 방법을 일컫는다.
app.use((req, res, next) => {    
	morgan('dev')(req, res, next);
})

```

이때, 미들웨어에함수호출 인자를 명시해주는데,원리는 간단하다.

[https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FA8fh7%2FbtrqNMP8H3t%2FGnPxppQbaSnszT4pBpUb7k%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FA8fh7%2FbtrqNMP8H3t%2FGnPxppQbaSnszT4pBpUb7k%2Fimg.png)

예를들어, `app.use(morgan('dev'))`의 인자 함수 부분 `**morgan('dev')`도 사실 메소드 내부 내에서 위 사진 처럼 함수 인자를 전달받아 콜백 함수 호출**이 이루어 진다.

이 원리를 이용해서(req, res, next) => { ... } 콜백 함수 부분 안에다 미들웨어를 사용할때 직접 함수 인자를 붙여주는 것이다.

이렇게 번거롭게 사용하는 이유는,조건식을 붙여서 조건에 따른 미들웨어를 적용 할 수 있기 때문이다.

```jsx
app.use((req,res,next)=>{
	// 만일 로그인이 되어있다면
	if(req.session.id){
		express.static(__dirname,'public')(req,res,next) // 기본요청은 public파일로 고정
	}else{
		next();
	}
});
```

# Reference

---

1. [https://inpa.tistory.com/entry/EXPRESS-📚-morgan-미들웨어](https://inpa.tistory.com/entry/EXPRESS-%F0%9F%93%9A-morgan-%EB%AF%B8%EB%93%A4%EC%9B%A8%EC%96%B4)
→ morgan 미들웨어 사용
2. [https://inpa.tistory.com/entry/EXPRESS-📚-bodyParser-cookieParser-미들웨어?category=898807](https://inpa.tistory.com/entry/EXPRESS-%F0%9F%93%9A-bodyParser-cookieParser-%EB%AF%B8%EB%93%A4%EC%9B%A8%EC%96%B4?category=898807)
→ 쿠키 파서 미들웨어 사용
3. [https://inpa.tistory.com/entry/EXPRESS-📚-미들웨어-확장법](https://inpa.tistory.com/entry/EXPRESS-%F0%9F%93%9A-%EB%AF%B8%EB%93%A4%EC%9B%A8%EC%96%B4-%ED%99%95%EC%9E%A5%EB%B2%95)
⇒ 미들웨어 패턴