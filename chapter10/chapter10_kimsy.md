## 10장 HTTP/2.0

### HTTP/2.0 등장 배경
+ HTTP/1.1의 단순성과 접근성 중심 설계로 회전지연(latency)문제 발생
+ 커넥션 하나 당 요청/응답을 하나만 처리 => 병렬/파이프라인 커넥션으로 해결 시도
+ 새로운 프로토콜 모색
  - HTTP 작업그룹의 HTTP-NG 프로젝트 (1997년 7월~)
  - Roy Fielding의 WAKA 프로토콜
  - MS의 S+M(Speed+Mobility) 프로토콜
  - Google의 SPDY 프로토콜 (2009년~)
+ HTTP/2.0 프로토콜 채택
  - 2012년 SPDY를 HTTP/2.0 프로토콜로 채택 
  - 2015년 5월 [공식표준 (RFC 7540)](https://www.rfc-editor.org/rfc/rfc7540.txt) 채택
  - 참조
    * [IETF 산하 HTTPbis 그룹의 HTTP 업데이팅](https://http2-explained.haxx.se/ko/part4)
    * [MDN - HTTP의 진화](https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/Evolution_of_HTTP#HTTP2_%E2%80%93_%EB%8D%94_%EB%82%98%EC%9D%80_%EC%84%B1%EB%8A%A5%EC%9D%84_%EC%9C%84%ED%95%9C_%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C)

### HTTP/2.0 개요
|항목|상세|
|---|---|
|TCP 이용 | - 서버-클라이언트 사이의 TCP 커넥션 위에서 동작 <br> - 클라이언트가 TCP 커넥션을 초기화|
|프레임 이용 | 요청/응답을 여러 바이너리 프레임에 나눠 저장|
|헤더 압축 | HTTP 헤더를 압축해 전송|
| 스트림 전송 | - 각 프레임은 스트림을 통해 전송 <br> - 하나의 스트림이 한 쌍의 요청/응답 처리 <br> - 커넥션 당 여러 스트림 생성 가능하며 혼잡제어/우선순위부여 기능 제공|
| 서버 푸시 | 클라이언트가 요청하지 않은 리소스도 필요 시 능동적으로 제공 가능
| 호환성 | - 요청/응답 메세지 의미는 HTTP/1.1과 동일 <br> - Content-Length: 명칭 `:content-length`로 변경 <br> - 404 Not Found: 상태줄 -> `:status` 헤더에 404 설정|

### HTTP/1.1과 차이점
+ 프레임 (Binary Framing)  
: 모든 메세지는 프레임에 담겨 전송된다
  ![](https://hpbn.co/assets/diagrams/ae09920e853bee0b21be83f8e770ba01.svg)

  - 크기: 헤더(8 Byte) + 페이로드(최대 16383 Byte)
  - 헤더필드
  
    |명칭|길이|상세|
    |---|---|---|
    |R|2 Bit| 의미 미정의 <br> 기본값: 0 <br> 수신자는 값 무시해야|
    |길이|14 Bit| 페이로드의 길이 (프레임 헤더 길이 제외) <br> 타입: unsigned integer|
    |종류|8 Bit| 프레임 종류|
    |플래그|8 Bit| 프레임 종류에 따라 다른 의미|
    |R|1 Bit|의미 미정의 <br> 기본값: 0 <br> 수신자는 값 무시해야|
    |스트림식별자|31 Bit|스트림 식별값 (만들어진 순으로 증가) <br> 0: 커넥션 전체와 연관된 프레임 의미 <br> 홀수: 클라이언트가 초기화 <br> 짝수:서버가 초기화|

  - 종류
    * DATA
    * HEADERS
    * PRIORITY
    * RST_STREAM
    * SETTINGS
    * PUSH_PROMISE
    * PING
    * GOAWAY
    * WINDOW_UPDATE: 스트림이 서로 간섭되지 않도록 [흐름제어](https://developers.google.com/web/fundamentals/performance/http2?hl=ko#%ED%9D%90%EB%A6%84_%EC%A0%9C%EC%96%B4) 시 이용
    * CONTINUATION 
+ 스트림과 멀티플렉싱  
: 스트림은 클라이언트-서버 사이에서 교환되는 프레임들의 독립된 양방향 시퀀스
  ![](https://hpbn.co/assets/diagrams/fa9ac7cba0327c032c5e1b57325496a4.svg)

  - 한쌍의 요청-응답이 전송되고 나면 해당 스트림은 닫힘
  - 한 커넥션에 여러 스트림 생성가능하며 우선순위 가질 수 있음 (우선순위대로의 처리 보장X)
  - 식별값은 나중에 만들어질수록 증가
    * 규칙 위반 시 PROTOCOL_ERROR로 응답해야
    * 식별값 고갈 시 커넥션 다시 맺어야
  - 스트림은 서버/클라이언트가 상대와 협상없이 일방적으로 생성 => 협상 시간 절감
+ 헤더압축  
  - 메세지 헤더를 HPACK 명세대로 압축하고 헤더블록조각들로 쪼개서 전송
  - 수신자는 쪼개진 조각들을 이어 압축을 해제하고 원래 헤더집합으로 복원
  - 압축 콘텍스트는 압축해제 시 변경됨   
    => 수/송신 측이 싱크되려면 항상 압축을 해제하거나 COMPRESSION_ERROR 보내야 
  - [참조: 구글 - HTTP2 헤더압축](https://developers.google.com/web/fundamentals/performance/http2?hl=ko#%ED%97%A4%EB%8D%94_%EC%95%95%EC%B6%95)  
+ 서버푸시  
  : HTTP/2.0은 하나의 요청에 대한 응답으로 여러 리소스 전송 가능  
  => 링크된 리소스 함께 제공해 트래픽/회전지연 감소 가능 
  - 푸시 전에 클라이언트에게 `PUSH_PROMISE` 프레임 보내서 알려야
  - 클라이언트는 `RST_STREAM` 프레임 보내 거절 가능 
  - 클라이언트는 `SETTINGS` 프레임 보내 서버 푸시 비활성화 가능 (값 = 0)
  - 주의점
    * 프록시가 서버 푸시의 추가 리소스 전달 안 할 수 있음
    * 프록시가 추가 리소스를 마음대로 전달할 수 있음 (서버가 전달하지 않아도)
    * 서버는 1) 안전하고 2) 캐시 가능하고 3) 본문 없는 요청에 대해서만 푸시 가능
    * 푸시 리소스는 클라이언트의 명시적인 요청과 연관돼야
    * `PUSH_PROMISE` 프레임은 원 요청으로 만들어진 스트림을 통해 전송됨
    * 클라이언트는 푸시 리소스가 Same-origin 인지 검사해야

      > [동일출처(same-origin)](https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy)  
      : URL의 프로토콜, 호스트, 포트가 모두 동일해야 동일출처로 인정 

### 알려진 보안 이슈
+ 중개자 캡슐화 공격 (Intermediary Encapsulation Attcks)  
  : HTTP/2.0은 헤더필드 값을 바이너리로 인코딩해 어떤 문자든 사용 가능  
  => 프록시 HTTP/1.1 메세지로 번역 시 문제 발생 가능
+ 커넥션 유지로 인한 개인정보 누출  
  : HTTP/2.0은 회전지연 줄이기 위해 커넥션을 오래 유지  
  => 이전 브라우저 사용자의 정보 유출 가능

### 성능이슈
+ HTTP/1 보다 적은 수의 TCP 커넥션 생성하므로 패킷손실률이 2% 이상인 환경에서는 성능이 더 낮음
+ [참조: HTTP2의 HOL(Head of Line) 문제](https://http3-explained.haxx.se/ko/why-quic/why-tcphol)