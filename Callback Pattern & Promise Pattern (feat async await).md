# Callback Pattern & Promise Pattern (feat.async/await)

Created: 2022년 8월 12일 오전 10:37
Last Edited Time: 2022년 8월 23일 오후 3:22
State: Done
Tag: node.js

# Callback Pattern이란

---

자바스크립트 호스트 환경이 제공하는 여러 함수를 사용하면 *비동기(asynchronous)* 동작을 스케줄링 할 수 있습니다

**비동기식 프로그래밍**에서는 **파일 읽기 또는 네트워크 요청 수행과 같은 일부 작업을 백그라운드 작업으로 실행**할 수 있습니다. 비동기 작업이 호출되면 이전 작업이 아직 완료되지 않은 경우에도 다음 작업이 즉시 실행됩니다. Node.js에서 비동기 작업의 완료를 통지받는 가장 기본적인 메커니즘은 콜백입니다. 콜백은 비동기 작업의 결과를 가지고 런타임에 의해 호출되는 함수일 뿐입니다. 즉, **다른 함수에 매개변수로 넘겨주어 때가되면 나중에 호출(called back)하는 개념이다.**

만약 스크립트 로딩이 끝나자마자 이 스크립트를 사용해 무언가를 해야만 하는 상황이 있다고 가정해 보자. 스크립트 안에 다양한 함수가 정의되어 있고, 우리는 스크립트 내부에 있는 그 함수 *- 실행하고자 하는 그 함수를 newFunction()이라고 하자 -* 를 실행하길 원하는 상황이다. 

**스크립트 호출한 뒤, 스크립트 내부함수인 newFunction()을 호출하면 JavaScript는 비동기 기반이므로 newFunction()이 존재하지 않는다는 에러코드를 반환한다.**

```jsx
loadScript('/my/script.js'); // script.js엔 "function newFunction() {…}"이 있습니다.
newFunction(); // 함수가 존재하지 않는다는 에러가 발생합니다!
```

이제 여기서 콜백함수를 추가하여 1) 스크립트 선호출 이후 2) 스크립트 내부함수는 나중에 호출(called back) 할 수 있게 해보자. 아래 코드와 같이 loadScript 함수에 callback을 인자로 넣어주자.

```jsx
function loadScript(src, callback) {
	// 스크립트 선언 및 경로 설정
  let script = document.createElement('script');
  script.src = src;
	// 스크립트 로딩
  script.onload = () => callback(script);
	// document객체에 srcipt를 추가한다.
  document.head.append(script);
}
```

이후에 콜백 함수 안에서 원했던 대로 스크립트 내부에 있는 함수를 마음대로 호출하여 사용할 수 있게 하면 콜백 함수 완성이다.

```jsx
loadScript('/my/script.js', function() {
  // 콜백 함수는 스크립트 로드가 끝나면 실행됩니다.
  newFunction(); // 이제 함수 호출이 제대로 동작합니다.
  ...
});
```

**콜백이 유용한 이유는, 콜백 함수만을 바꿔줌으로서하나의 함수를 여러가지로 응용할 수 있기 때문이다.** 아래 처럼 코딩하여 함수를 나눠줌으로써 코드를 재활용 할 수 있고, 관리도 더 쉬워지게 된다.

```jsx
function introduce (lastName, firstName, callback) {
    var fullName = lastName + firstName;
    callback(fullName);
}
 
function say_hello (name) {
    console.log("안녕하세요 제 이름은 " + name + "입니다");
}
function say_bye (name) {
    console.log("지금까지 " + name + "이었습니다. 안녕히계세요");
}
introduce("홍", "길동", say_hello);
// 결과 -> 안녕하세요 제 이름은 홍길동입니다
 
introduce("홍", "길동", say_bye);
// 결과 -> 지금까지 홍길동이었습니다. 안녕히계세요
```

콜백은 비동기 환경에서 특정 함수의 동기 호출을 할 수 있게 하기에 굉장히 유용한 기능이다. 다만 콜백을 잘못 쓰게되면 콜백지옥이라는 굉장히 안 좋은 코드를 짜게 될 수도 있다는 단점이 존재한다.

## **콜백지옥**

```jsx
function add(x, callback) {
    let sum = x + x;
    console.log(sum);
    callback(sum);
}
 
add(2, function(result) {
    add(result, function(result) {
        add(result, function(result) {
            console.log('finish!!');
        })
    })
})
 
/*
4
8
16
finish!!
*/
```

