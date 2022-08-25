# 본격적인 Node 체험학습

Created: 2022년 8월 9일 오후 2:29
Last Edited Time: 2022년 8월 23일 오후 3:23
State: Done
Tag: node.js

# REPL 사용

---

- Read
코드를 읽고
- Evaluate
코드를 평가하고 (이게 무슨 의미인지 해석)
- Print
함수의 실행값을 출력
- Loop
위를 계속 반복

한 줄 짜리 코드 궁금할 때 사용함 ㅋㅋㅋ

# 모듈 사용

---

어…그냥 import/export하는 거다…

뭐..더 알고 싶으면 모듈에 관해 정리해두었던 페이지를 참조하자.

[Javascript 모듈 추출](Javascript%20%E1%84%86%E1%85%A9%E1%84%83%E1%85%B2%E1%86%AF%20%E1%84%8E%E1%85%AE%E1%84%8E%E1%85%AE%E1%86%AF%201731f82bc33d489bac50b5de9cd854ac.md)

# 노드 내장 객체

---

~~내장객체는 많아도 너무 많다. 외우지 말고 나중에 필요할 때 들춰보는 식으로 하자. ^^~~

## 1. global객체

---

브라우저에서의 window와 같은 전역객체. 최근에는 globalThis로 이름을 변경했으나 예전 브라우저에서 호환되지 않는다. 

또한 global 객체 속성 값 대입을 통해 파일 간 공유가 가능하지만 절대 남용하지 마라. 어디서 대입했는지 찾기가 어려워 코드 유지보수가 어렵다.

## 2. console

---

console 또한 global 객체 안에 들어있는데, 보통 디버깅을 위해 사용된다.

> The `node:console` module provides a simple debugging console that is similar to the JavaScript console mechanism provided by web browsers.
> 

## 3. timer

---

- setTimeout(콜백 함수, 밀리초)
- setInterval(콜백 함수, 밀리초)
- setImmediate(콜백 함수)

위 세 친구들은 콜백 함수를 실행하는데, 실행한 콜백 함수를 백 그라운드로 보내서, 동시에 실행되도록 한다. 

**즉, 타이머를 사용하여 콜백함수를 백그라운드로 보내는 대표적인 함수들이다.**

그렇다면 각각 무슨 차이점이 있나?

| timer | 기능 | 취소함수 |
| --- | --- | --- |
| setTimeout(콜백 함수, 밀리초) | 주어진 밀리초 이후에 콜백함수를 실행 | clearTimeout(아이디) |
| setInterval(콜백 함수, 밀리초) | 주어진 밀리초 간격으로 콜백함수를 반복 실행 | clearInterval(아이디) |
| setImmediate(콜백 함수) | 주어진 콜백함수를 즉시 호출 | clearImmediate(아이디) |

위 세 타이머 함수들은 선언하여 모두 id변수를 통해 함수를 저장해주도록 한다. 언제 취소할 수 있게 되는지 모르기 때문이다. 이렇게 id변수를 사용하면 나중에 clear~로 타이머를 취소할 수 있게 해줄 수 있게 할 수 있다.

자세히 보면 `setImmediate(콜백 함수)`는  `setTimeout(()=>{}, 0)`이랑 똑같은 동작을 한다. 그렇다면 `setImmediate(콜백 함수)`을 도대체 왜 써야하는 걸까? 이유는 간단하다.

`setImmediate(콜백 함수)`은 순서가 보장되지 않으므로 `setTimeout(()=>{}, 0)` 은 사용하지 않는 것이 좋다.

## 4. __filename,__dirname :: 파일경로

---

![Untitled](%E1%84%87%E1%85%A9%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%B5%E1%86%AB%20Node%20%E1%84%8E%E1%85%A6%E1%84%92%E1%85%A5%E1%86%B7%E1%84%92%E1%85%A1%E1%86%A8%E1%84%89%E1%85%B3%E1%86%B8%20e32f99de65f6433aa11d35b2e434babc/Untitled.png)

- __filename : 현재 실행 중인 파일 경로
- __dirname : 현재 실행 중인 폴더 경로

