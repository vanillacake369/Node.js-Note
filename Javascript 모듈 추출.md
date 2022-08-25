# Javascript 모듈 추출

Created: 2022년 8월 9일 오전 3:12
Last Edited Time: 2022년 8월 23일 오후 3:23
State: Done
Tag: node.js

# 모듈이란?

---

모듈이란 **관련된 코드들을 하나의 코드 단위로 캡슐화** 하는 것

# 모듈 추출하기

---

크게 두 가지 방법으로 나뉜다.

1. **Import & Export로 모듈화하기**
2. **Exports & Require로 모듈화하기**

# Import & Export

---

![https://velog.velcdn.com/images%2Fgrinding_hannah%2Fpost%2F714ccc52-180c-4ca2-8fac-6ee952e54407%2Fimage.png](https://velog.velcdn.com/images%2Fgrinding_hannah%2Fpost%2F714ccc52-180c-4ca2-8fac-6ee952e54407%2Fimage.png)

> default 키워드로 특정 요소를 지정할 경우(위 예시의 경우 person), import 하는 파일에서 어떤 이름으로 가져오더라도 항상 default로 지정된 요소를 가지고오기 때문에 상관이 없다.
> 

![https://velog.velcdn.com/images%2Fgrinding_hannah%2Fpost%2F6d775c93-df78-4018-958c-84e73f39b451%2Fimage.png](https://velog.velcdn.com/images%2Fgrinding_hannah%2Fpost%2F6d775c93-df78-4018-958c-84e73f39b451%2Fimage.png)

가장 많이 쓰이는 import 형식은 **Named Export**.

> Named Export:import {something} from './파일경로
> 

# Exports & Require

---

# module.exports ↔ exports

---

| Module.exports | Exports |
| --- | --- |
| When we want to export a single class/variable/function from one module to another module, we use the module.exports way. | When we want to export multiple variables/functions from one module to another, we use exports way. |
| It is the object reference that gets returned from the require() calls. | exports is not returned by require().  It is just a reference to module.exports. |

> **module.exports :** When we want to export a single class/variable/function from one module to another module, we use *module.exports*

**exports :** When we want to export multiple variables/functions from one module to another, we use exports.
> 

다음은 module.exports를 사용한 예제 코드이다.

```jsx
class Arithmetic {
    constructor(a, b) {
        this.a = a;
        this.b = b;
    }
 
    add() {
        return this.a + this.b;
    }
    subtract() {
        return this.a - this.b;
    }
 
    multiply() {
        return this.a * this.b;
    }
 
    divide() {
        if (this.b != 0) {
            return this.a / this.b;
        }
        return "divided by zero !!!!";
    }
};
 
module.exports = Arithmetic;
```

```jsx
const Arithmetic = require('./calculator.js');
 
const op = new Arithmetic(100,40);
 
console.log(`Addition -> ${op.add()}`);
console.log(`subtraction -> ${op.subtract()}`);
console.log(`Multiplication -> ${op.multiply()}`);
console.log(`Division -> ${op.divide()}`);
```

이를 아래 exports를 사용한 예제 코드와 대조해서 비교해보자.

```jsx
exports.add = (a, b) => a + b;
exports.subtract = (a, b) => a - b;
exports.multiply = (a, b) => a * b;
exports.divide = (a, b) => {
    if (b != 0) {
        return a / b;
    }
    return `Divided by zero !!!`;
}
```

```jsx
const Arithmetic = require('./calculator.js');
 
console.log(`Addition -> ${Arithmetic.add(100,40)}`);
console.log(`subtraction -> ${Arithmetic.subtract(100,40)}`);
console.log(`Multiplication -> ${Arithmetic.multiply(100,40)}`);
console.log(`Division -> ${Arithmetic.divide(100,40)}`);
```

# 브라우저에서 require/export가 안 되는 이유

---

> require / exports 는 기존까지 Node.js에서 사용되던 CommonJs 모듈 방식으로, module.exports 객체에 모듈 전체를 치환하여 내보내고, require을 통해 해당 모듈을 불러오는 방식이다. 따라서 CommonJs 모듈 방식으로는 전체 모듈을 다 불러오게 되고, 불필요한 모듈이 로드되기도 하고 모듈 내에서 어떤 부분을 사용했는지도 확인할 수 없다.
> 
> 
> 때문에 **ES6(ECMA2015)**부터는 **import / export 라는 방식으로 모듈을 불러오고 내보낸다**. ES 모듈의 import는 require과는 다르게 import {name} from 'module'과 같이 모듈내에 정의된 특정한 부분만을 불러올 수 있다.
> 
> 다른 ES6 문법들, 이를테면 let이나 const, arrow function 등이 Node.js 상에서 정상적으로 작동하기 때문에, ES 모듈도 당연히 불러올 수 있을 거라고 생각했지만, Node.js에서 import를 사용하니 ES 모듈을 불러올 수 없다는 오류가 발생했다.여러 자료들을 찾아본 결과, **Node.js가 최신 자바스크립트 문법들은 지원하지만, 모듈 시스템으로 CommonJs를 채택했기 때문에 ES 모듈 시스템은 사용할 수 없다고 한다.**
> 
> 참고: [https://velog.io/@ohzzi/Node.js-%EC%97%90%EC%84%9C-importexport-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0](https://velog.io/@ohzzi/Node.js-%EC%97%90%EC%84%9C-importexport-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)
> 

즉, **Node.JS**는 Common.JS를 채택했기 때문에 `require/export`가 되고, **브라우저**에서는 ECMAScrip(ES6)를 사용하기 때문에 `import/export`만 되고 `require/export`가 되지 않는다는 말!

# Reference

---

1. [https://ko.javascript.info/modules-intro](https://ko.javascript.info/modules-intro)
→ 모듈 소개
2. [https://ko.javascript.info/import-export](https://ko.javascript.info/import-export) 
→ 모듈 내보내고 가져오기
3. [https://jongmin92.github.io/2016/08/25/Node/module-exports_exports/](https://jongmin92.github.io/2016/08/25/Node/module-exports_exports/)
→ module.exports와 exports 차이 이해하기
4. [https://www.geeksforgeeks.org/difference-between-module-exports-and-exports-in-node-js/](https://www.geeksforgeeks.org/difference-between-module-exports-and-exports-in-node-js/)
→ Difference between module.exports and exports in Node.js
5. [https://velog.io/@grinding_hannah/JavaScript-Import-Export로-모듈화하기](https://velog.io/@grinding_hannah/JavaScript-Import-Export%EB%A1%9C-%EB%AA%A8%EB%93%88%ED%99%94%ED%95%98%EA%B8%B0)
→ [JavaScript] 다른 JS파일에서 요소 가져오기 / 모듈화하기