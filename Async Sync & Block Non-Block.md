# Async/Sync & Block/Non-Block

Created: 2022년 8월 12일 오후 1:52
Last Edited Time: 2022년 8월 23일 오후 3:22
State: Done
Tag: node.js

# Async vs Sync

---

> 작업을 하는 주체와 대상이 2개 이상인 상황에서 비동기와 동기를 구분한다.
> 
- 비동기 - Asynchronous
    - **작업들의 시작/종료 시점 및 순서를 결정할 수 없는 상황**
    - 작업 주체의 작업 시작/종료 순간이 서로 일치하지 않는 상황
- 동기 - Synchronous
    - **작업들의 시작/종료 시점 및 순서를 결정할 수 있는 상황**
    - 작업 주체의 작업 시작/종료 순간이 서로 일치하는 상황

# Block vs non-Block

---

> 작업의 주체와 대상이 2개 이상인 상황에서 Block과 non-Block을 구분한다.
> 
- Block
    - **하나의 작업자가 다른 작업자에게 보낸 요청이 돌아 오기 전 까지 대기하는 상황**
- non - Block
    - **하나의 작업자가 다른 작업자에게 보낸 요청이 돌아 오기 전에 다른 작업을 하는 상황**

# Event Loop

---

Js에서는 이벤트 루프를 통해 이러한 비동기 작업을 처리한다.

![Untitled](Async%20Sync%20&%20Block%20Non-Block%20e7770b9780fa4b769dd0766c2f013f17/Untitled.png)

1. Call stack에서 비동기 callback함수가 호출되면 (api에 요청을 보내는 함수 + 더하는 함수)
2. 해당 비동기 callback 함수를 background(Web API)로 이동한다.
3. 여기서 background는 call stack이 실행되는 스레드와는 독립적으로 동작한다.
(응답이 올 때 까지 기다리는 동작 수행)
4. 비동기 작업이 마무리되면 내부함수(소수 1000개와 응답을 더하는 동작)를 task queue 전달한다.
5. Task queue에 전달된 내부함수는 call stack이 빌 때 까지 기다린다.
6. 독립적으로 돌아가는 call stack의 모든 함수가 실행되고 나면 (anonymous까지 종료)
7. 그 때 task queue(callback queue)에 있던 내부함수를 call stack으로 올려서 해당 내부함수를 수행한다.

# Reference

---

1. [https://velog.io/@curiosity806/자바스크립트의-비동기-동작](https://velog.io/@curiosity806/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-%EB%B9%84%EB%8F%99%EA%B8%B0-%EB%8F%99%EC%9E%91)
2. [https://www.notion.so/vanillacake369/Node-js-ee4667af474a49959338d6b471bcee9b#4d5cf0f7897645389542eaaecb18f1ff](Node%20js%E1%84%85%E1%85%A1%E1%86%AB%20ee4667af474a49959338d6b471bcee9b.md)