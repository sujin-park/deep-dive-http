## 11장 클라이언트 식별과 쿠키

### 개별 접촉
: HTTP는 익명으로 사용되며 모든 요청이 독립적/일회성인 무상태(stateless) 요청   
=> 개인화(맞춤추천, 세션추적, 사용자정보 저장)에 추가 처리 필요  

### HTTP 헤더
: 사용자 정보 전달하는 대표적 헤더
|명칭|헤더타입|상세|한계|
|---|---|---|---|
|From|요청|사용자 이메일 주소|확실한 식별 어려움|
|User-Agent|요청|사용자 브라우저/OS 정보|확실한 식별 어려움|
|Referer|요청|링크 타기 전 근원 페이지|확실한 식별 어려움|
|Authorization|요청|사용자명+패스워드|보안취약|
|Client-ip|확장(요청)|클라이언트 IP주소<br>(함수로 취득 가능)|확실한 식별 어려움 <br>예) 여러 사용자가 한 기기 사용하는 경우<br> 유동IP, NAT방화벽, 프록시 사용하는 경우|
|X-Forwarded-For|확장(요청)|클라이언트 IP주소|
|Cookie|확장(요청)|서버가 생성한 ID라벨|

### 뚱뚱한 URL
+ 정의
  - URL의 처음이나 끝에 사용자 식별정보값 추가해 제공하는 방식  
  - ex) www.test.kr/wishlist/ref=asia/`01-124035-21098838`  
+ 문제점
  - URL의 비가독성 증가해 사용자에게 혼란 초래
  - 개인정보 유출 가능
  - URL이 변경되므로 캐시 이용 불가 => 서버 부하 증가
  - 타 사이트 접근하거나 특정 URL 호출 시 이탈 발생
  - 세션 간 정보 지속 불가 (URL 따로 저장하지 않는 한 )

### 쿠키
+ 특성
  - 사용자 식별 및 세션 유지에 사용되는 가장 일반적인 방식
  - 모든 브라우저가 지원
  - 캐시와 충돌 가능성이 있어 쿠키의 내용물은 캐싱 안 됨
  - 리스트 (이름=값) 형태로 정보 저장  
  - ex) Cookie: name="Jane Doe"; phone="123-1233"
+ 종류

  |명칭|상세|용도|
  |---|---|---|
  |세션쿠키<br>(session cookie)|한 브라우저 세션 동안 존재<br>파기시점 설정: Discard, Expires, Max-Age 파라미터|사이트 탐색 시의 설정/선호 저장|
  |지속쿠키<br>(persistent cookie)|브라우저 닫거나 기기 재시작 후에도 잔존|자주 방문하는 사이트, 로그인/설정 정보 등 유지|

+ 쿠키 관리 (클라이언트 측 상태, HTTP State Mng Mechanism)
  - 사이트A 방문 시 서버가 사용자에게 식별값을 할당
  - 할당값을 Set-Cookie 등의 응답헤더에 기술해 사용자에게 전달
  - 브라우저가 전달 받은 쿠키값을 쿠키DB에 저장
  - 사이트A 재방문 시 쿠키DB의 쿠키값을 Cookie 헤더에 기술해 서버에 전송
  - 보통 사이트마다 2~3개의 쿠키를 전송 => 성능저하 방지 
+ 세션추적
  - 사이트에서 여러 트랜잭션 만드는 사용자 추적에 사용
  - 과정 예시
    * 브라우저가 사이트의 루트 페이지를 요청
    * 서버가 전자상거래 URL로 리다이렉트
    * 클라이언트가 리다이렉트 URL로 요청
    * 서버가 2개의 세션쿠키 기술한 응답을 전송 + 사용자 전용 URL 생성해 리다이렉트 (뚱뚱한 URL)
    * 클라이언트가 세션쿠키 2개 첨부해 사용자 전용 URL 요청   
    * 서버가 쿠키 2개 더 첨부하고 home.html로 리다이렉트
    * 클라이언트가 쿠키 4개 첨부해 home.html 요청
    * 서버가 콘텐츠 전송
+ 쿠키 캐싱
  - 캐싱 시 이전 사용자 쿠키 할당 및 개인정보 누출 가능
  - 원칙

    |원칙|적용예시|
    |---|---|
    |캐시 불가 문서 표시 | Cache-Control: no-cache="Set-Cookie"|
    |캐시 가능 문서 표시 | Cache-Control: public|
    |Set-Cookie 헤더 제거하거나 재검사 강제  | Cache-Control: must-revalidate, max-age=0|
    |Set-Cookie 헤더 있는 이미지 재검사 강제 | Cache-Control: must-revalidate, max-age=0|

+ 보안
  - 비할성화 가능
  - 개인정보 및 브라우징 습관 추적 가능
  - 1998년 미 재무성 컴퓨터 사고 자문단은 쿠키의 위험성이 과대됐다고 평가

### EU 개인정보보호법(GDPR, General Data Protection Regulation) 관련
: 2018년 EU의 개인정보보호법(GDPR)에서 쿠키를 개인정보로 인정  
  => 수집 시 명확한 목적 제시 및 명시적 동의 필요  
