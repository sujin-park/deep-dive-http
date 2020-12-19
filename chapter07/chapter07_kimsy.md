## 7장 캐시
: 자주 쓰이는 문서의 사본을 자동으로 보관하는 HTTP장치
+ 혜택
  - 불필요한 데이터 전송/비용 절감
  - 네트워크 병목 감소 => 응답속도 증대
  - 서버에 대한 요청/부하 감소 
    * ex) Flash Crowds 대응
  - 거리 지연 감소
+ 적중

  |명칭|상세|
  |---|---|
  |캐시 적중 (cache hit) | 캐시에 대응 사본 있어 요청 처리되는 경우
  |캐시 부적중 (cache miss) | 대응 사본 없어 요청이 원서버로 전달되는 경우|

  > 구분: 응답코드는 200(OK)로 동일하므로 Via, Date, Age 헤더 이용 필요

+ 재검사 (Revalidation)  
  : 저장된 사본이 최신인지 서버에게 확인하는 요청
  - 요청  
    ex) GET If-Modified-Since 
  - 응답

    |종류|상세|
    |---|---|
    |성공 | 응답코드 304 (Not Modified) |
    |실패 | 응답코드 200 (OK) + 최신 객체 |
    |객체없음 | 응답코드 404 (Not Found) |

+ 속도  
  : 순수 캐시적중 > 재검사 적중 > 재검사 부적중 = 캐시 부적중
+ 적중률
  - 캐시(문서) 적중률 = 순수 캐시 적중 건수 / 모든 요청 건수
  - 바이트 적중률 = 순수 캐시 적중 (Byte)/ 모든 요청 (Byte)
  - 재검사 적중률 = 재검사 적중 건수 / 모든 요청 건수

### 캐시 토폴로지
+ 개인 전용 캐시 (private cache)
  - 작고 저렴
  - 웹브라우저에 내장
+ 공용 프록시 캐시 (public cache)
  - 여러 사용자 집단이 자주 이용하는 응답 저장
  - 브라우저 설정이나 프록시 자동설정 파일로 이용 가능
  - 인터셉트 프록시 이용 시 사용 강제 가능
  - 더 큰 부모 캐시 만들어 계층적 구성 가능   
    but) 연쇄 지연 발생 가능
+ 캐시망
  - 부모 캐시 이용 여부 동적으로 결정
    * 기준: URL, 로컬 사본 존재 여부 등
+ 형제캐시   
  - HTTP는 피어링 지원 안 하므로 확장 프로토콜 이용 필요
    * ICP (Internet Cache Protocol)
    * HTCP (Hypertext Cache Protocol)

> 토폴로지: 네트워크 객체들의 배치   
> [참고: 캐시정책](https://pjh3749.tistory.com/264)

### 캐시 처리 단계
+ **1)** 요청 수신
  - 커넥션 감지 및 데이터 수신
  - 고성능 캐시의 경우 병렬처리 가능
  - 메세지 일부 도착한 상태로 처리시작 가능
+ **2)** 메세지 파싱
  - URL 및 헤더 파싱해 자료구조에 저장
+ **3)** 로컬 복사본 검색
  - 로컬 캐시 검색하거나 부모프록시/원서버에 요청
+ **4)** 신선도 검사
  - 일정 시간 지난 사본은 변경점이 있는지 서버에 재검사 요청
+ **5)** 응답헤더 생성
  - 캐시된 원서버 응답 헤더를 기반으로 응답헤더 생성
  - 클라이언트에 맞게 헤더를 조정
    
    |용도|동작|대상 헤더|
    |---|---|---|
    |신선도 정보 |삽입| Cache-Control, Age, Expires|
    |프록시 캐시 정보| 삽입| Via |
    |생성일시(원서버)| 수정불가| Date|

+ **6)** 발송
  - 클라이언트에게 응답 발송
  - 프록시 캐시는 클라이언트와 연결 유지해야
  - 고성능 캐시는 로컬 저장소와 네트워크 I/O 버퍼 사이의 복사 회피하기도
+ **7)** 로깅 (선택)
  - 로그 파일 및 캐시 사용 통계 관리
  - ex) 캐시적중/부적중 횟수, 요청종류, URL 등
  - 양식
    * Squid Log Format
    * Netscape Extended Common Log Format

### 신선도 검사
+ 문서 만료  
  : 응답헤더를 이용해 유효기간을 명시 <- 로컬 시계 정확해야
  
  |버전|헤더|상세|예시|
  |---|---|---|---|
  |HTTP/1.1  |Cache-Control|문서의 최대 나이 설정 <br>단위: 초|Cache-Control: max-age=484200|
  |HTTP/1.0+ |Expires|절대유효기간 명시|Expires: Fri, 13 Dec 2020, 03:00:00 GMT|

