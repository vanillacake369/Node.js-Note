# Node.js로 요청과 응답

Created: 2022년 8월 14일 오후 5:20
Last Edited Time: 2022년 8월 23일 오후 3:22
State: Done
Tag: node.js

# REST API Server

---

```jsx
const http = require('http');
const fs = require('fs').promises;

const users = {}; // 데이터 저장용

http.createServer(async (req, res) => {
  try {
    if (req.method === 'GET') {
      if (req.url === '/') {
        const data = await fs.readFile('./restFront.html');
        res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
        return res.end(data);
      } else if (req.url === '/about') {
        const data = await fs.readFile('./about.html');
        res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
        return res.end(data);
      } else if (req.url === '/users') {
        res.writeHead(200, { 'Content-Type': 'application/json; charset=utf-8' });
        return res.end(JSON.stringify(users));
      }
      // /도 /about도 /users도 아니면
      try {
        const data = await fs.readFile(`.${req.url}`);
        return res.end(data);
      } catch (err) {
        // 주소에 해당하는 라우트를 못 찾았다는 404 Not Found error 발생
      }
    } else if (req.method === 'POST') {
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
    } else if (req.method === 'PUT') {
      if (req.url.startsWith('/user/')) {
        const key = req.url.split('/')[2];
        let body = '';
        req.on('data', (data) => {
          body += data;
        });
        return req.on('end', () => {
          console.log('PUT 본문(Body):', body);
          users[key] = JSON.parse(body).name;
          res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
          return res.end('ok');
        });
      }
    } else if (req.method === 'DELETE') {
      if (req.url.startsWith('/user/')) {
        const key = req.url.split('/')[2];
        delete users[key];
        res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
        return res.end('ok');
      }
    }
    res.writeHead(404);
    return res.end('NOT FOUND');
  } catch (err) {
    console.error(err);
    res.writeHead(500, { 'Content-Type': 'text/plain; charset=utf-8' });
    res.end(err.message);
  }
})
  .listen(8082, () => {
    console.log('8082번 포트에서 서버 대기 중입니다');
  });
```

- `res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });`

```jsx
http.createServer(async (req, res) => {
  try {
    if (req.method === 'GET') {
      if (req.url === '/') {
        const data = await fs.readFile('./restFront.html');
        res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
        return res.end(data);
      } else if (req.url === '/about') {
        const data = await fs.readFile('./about.html');
        res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
        return res.end(data);
      } else if (req.url === '/users') {
        res.writeHead(200, { 'Content-Type': 'application/json; charset=utf-8' });
        return res.end(JSON.stringify(users));
      }
```

리스폰스 헤더에 정보를 지정해줄 수 있는 코드이다. 다음은 리스폰스 헤더에 대한 데이터 종류이다.

```jsx
200 OK
Access-Control-Allow-Origin: *
Connection: Keep-Alive
Content-Encoding: gzip
Content-Type: text/html; charset=utf-8
```

- `const data = await fs.readFile(`.${req.url}`);`

GET방식에서 여러 if문으로 필터링이 안 되었다면 해당 url에 해당하는 자원파일을 async/awati와 fs모듈을 통해 가져오게 한다.

만약 없다면 catch문으로 err를 잡고 해당하는 라우트를 찾지 못 했다는 err를 처리해준다.

```jsx
http.createServer(async (req, res) => {
			...
      // /도 /about도 /users도 아니면
      try {
        const data = await fs.readFile(`.${req.url}`);
        return res.end(data);
      } catch (err) {
        // 주소에 해당하는 라우트를 못 찾았다는 404 Not Found error 발생
      }
    }
		...
		res.writeHead(404);
		    return res.end('NOT FOUND');
		  } catch (err) {
		    console.error(err);
		    res.writeHead(500, { 'Content-Type': 'text/plain; charset=utf-8' });
		    res.end(err.message);
		  }
		})
		  .listen(8082, () => {
		    console.log('8082번 포트에서 서버 대기 중입니다');
		  });
```

# Reference

---

1. [https://github.com/ZeroCho/nodejs-book/blob/master/ch4/4.2/restServer.js](https://github.com/ZeroCho/nodejs-book/blob/master/ch4/4.2/restServer.js)
→ 예제코드
2. [https://developer.mozilla.org/en-US/docs/Glossary/Response_header](https://developer.mozilla.org/en-US/docs/Glossary/Response_header)
→ 리스폰스 헤더