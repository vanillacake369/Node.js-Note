# Node.js 이벤트 루프 상세과정

Created: 2022년 8월 5일 오전 10:41
Last Edited Time: 2022년 8월 23일 오후 3:23
State: Done
Tag: node.js

# 아직은 때가 아니다.

---

아래 공부해야하는 이유를 적고 공식 문서를 뜯어보며 하나하나 번역해서 내가 제대로 이해해보려 했다. **공부란 실제로 필요할 때 가서 상놈식으로 진득하게 하는 것이 답이다. *지금 해두면 나중에 가서 필요하겠지* 라는 마인드는 버리는 게 좋다.** 그래서 당분간 이벤트 루프 공부는 그만두고 지금 당장 필요한 Node.js 기본기와 JavaScript 기본기를 익히는 시간을 가지려한다.

# 이벤트 루프를 굳이 깊게 공부해야하나?

---

두 가지의 당위성을 가지고 깊게 공부하고자 한다. 

//물론 제일 중요한 건 기술을 빨리 습득해서 스스로 고민하는 시간을 가지며 코딩을 해나가는 것이겠지만…

1. 상세한 비동기처리를 공부하기 위해서는 이벤트에 의해 작동되는 JS와 노드의 이벤트 리스너를 자세히 파악해야겠다고 생각이 들었다. 이벤트 리스너는 이벤트 루프에 의해 돌아가므로 이벤트 루프에 대한 선행은 필수적이다.
가령 구름EDU와 같은 교육 서비스 플랫폼에서 Node.js를 사용한다고 가정해보자. (실제로 쓰고 있음). 여기서는 물론 강의의 퀄리티와 인기도가 제일 우선시 되겠다만, 실제 플랫폼의 서비스 자체가 끊기지 않아야 한다. 일례로 대부분의 크롬 유저들은 엄청난 탭을 열어두는데, 이 때 영상이 끊기게 된다면 UX의 퀄리티가 매우 안 좋아질 것이다.
이 때문에 비동기처리는 매우 중요하고 이에 관련된 이벤트 루프는 굉장히 중요하다 ~~라고 생각한다~~
2. 자바스크립트와 노드의 기초이다..

Node.js 공식 문서를 참조해보면  두 가지를 알 수 있다. 

1. 이벤트 루프가 무엇인지
2. 노드에서 이벤트 루프를 어떤 과정으로 동작/제어하는지

차근차근 공부해가보자.

# 1. 이벤트 루프란?

---

> The event loop is what allows Node.js to perform non-blocking I/O operations — despite the fact that JavaScript is single-threaded — by offloading operations to the system kernel whenever possible.
> 
> 
> Since most modern kernels are multi-threaded, they can handle multiple operations executing in the background. When one of these operations completes, the kernel tells Node.js so that the appropriate callback may be added to the **poll** queue to eventually be executed. We'll explain this in further detail later in this topic.
> 

이벤트 루프는 싱글 스레드로 동작하는 자바스크립트를 사용하여 non-blocking I/O 연산을 수행할 수 있게끔 한다.

근래 커널들은 멀티 스레드 기반이므로, 여러 연산 중 하나가 완료되면, 커널이 Node.js에게 poll queue에 완료된 연산에 적합한 callback함수가 추가하라고 명령한다.

- poll queue?
    
    [Polling system - Wikipedia](https://en.wikipedia.org/wiki/Polling_system)
    
    ![Untitled](Node%20js%20%E1%84%8B%E1%85%B5%E1%84%87%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%85%E1%85%AE%E1%84%91%E1%85%B3%20%E1%84%89%E1%85%A1%E1%86%BC%E1%84%89%E1%85%A6%E1%84%80%E1%85%AA%E1%84%8C%E1%85%A5%E1%86%BC%204ee43b929af54a088e8c9f25901ccaed/Untitled.png)
    
    > In queueing theory, a discipline within the mathematical theory of probability, a polling system or polling model is **a system where a single server visits a set of queues in some order.**
    > 
    
    하나의 단일 서버가 여러 큐 집합들로부터 어떤 기준의 순서대로 작업 큐들을 수행하는 것을 말한다.
    

![Untitled](Node%20js%20%E1%84%8B%E1%85%B5%E1%84%87%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%85%E1%85%AE%E1%84%91%E1%85%B3%20%E1%84%89%E1%85%A1%E1%86%BC%E1%84%89%E1%85%A6%E1%84%80%E1%85%AA%E1%84%8C%E1%85%A5%E1%86%BC%204ee43b929af54a088e8c9f25901ccaed/Untitled%201.png)

- REPL?
    
    [[node.js] REPL 이란?](https://velog.io/@bueong/node.js-REPL-%EC%9D%B4%EB%9E%80)
    
    > REPL은 **R**ead-**E**val(evaluation)-**P**rint **L**oop의 약어로 사용자가 특정 코드를 입력하면 그 코드를 평가하고 코드의 실행결과를 출력해주는 것을 반복해주는 환경을 말한다.
    node.js 에서도 REPL 환경을 지원하기 때문에 자바스크립트 학습중에 간단한 자바스크립트 코드등을 즉석에서 바로 실행해 결과를 확인해 볼 수 있다는 장점이 있다.
    > 
    
    간단히 말해 코드 평가/결과 확인 환경이라고 생각해주면 될 것 같다.