+ 유효한 동의확보 방법 (프랑스 CNIL 가이드라인)
  - 사용자가 자유롭고 구체적이며 충분한 정보에 입각해 명확한 긍정 의사를 명시적으로 표시한 경우만 유효
    * 묵시적 동의 인정 불가 (예: 브라우저의 이용 설정, 사이트 이용 지속)
    * 일반 이용약관에 대한 포괄적 동의 인정 불가
  - 사전에 의사 표기된 동의박스 이용 불가
  - 쿠키수집 비동의 시 접근 막는 쿠키월(cookie walls) 적용 불가
  - 용이하고 사용자 친화적인 동의 철회 솔루션 필요
  - 쿠키 및 추적도구 적용하는 이해관계자의 사용자 동의확보 증명 메커니즘 항시 구현 의무
  - 동의 요청 전 안내 필요 사항
    * 데이터 컨트롤러의 신원
    * 데이터 처리 활동 목적
    * 동의 철회 권리
+ 동의 의무 면제
  - 전송오류/데이터손실 여부 추적에 필수거나 원활한 온라인 서비스 위해 적용된 쿠키
    * 사용자가 해당 쿠키의 적용사실 및 목적을 인지 필요   
      => 개인정보처리방침이나 쿠키정책에 적시 필요
  - 사용자가 명시적으로 요청한 서비스 제공 위해, 사용자의 온라인 활동 침해하지 않는 선에서 적용된 사용자 분석용 온라인 추적도구
    * 어떤 유형의 온라인 추적 도구가 적용되는지 사용자에게 고지 
    * 추적 목적을 익명통계 생성으로 엄격히 제한 
    * 데이터 이용 수명 제한
    * 수집된 개인정보의 제3자 양도 및 다른 처리와 병합 불가
+ 참조  
  - [한국인터넷진흥원 해외 개인정보보호 동향 보고서 2019년 9월 2주](https://www.privacy.go.kr/cmm/fms/FileDown.do?atchFileId=FILE_000000000836920&fileSn=0&nttId=9792&toolVer=&toolCntKey_1=)
  - [구글 EU 사용자 동의 정책 관련 도움말](https://www.google.com/about/company/user-consent-policy-help/)
  - [cookiechoices 동의 관련 안내](http://www.cookiechoices.org/intl/ko/)

### 브라우저별 쿠키 관리
+ 구글 크롬
: Cookies라는 SQLite 파일에 쿠키를 저장  

  |필드명|상세|
  |---|---|
  |creation_utc | 쿠키 생성시점 <br> 형식: GMT 기준 초단위|
  |host_key | 쿠키의 도메인 |
  |name | 쿠키 이름 |
  |value | 쿠키 값 |
  |path | 쿠키 관련 도메인 경로|
  |expire_utc | 쿠키 파기시점 <br> 형식: GMT 기준 초단위|
  |is_secure | SSL 커넥션일 경우에만 전송|

+ IE
  - 쿠키를 캐시 디렉토리에 각각 개별파일로 저장
  - 자체 형식으로 저장
  - 도메인당 크기 제한 [(참조:MS 공식문서)](https://docs.microsoft.com/ko-kr/internet-explorer/kb-support/ie-edge-faqs)
  - 형식: 여러 행으로 기술 
    * 1행: 쿠키명 (Name)
    * 2행: 쿠키값 (Value)
    * 3행: 도메인, 경로 (Domain/path)
    * 나머지행: IE만을 위한 추가 데이터 


### 쿠키 명세
+ Version 0 (Persistent Client State)
  - Netscape에서 정의한 최초의 쿠키 표준
  - 서버 전송 시 해당되는 쿠키 값을 Cookie 헤더에 모두 이어 붙여 기술
  - Set-Cookie 헤더 속성
  
    |명칭|필수|상세|예시|
    |---|---|---|---|
    |name=value|필수|서버 방문 시 전달할 이름=값 조합<br>구분문자:세미콜론(;) <br>제외문자: 세미콜론(;), 쉼표(,), 등호(=), 공백( )|Set-Cokie: country=KR|
    |Expires|선택|쿠키 파기일자 <br>기본값:세션 만료시까지<br>구분자:하이픈(-)|Set-Cookie: name=lee; expires=Wednesday, 17-Dec-69 23:03:00 GMT|
    |Domain|선택| 쿠키를 전달 받는 사이트 제한 <br>비교:후방일치 시 허용<br>기본값:응답 생성 서버의 호스트명|Set-Cookie: order=recent; domain="myspace.io"|
    |Path|선택| 쿠키를 전달 받는 사이트 경로 제한<br>비교:전방일치 시 허용<br>기본값: 목표 URL 경로|Set-Cookie: level=25; path=/info|
    |Secure|선택|HTTP SSL 보안 연결 시에만 전송|Set-Cookie: seq_id=412398750; secure|

+ Version 1 (RFC 2965)
  - 속성 더 많은 Set-Cookie2 헤더 이용해 Version 0 확장
  - 2011년 RFC 6265 (HTTP State Mng Mechanism) 으로 대체
  - 현재는 사용 안 함 (HISTORIC 상태)