+ 서버 재검사  
  : 캐시된 사본의 변경점을 서버에게 확인
  - 변경점 존재 -> 사본 갱신
  - 변경점 없음 -> 만료 헤더만 갱신
  - 조건부 메소드  
    : 조건부 GET 요청 이용해 효율적 재검사 가능

    |헤더|상세|
    |---|---|
    |If-Modified-Since  <br> (= IMS요청)|특정 시점 이후 변경점 존재하는 경우에만 전송 <br>Last-Modified 응답헤더와 함께 사용|
    |If-None-Match|일련번호/버전 등의 태그가 불일치하는 경우에만 전송 <br>시간에 따른 변경점이 작거나 부정확한 경우 유용|

    * [Etag 참고](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/ETag)
    * 약한 검사기 (Weak Validator)  
      : 약간의 변경점은 같은 것으로 취급, 접두어 "W/"로 구분  
      ex) Etag: W/"v2.7"
    * 강한 검사기 (Strong Validator)  
      : 모든 변경점을 변경값으로 취급해 값 갱신  

### 캐시 제어
+ 방식
  |명칭/헤더|상세|
  |---|---|
  |Cache-Control: no-store <br> Cache-Control: no-cache <br> Pragma: no-cache|no-store:사본 생성 금지 <br> no-cache: 재검사 필수 <br> Pragma: HTTP/1.0+ 호환용|
  |Cache-Control: max-age=0 <br> Cache-Control: s-maxage=6600|max-age: 최대 나이(초)<br> s-maxage: 공유 캐시에만 적용 <br> 0인 경우 캐시/리프레시 금지|
  |Expires: Fri, 13 Dec 2020, 03:00:00 GMT <br> (Deprecated)| 실제만료시각(현재 규격 아님)<br> 0 설정 불가|
  |Cache-Control: must-revalidate| 신선도 만료 시 재검사 강제 <br> 원서버 이용 불가시 504(Gateway Timeout) 반환|
  |휴리스틱 만료| Cache-control/Expires 헤더 없는 경우 경험적으로 최대 나이 계산 <br> ex) LM 인자 알고리즘 (수정시각 최근일수록 나이 짧게 설정)|

+ 클라이언트 신선도 제약  
: 브라우저의 리프레시/리로드 버튼으로 강제 갱신/재검사 요청 가능

  |헤더|상세|
  |---|---|
  |Cache-Control: max-stale = `n` |만료시각이 n만큼 지난 문서도 제공 가능 |
  |Cache-Control: min-fresh = `n`|지금으로부터 n초 전까지의 문서만 요청 |
  |Cache-Control: max-age = `n`| n초보다 오래되지 않은 문서만 요청|
  |Cache-Control: no-cache <br> Pragma: no-cache| 재검사한 문섬나 요청|
  |Cache-Control: no-store| 매시에 저장 금지 <br> ex) 민감정보 등|
  |Cache-Control: only-if-cached|캐시에 있는 사본만 요청|

  > 보통 유효기간 짧음 => 문서변경 반영 가능   
  > DNS와 달리 HTTP는 클라이언트의 만료일 덮어쓰기 및 강제로딩 허용  

### 캐시 제어 설정
+ Apache
  - mod_headers: 개별 헤더 설정
  - mod_expires: 만료시각 설정된 Expires 헤더 자동생성
+ HTTP-EQUIV
  - 웹서버 설정 파일과 상호작용 없이 HTTP 헤더 생성 가능
  - HTML 파일만 지원
  - 서버부하 가중 및 혼란 초래하므로 많은 S/W에서 무시됨
  ```
  <html>
    <head>
      <meta http-equiv="Cache^control" content="no-cache">
    </head>
  </html>
  ```

### 신선도 검사 알고리즘
: 나이 < 신선도수명 여부 계산
+ 나이 계산
  - 겉보기 나이: 응답수신시각 - 현재시각
  - 보정된 나이: 겉보기 나이와 Age 헤더값 중 큰 값
  - 점층적 나이 계산: 보정된 나이 - 네트워크 지연 추정값 
  - 최종 나이: 캐시에 도착했을 때 나이 - 캐시에 머문 시간
+ 신선도 수명 계산
  : 서버/클라이언트의 제약조건 반영해 계산

### 캐시와 광고
+ 광고회사의 딜레마
  - 캐시 이용 시 콘텐츠 제공 비용 감소하나 실제 접근 횟수 알 수 없음
  - 접근 별로 URL 변경해 캐시 무력화
  - 무력화 대신 캐시가 서버에 적중 보고하게 해야   
    => 트래픽 감소 but) 지연 발생  
+ 로그 마이그레이션  
  : 대형캐시는 로그를 수동 제공하기도   
    but) 인증/프라이버시 문제 발생 가능
+ 적중측정 및 사용량 제한 
  - = Simple Hit-Metering and Usage-Limiting for HTTP
  - 특정 URL의 캐시적중 횟수를 Meter 헤더에 실어 정기적으로 서버에 전송
  - 서버는 캐시의 문서제공횟수/처리시간 제어 가능   