![Untitled](Callback%20Pattern%20&%20Promise%20Pattern%20(feat%20async%20awa%20df425ca884e3455eb1b36cda398d8bcc/Untitled.png)

# Promise Pattern

---

콜백을 받는 함수를 프라미스를 반환하는 함수로 바꾸는 것을 '프라미스화(promisification)'라고 합니다. 파일시스템 접근이나 HTTP 리퀘스트와 같은 비동기 프로세스가 시작되면, 호출자에게 프로세스가 종료되었음을 알려야하는 상황이 온다. 이 때 프로미스를 사용할 수 있다. 여기서 ***프라미스란 백그라운드에서 실행된 결과를 핸들링하는 객체이다.***

기능을 구현 하다 보면 콜백보다는 프라미스가 더 편리하기 때문에 콜백 기반 함수와 라이브러리를 프라미스를 반환하는 함수로 바꾸는 게 좋은 경우가 종종 생길 겁니다. Promise를 사용하여 콜백지옥을 막을 수는 있다.~~다만, then절 지옥을 생성할 수도 있게된다.~~

### 예제코드 #1

아래는 이전에 설명할 때 사용되었던 스크립트 동기로딩 이후 스크립트 내부 함수를 사용하는 예제를 콜백 패턴으로 작성한 코드이다.

```jsx
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`${src}를 불러오는 도중에 에러가 발생함`));

  document.head.append(script);
}

// 사용법:
// loadScript('path/script.js', (err, script) => {...})
```

`loadScript(src, callback)`를 이제 프라미스화해봅시다. 새로운 함수 `loadScriptPromise(src)`
는 `loadScript`와 동일하게 동작하지만 `callback`을 제외한 `src`만 인수로 받아야 하고, 프라미스를 반환해야 합니다.

```jsx
let loadScriptPromise = function(src) {
  return new Promise((resolve, reject) => {
    loadScript(src, (err, script) => { // err이면 reject실행, scipt면 resolve.
      if (err) reject(err)
      else resolve(script);
    });
  })
}

// 사용법:
// loadScriptPromise('path/script.js').then(...)
```

`loadScriptPromise`는 기존 함수 `loadScript`에 모든 일을 위임합니다. `loadScript`의 콜백은 스크립트 로딩 상태에 따라 `이행(reslove)` 혹은 `거부(reject)`상태의 프라미스를 반환합니다.

### 예제코드 #2

강의에서의 더 쉬운 예제 코드를 살펴보자.

```jsx
const condition = true; // true이면 resolve, false이면 reject
const promise = new Promise((resolve,reject) => {
	if(condition){
		resolve('성공');
	}else{
		reject('실패');
	}
});
// 다른 코드 삽입
promise
.then((message) => {
	console.log(message); //성공(resolve)한 경우 실행
})
.catch((error) => {
	console.error(error); // 실패(reject)한 경우 실행
})
```

### 예제코드 #3

아래 콜백 코드를 프로미스화하여 두 번째 코드로 만들어본다.

```jsx
function findAndSaveUser(Users) {
	Users.findOne({})
		if(err){
			return console.error(err);
		}
		user.name = 'zero';
		user.save((err) => { // 두 번째 콜백
			if (err) {
				return console.error(err);
			}
			User.findOne({ gender: 'm'}, (err, user) => { // 세 번째 콜백
				// 생략
			});
		});
	});
}
```

이제 이를 프로미스화해보자.

```jsx
function findAndSaveUser(Users) {
	Users.findOne({})
	.then((user)=>{
		user.name='zero';
		return user.save();
	})
	.then((user)=>{
		return Users.findOne({gender:'m'});
	})
	.then((user)=>{
		// 생략
	})
	.catch(err => {
		console.error(err);
	});
}
```

## Promise.all(), **Promise.allSettled()**

만약 프로미스가 여러 개 있을 때, 어떻게 한 번에 결과값을 받아올까? 

`Promise.all()`에 넣으면 모두 resolve될 때까지 기다렸다가 then으로 넘어간 뒤, 각 프로미스 결과값을 배열로 넣어버린다. 

하지만 `Promise.all()` 사용 시, **주어진 Promise 중 하나라도 Reject 되는 경우 모두 이행취소가 되어버힌다. 근래 들어서는 성공을하던 실패를 하던 무조건 일단 배열에 결과값을 담아주는 `Promise.allSettled()`라는 함수를 쓴다.** 예제는 아래 레퍼런스 참조했던 사이트에서 찾아보자.

<aside>
⚠️ 프라미스화는 곧 배우게 될 `async/await`와 함께 사용하면 더 좋습니다. 다만, 콜백을 완전히 대체하지는 못한다는 사실을 기억해 두시기 바랍니다.

**프라미스는 하나의 결과만 가질 수 있지만, 콜백은 여러 번 호출할 수 있기 때문입니다.**

따라서 프라미스화는 콜백을 단 한 번 호출하는 함수에만 적용하시기 바랍니다. 프라미스화한 함수의 콜백을 여러 번 호출해도, 두 번째부터는 무시됩니다.

</aside>

# async/await

---

앞 서 Promise 문법을 사용하면 다음과 같은 이점을 갖고 있었다. 

1. 각 비동기 실행된 함수의 결과값을 들고 있을 수 있게하고 
2. 이에 따라 에러처리를 할 수 있게 catch문을 붙여줄 수 있었다. (아래 코드로 복습하자)

async/await 문법은 위 코드처럼 프로미스를 사용한 코드를 한 번 더 깔끔하게 줄여준다.

await를 사용하기 위해서 다음 단계를 거친다.

- **함수 선언부를 `async function`으로 교체**
// 항상 함수는 async function이여야 한다.
- **프로미스 앞에 await를 붙여준다. 이렇게 하면 프로미스가 resolve될 때까지 기다린 후, 다음 로직으로 넘어간다.**
// Ex) `let user = await Users.findOne({});`
     → `await Users.findOne({})`이 resolve될 때까지 기다린 뒤, user 변수를 초기화

```jsx
function findAndSaveUser(Users) {
	Users.findOne({})
	.then((user)=>{
		user.name='zero';
		return user.save();
	})
	.then((user)=>{
		return Users.findOne({gender:'m'});
	})
	.then((user)=>{
		// 생략
	})
	.catch(err => {
		console.error(err);
	});
}
```

위 코드를 async/await를 사용하여 짧게 바꿔보자.

```jsx
async function findAndSaveUser(Users) {
	let user = await Users.findOne({});
	user.name = 'zero';
	user = await user.save();
	user = await Users.findOne({gender:'m'});
	// 생략
}
```

- `변수 = await 프로미스;`
프로미스가 resolve된 값이 변수에 저장
- `변수 await 값;`
그 값이 변수에 저장

async/await의 에러 처리를 하기 위해서는 try-catch문을 사용해준다.

```jsx
async function findAndSaveUser(Users) {
	try{
		let user = await Users.findOne({});
		user.name = 'zero';
		user = await user.save();
		user = await Users.findOne({gender:'m'});
		// 생략
	} catch (error) {
		console.error(error);
	}
}
```

for문과 async/await를 같이 써서 Promise.all을 대체할 수도 있다. 이는 노드10버전부터 지원하는 ES2018문법이다.

```jsx
// 프로미스1,프로미스2 선언
const promise1 = Promise.resolve('성공'); 
const promise2 = Promise.resolve('성공2');
// async 익명함수 선언 이후 for문을 통해 모든 프로미스 결과값 받아오기 : Promise.all 대체
(async() => {
	for await (promise of [promise1, promise2]) {
		console.log(promise);
	}
})();
```

# Reference

---

[8. Callback Async Programming](JS%20%E1%84%80%E1%85%B5%E1%84%87%E1%85%A9%E1%86%AB%20%E1%84%86%E1%85%AE%E1%86%AB%E1%84%87%E1%85%A5%E1%86%B8%20%E1%84%8B%E1%85%B5%E1%86%A8%E1%84%92%E1%85%B5%E1%84%80%E1%85%B5%20%5BNode%20js%20%E1%84%80%E1%85%AD%E1%84%80%E1%85%AA%E1%84%89%E1%85%A5%5D%20b1bb6370cffa48f7a843341a7201e969.md) 

1. [https://junhokims.tistory.com/41](https://junhokims.tistory.com/41)
2. [https://neep305.github.io/2019-12-12-about-javascript-callback-function/](https://neep305.github.io/2019-12-12-about-javascript-callback-function/)
3. [https://ko.javascript.info/callbacks](https://ko.javascript.info/callbacks)
4. [https://ko.javascript.info/promisify](https://ko.javascript.info/promisify)
5. [https://www.mariokandut.com/understanding-promises-in-node-js/](https://www.mariokandut.com/understanding-promises-in-node-js/)
6. [https://www.geeksforgeeks.org/promises-in-node-js/](https://www.geeksforgeeks.org/promises-in-node-js/)
7. [https://ko.javascript.info/async-await](https://ko.javascript.info/async-await)