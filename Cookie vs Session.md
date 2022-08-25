# Cookie vs Session

Created: 2022년 8월 9일 오전 4:29
Last Edited Time: 2022년 8월 23일 오후 3:23
State: Done
Tag: network

# HTTP의 두 가지 특성

---

1. 비연결지향 connectionless
: 요청/응답 주고 받는 순간에만 연결 유지
2. 상태정보 비유지 stateless
: 통신이 끝나면 서버는 클라이언트 상태 유지하지 않음.

위 두 가지 특성을 보완하기 위해 쿠키/세션을 사용

# 쿠키

---

- 클라이언트(브라우저)에 파일로 저장
- 총 300개까지 쿠키 저장 가능. 하나의 쿠키 값은 최대 4KB, 도메인 당 20개의 쿠키 값까지 가능
- 한 번 쿠키가 저장되면, 저장된 순간 이후로의 모든 페이지 요청은 쿠키 name과 그에 매핑된 value를 요청한다.
- 쿠키가 한 번 클라이언트 사이드에 저장되면, 쿠키의 지속시간을 설정해둔 시간만큼 지속된다. 
//반면 세션은 브라우저 종료에 따라 삭제됨

# 쿠키 생성 과정

---

![Untitled](Cookie%20vs%20Session%20f322cbdf68234ab8afbba1cbeb5e87b8/Untitled.png)

1. 클라이언트가 사용자 정보를 입력하여 서버에 특정 Request 
// Ex)로그인
2. 서버의 쿠키 생성
: 클라이언트ID,접속시간,IP 등을 이용하여 쿠키 문자열 생성. HTTP 응답 메세지에 쿠키 포함하여 클라이언트에 전달함
3. 클라이언트는 수신한 쿠키를 저장. 재방문 시, 클라이언트가 요청 메세지에 쿠키 헤더를 포함하여 전달

# 안전한 쿠키 사용

---

1. 쿠키는 텍스트 파일이므로 보안에 취약함
2. 쿠키 정보를 이용하여 웹 사이트가 사용자 정보를 수집하기도 함.
3. 따라서 인터넷 사용기록과 쿠키를 수시로 삭제해주어야 보안이 안전함

# 세션

---

- 세션은 쿠키의 취약점을 보완하기 위해 제시된 방법
- 사용자 세션 정보를 서버에 전역변수로서 저장됨. 각 세션은 고유의 id값과 그에 따른 저장된 value값을 가짐→ 보안에 좋음
- 브라우저가 종료되면 자동적으로 삭제됨. 세션값을 영원히 유지하기 위해서는 데이터베이스에 저장해야함.
- 서버에 저장되기에 저장 공간이 필요하며, 접속수에 따른 서버 과부하에 의해 성능 저하가 될 수 있음.
- 하지만 세션 또한 결국 쿠키를 사용한다.

# Reference

---

1. 2-06.WWW and HTTP.p29-p33.인터넷과 멀티미디어 통신.차영욱 교수님안동대학교 2학년 2학기 과정.
2. [https://www.guru99.com/difference-between-cookie-session.html](https://www.guru99.com/difference-between-cookie-session.html)
3. [https://velog.io/@chelsea/네트워크-HTTP-Cookie-vs-Session](https://velog.io/@chelsea/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-HTTP-Cookie-vs-Session)
4. [https://www.paolinoproductions.com/generate-a-session-token-upon-login](https://www.paolinoproductions.com/generate-a-session-token-upon-login)
5. [https://dev-coco.tistory.com/61](https://dev-coco.tistory.com/61)