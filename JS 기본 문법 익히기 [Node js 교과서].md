# JS 기본 문법 익히기 [Node.js 교과서]

Created: 2022년 8월 6일 오후 4:58
Last Edited Time: 2022년 8월 23일 오후 3:23
State: Done
Tag: node.js

# 0. 기본기의 중요성

---

- 내가 코드를 자유자재로 윤활하게 적재적소에 맞추어 함수형 혹은 객체지향의 형식으로 작성할 수 있을 줄 알아야 함.
- 추가로 더 중요한 것은 타인이 작성한 코드를 리뷰할 때, 그 사람이 어떤 스타일로 작성하게 되는지 모르기 때문에 미리 모든 기본기와 문법에 대해 익숙해야함.

# 1. 호출스택, 백그라운드, 태스크 큐

---

- 호출스택과 백그라운드는 동시에 실행된다.
자바스크립트는 싱글 스레드임에도 불구하고 백그라운드가 OS영역이기 때문에(C++로 짜여있기 때문에) 가능한 부분이다.
- setTimeout은 무조건 Background 가서 실행됨
- Task Queue가 비어있을 때만 Background에서 함수 보냄
- Promise API의 내부까지는 동기로 실행되지만, then state를 만나게 되는 순간 비동기로 실행됨!
따라서 호출 스택에 Promise와 Promise의 내부코드까지 같이 올라간다.
// then의 코드가 비동기이므로 백그라운드로 간다..(??? 잉 왜 ???)
- Task Queue에서는 Promise의 코드(Promise state 혹은 )를 최우선적으로 호출스택으로 옮겨 실행시킨다. 즉, Promise코드는 다른 코드들보다, 호출스택으로의 최우선권을 갖게 된다.

```jsx
function oneMore(){
    console.log('one more');
}
function run() {
    console.log('run run');
    setTimeout(()=>{
        console.log('wow');
    },0)
    new Promise((resolve) => {
        resolve('hi');
    })
    .then(console.log);
    oneMore();
}

setTimeout(run,5000);
```

> 5초 뒤 아래 결과물들이 출력됨. 왜 이렇게 되는지 이해가 안 된다면 꼭 호출스택,백그라운드,태스크 큐 그려보면서 이해하고 넘어갈 것!
> 
> 
> run run
> one more
> hi
> wow
> 

# 2. 객체 리터럴

---

**예전 문법**

```jsx
var es = 'ES';
var sayNode = function(){
	~A~
};
var oldObject = {
	sayJS : function(){
		~B~
	},
	sayNode : sayNode,
};
oldObject[es+6]= ... ;
console.log(oldObject.ES6); // ==> ...
```

**ES2015+**

```jsx
var es = 'ES';
const newObject = {
	sayJS(){
		console.log('JS');
	},
	sayNode,
	[es+6]:'Fantastic',
};
newObject.sayNode();
newObject.sayJS();
console.log(newObject.ES6);
```

# 3. 화살표 함수

---

**예전 문법**

```jsx
function add1(x,y){
	return x+y;
}
```

**ES2015+**

```jsx
// # 1)
const add2 = (x,y)=>{
	return x+y;
}
// # 2)
const add3 = (x,y)=>x+y;
// # 3)
const obj = (x,y)=>{
	return {x,y};
}
```

#2)에서의 화살표 함수는 아래와 같이 변경할 수 있다. 

```jsx
const add3 = (x,y)=>(x+y);
// 위 문법은 
```

다만 위 문법 자체는 중괄호 이후 바로 return이 나오는 경우에 한해서만 가능하다. 객체를 return 하는 경우에 따라 중괄호 해석을 못 하기 때문에 *- 객체를 의미하는지, 함수의 바디를 의미하는지 애매모호하다 -* 꼭 body의 코드가 return문 한 문장인 경우에 한해서만 쓸 수 있다.

# 4. 화살표 함수에서의 this연산

---