## 5. module, exports

---

module.exports와 exports가 같은 객체를 참조한다. 따라서 exports 객체에 add함수를 넣으면 module.exports에도 add 함수가 들어간다.

**주의해야할 점은 module.exports과 exports의 참조관계가 끊기면 안 된다!**

**module.exports에는 어떤 값이든 대입가능하지만, exports에는 반드시 객체처럼 속성명과 속성값을 대입해야한다.**

exports와 module.exports에는 참조관계가 있으므로 한 모듈에는 exports 객체와 module.exports를 동시에 사용하지 않는 것이 좋다!

참고로 노드에서 모듈을 사용할 때는 두 가지 특징을 지원한다. 첫 째로 캐싱을 사용할 수 있고, 둘 째로 순환참조를 지원한다. 이를 알아보자.

1. **require() cache**

`require(‘./A’)`을 통해서 한 번 불러왔다면 또 다시 불러올 때는 캐시에서 불러온다. 캐싱 사용은 console.log(require)를 통해 require 객체의 내부 속 cach에 할당되어있는 주소를 참조하면 된다.

1. **순환참조**

만약 아래와 같은 경우가 있다고 해보자.

`dep1.js`

```jsx
require('./dep2');

module.exports = {
	hello : 'zerocho',
};
```

`dep2.js`

```jsx
require('./dep1');
```

node에서는 자체적으로 위와 같은 순환참조에 의해 exports가 망가지는 경우를 방지하기 위해 exports에 대해 빈 객체를 강제한다.

## 6. process

---

현재 실행 중인 Node.js 프로세스 정보를 볼 수 있음. 콘솔에 다음을 입력해보자

```jsx
console.log(process.env);           // 컴퓨터 환경과 관련된 정보를 가진 객체 : 환경변수 설정 등등
console.log(process.version);       // node.js의 버전
console.log(process.versions);      // node.js와 연관된 프로그램들의 버전을 가진 객체
console.log(process.arch);          // 프로세서의 아키텍처(arm/ia32/x64)
console.log(process.platform);      // 플랫폼(win32/linux/sunos/freebsd/darwin)
console.log(process.memoryUsage()); // 메모리 사용 정보를 가진 객체
console.log(process.uptime());      // 현재 프로그램이 실행된 시간
```

- process.env

서비스의 중요한 키를 저장하는 공간으로도 사용된다. 중요한 비밀번호와 같은 경우 다음과 같이 process.env의 속성으로 대체한다.

```jsx
const secretId = preocess.env.SECRET_ID;
const secretCode = processs.env.SECRET_CODE;
```

- process.nextTick

이벤트루프가 다른 콜백 함수들보다 nextTick의 콜백 함수를 우선으로 처리하도록 만든다. process.nextTick은 setImmediate나 setTimeout보다 먼저 실행된다. resolve된 Promise 또한 nextTick처럼 다른 콜백들보다 우선시된다. 따라서 process.nextTick과 Promise를 마이크로태스크라고 따로 구분지어 부른다. 아래 예제코드를 살펴보자.

- process.exit

실행 중인 노드 프로세스를 종료한다.

# Reference

---

1. [https://nodejs.org/api/console.html](https://nodejs.org/api/console.html)
→ 콘솔
2. [https://manbalboy.github.io/javascript/nodetimer.html](https://manbalboy.github.io/javascript/nodetimer.html)
→ 타이머
3. [https://sukth09.tistory.com/30](https://sukth09.tistory.com/30)
→ 파일경로
4. [https://stackoverflow.com/questions/9210542/node-js-require-cache-possible-to-invalidate](https://stackoverflow.com/questions/9210542/node-js-require-cache-possible-to-invalidate)
→ require() cache
5. [https://baeharam.netlify.app/posts/javascript/JS-Task와-Microtask의-동작방식](https://baeharam.netlify.app/posts/javascript/JS-Task%EC%99%80-Microtask%EC%9D%98-%EB%8F%99%EC%9E%91%EB%B0%A9%EC%8B%9D)
→ process.nextTick(콜백)