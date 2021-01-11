## 12장 기본 인증

### 기본 인증
: HTTP는 사용자 식별을 위해 자체적인 인증 기능을 제공
+ 과정
  - 요청  
    : 클라이언트가 서버에 요청을 전송 
  - 인증요구  
    : 서버가 WWW-Authenticate 헤더와 상태코드 401(Anauthoized) 전송
  - 인증
    : 클라이언트가 Authorization 헤더에 인증정보(사용자명+비밀번호) 기술해 요청 재전송
  - 성공
    : 인증 성공한 경우 서버가 정상 응답 (Authentication-Info 헤더에 추가 정보 기술하기도)
+ 영역
  : 보통 영역(realm)에 따라 다른 사용자 권한 부여  
   => WWW-Authenticate 헤더의 realm　파라미터에 영역명이나 호스트명 기술
   ```
   HTTP/1.0 401 Unahthorized
   WWW-Authenticate: Basic realm="Company Spiral"    
   ```
+ 인코딩
  : 클라이언트가 Authorization 응답 시 인증정보를(사용자명:비밀번호) Base-64로 인코딩해 전송
+ 프록시
: 기본인증과 헤더명 및 상태코드만 다르며 같은 과정 거침
  
  |웹서버|프록시서버|
  |---|---|
  |WWW-Authenticate / 401| Proxy-Authenticate|
  |Authorization| Proxy-Authorization|
  |Authentication-Info| Proxy-Authentication-Info|

### 기본인증의 보안결함
+ 인증정보 쉽게 디코딩 가능
+ 인증정보 가로채서 서버 접근 가능 (=재전송 공격)
+ 사용범위 제한해도 동일 인증정보 사용하는 타 사이트 해킹 가능성 존재
+ 요청정보 수정되거나 프록시/중개자 개입된 경우 정상동작 보장 불가 
+ 가짜서버/게이트의 위장에 취약
=> SSL 등 암호화된 전송기술 연계하거나 다이제스트 인증 사용해야