[**화살표 함수에는 'this’가 없습니다](https://ko.javascript.info/arrow-functions#ref-3112).** 화살표 함수 본문에서 `this`에 접근하면, 외부에서 값을 가져온다. 이를 잊지 말아야 한다. 아래 예제를 확인해보자.

```jsx
let group = {
  title: "1모둠",
  students: ["보라", "호진", "지민"],

  showList() {
		*this.students.forEach( // this : group 자체
      student => alert(this.title + ': ' + student) // this : group 자체
    );*
	}
};

group.showList();
```

화살표 함수에는 this가 없으므로 this는 showList가 가리키는 대상, 즉 group이다. 따라서 위 두 this 모두 group을 가르킨다.

반면 아래 예제를 확인해보자.

```jsx
let group = {
  title: "1모둠",
  students: ["보라", "호진", "지민"],

  showList() {
    this.students.forEach(function(student) {
      // TypeError: Cannot read property 'title' of undefined
      alert(this.title + ': ' + student)
    });
  }
};

group.showList();
```

에러는 `forEach`에 전달되는 함수의 `this`가 `undefined` 이어서 발생했습니다. `alert` 함수에서 `undefined.title`에 접근하려 했기 때문에 얼럿 창엔 에러가 출력됩니다. 그런데 화살표 함수는 `this` 자체가 없기 때문에 이런 에러가 발생하지 않습니다.

# 5. nullish 병합 연산자

---

그냥 삼항 연산자인데, 여러 피연산자 중 값이 확정되어있는 피연산자를 찾아내 반환하는 내장 연산자이다.

?? 의 syntax를 사용한다.

예제는 다음과 같다.

```jsx
let firstName = null;
let lastName = null;
let nickName = "바이올렛";

// null이나 undefined가 아닌 첫 번째 피연산자
*alert(firstName ?? lastName ?? nickName ?? "익명의 사용자"); // 바이올렛*
```

# 6. 비구조화 할당 (=구조 분해 할당)

---

예전문법에서는 객체 리터럴에서 각 어트리뷰트를 받아올 때 일일이 변수로 받아왔다면, 새로운 문법에서는 괄호{}와 목표변수를 사용하여 받아올 수 있다.

// 그냥 코드를 보면 이해가 빠르다 ㅋㅋㅋ

**예전 문법**

```jsx
var candyMachine = {
	status : {
		name : 'node',
		count : 5,
	},
	getCandy:function(){
	this.status.count--;
	return this.status.count;
};
var getCandy = candyMachine.getCandy;
var count = candyMachine.status.count;
```

**ES2015+**

```jsx
const candyMachine = {
	status : {
		name : 'node',
		count : 5,
	},
	getCandy() {
	this.status.count--;
	return this.status.count;
};
const { getCandy, status: { count } } = candyMachine;
```

```jsx
let options = {
  size: {
    width: 100,
    height: 200
  },
  items: ["Cake", "Donut"],
  extra: true
};

// 코드를 여러 줄에 걸쳐 작성해 의도하는 바를 명확히 드러냄
let {
  size: { // size는 여기,
    width,
    height
  },
  items: [item1, item2], // items는 여기에 할당함
  title = "Menu" // 분해하려는 객체에 title 프로퍼티가 없으므로 기본값을 사용함
} = options;

alert(title);  // Menu
alert(width);  // 100
alert(height); // 200
alert(item1);  // Cake
alert(item2);  // Donut
```

![Untitled](JS%20%E1%84%80%E1%85%B5%E1%84%87%E1%85%A9%E1%86%AB%20%E1%84%86%E1%85%AE%E1%86%AB%E1%84%87%E1%85%A5%E1%86%B8%20%E1%84%8B%E1%85%B5%E1%86%A8%E1%84%92%E1%85%B5%E1%84%80%E1%85%B5%20%5BNode%20js%20%E1%84%80%E1%85%AD%E1%84%80%E1%85%AA%E1%84%89%E1%85%A5%5D%20b1bb6370cffa48f7a843341a7201e969/Untitled.png)

# 7. Class

---

프로토타입 문법을 잠깐 알아보자.

![Untitled](JS%20%E1%84%80%E1%85%B5%E1%84%87%E1%85%A9%E1%86%AB%20%E1%84%86%E1%85%AE%E1%86%AB%E1%84%87%E1%85%A5%E1%86%B8%20%E1%84%8B%E1%85%B5%E1%86%A8%E1%84%92%E1%85%B5%E1%84%80%E1%85%B5%20%5BNode%20js%20%E1%84%80%E1%85%AD%E1%84%80%E1%85%AA%E1%84%89%E1%85%A5%5D%20b1bb6370cffa48f7a843341a7201e969/Untitled%201.png)

Class는 프로토타입 문법을 깔끔하게 만들어준 문법

![Untitled](JS%20%E1%84%80%E1%85%B5%E1%84%87%E1%85%A9%E1%86%AB%20%E1%84%86%E1%85%AE%E1%86%AB%E1%84%87%E1%85%A5%E1%86%B8%20%E1%84%8B%E1%85%B5%E1%86%A8%E1%84%92%E1%85%B5%E1%84%80%E1%85%B5%20%5BNode%20js%20%E1%84%80%E1%85%AD%E1%84%80%E1%85%AA%E1%84%89%E1%85%A5%5D%20b1bb6370cffa48f7a843341a7201e969/Untitled%202.png)

강의 내내 [prototype](https://ko.javascript.info/prototype-inheritance#ref-3294)이 뭐니, [apply](https://ko.javascript.info/call-apply-decorators)가 뭐니, 부모를 어떻게 지정해줘서 constructor를 어떻게 지정해주니 마니 하는데…

자바 스크립트를 따로 공부해야하는 시간을 가지고, 지금은 Node.js를 익히는 시간부터 가져보자.

// 앞 뒤 순서가 전~~~혀 안 맞지만…

# 8. Callback Async Programming

---

> 무언가를 비동기적으로 수행하는 함수는 함수 내 동작이 모두 처리된 후 실행되어야 하는 함수가 들어갈 `콜백`을 인수로 반드시 제공해야 합니다.
→ **콜백함수는 간단하게 다른 함수에 매개변수로 넘겨준 함수를 말한다. 즉, 함수 안에서 실행하는 또 다른 함수를 의미한다. 매개변수로 넘겨받은 함수는 일단 넘겨받고, 때가 되면 나중에 호출(called back)한다는 것이 콜백함수의 개념이다.**
> 

스크립트 로딩이 끝나자마자 이 스크립트를 사용해 무언가를 해야만 한다고 가정해 봅시다. 스크립트 안에 다양한 함수가 정의되어 있고, 우리는 이 함수를 실행하길 원하는 상황입니다.

그런데 `loadScript(...)`를 호출하자마자 내부 함수를 호출하면 원하는 대로 작동하지 않습니다.

// JS는 비동기 기반이니까

```jsx
loadScript('/my/script.js'); // script.js엔 "function newFunction() {…}"이 있습니다.
newFunction(); // 함수가 존재하지 않는다는 에러가 발생합니다!
```

이제 위 loadScript의 두 번째 인수로 콜백 함수를 추가해보자. `callback()` 이전에 스크립트 로딩을 통해 스크립트 내부 함수를 불러올 수 있게 한다.

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

콜백이 유용한 이유는, 콜백 함수만을 바꿔줌으로서하나의 함수를 여러가지로 응용할 수 있기 때문이다. 아래 처럼 코딩하여 함수를 나눠줌으로써 코드를 재활용 할 수 있고, 관리도 더 쉬워지게 된다.

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

~~여기서 call()과 apply()를 활용하는 것도 있다만, 빨리빨리 넘어가자…~~

**콜백지옥**

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

# 9. Promise 문법

---

> 프로미스 : 내용이 실행 되었지만 결과를 아직 반환하지 않은 객체
> 

`const promise = Promise( X, Y, … );` 를 통해 각 경우에 따른 함수의 결과값을 변수가 들고있다가, `promise.then( … )` 절로 원할 때 결과값을 꺼낼 수가 있다!!

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

***코드를 분리할 수 있냐 없냐의 큰 차이가 있다!*** 가령 아래 타임아웃 코드를 이분화하여 코드 분리가 가능하다.

```jsx
setTimeout(()=>{
	codeA;
	codeB;
	codeC;
	...
},3000);
```

```jsx
const promise = setTimeoutPromise(3000);
code A;
code B;
code C;
...
promise.then(()=>{
 
});
```

Promise를 사용하여 콜백지옥을 막을 수는 있다.~~다만, then절 지옥을 생성할 수도 있게된다.~~

## Promise.all(), **Promise.allSettled()**

만약 프로미스가 여러 개 있을 때, 어떻게 한 번에 결과값을 받아올까? 

`Promise.all()`에 넣으면 모두 resolve될 때까지 기다렸다가 then으로 넘어간 뒤, 각 프로미스 결과값을 배열로 넣어버린다. 

하지만 `Promise.all()` 사용 시, **주어진 Promise 중 하나라도 Reject 되는 경우 모두 이행취소가 되어버힌다. 근래 들어서는 성공을하던 실패를 하던 무조건 일단 배열에 결과값을 담아주는 `Promise.allSettled()`라는 함수를 쓴다.** 예제는 아래 레퍼런스 참조했던 사이트에서 찾아보자.

# 10. async/await 문법

---

앞 서 Promise 문법을 사용하면 다음과 같은 이점을 갖고 있었다. 

1. 각 비동기 실행된 함수의 결과값을 들고 있을 수 있게하고 
2. 이에 따라 에러처리를 할 수 있게 catch문을 붙여줄 수 있었다. (아래 코드로 복습하자)

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

1. [https://ko.javascript.info/](https://ko.javascript.info/)
2. [https://bigtop.tistory.com/35](https://bigtop.tistory.com/35) 
→ 콜백 함수 이해
3. [https://inpa.tistory.com/entry/JS-📚-자바스크립트-콜백-함수](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%BD%9C%EB%B0%B1-%ED%95%A8%EC%88%98)
→ 콜백 함수 이해 2
4. [https://velog.io/@eggplantiny/Promise.allSettled-가-필요한-순간](https://velog.io/@eggplantiny/Promise.allSettled-%EA%B0%80-%ED%95%84%EC%9A%94%ED%95%9C-%EC%88%9C%EA%B0%84)
→ Promise.allSettled()
5. [https://ko.javascript.info/async-await](https://ko.javascript.info/async-await)