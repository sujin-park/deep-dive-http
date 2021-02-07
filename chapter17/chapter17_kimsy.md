## 17장 내용 협상과 트랜스코딩

### 17.1 내용 협상 기법 (Content-negotiation)
+ 내용 협상 기법 (Content-negotiation) 
  - 하나의 URL이 여러 리소스에 대응할 필요가 있는 경우 (예: 다국어 리소스 등)
  - 클라이언트-서버가 적합한 것 판단하도록 HTTP가 제공하는 협상방법
+ 배리언트 (variant)  
  : 특정 리소스의 서로 다른 버전
+ 종류
  
  |협상기법|상세|장점|단점|
  |---|---|---|---|
  |클라이언트 주도|클라이언트가 요청 시 서버가 선택지 제시|- 서버 구현 용이 <br> - 클라이언트가 최선의 선택 가능|대기 시간 증가 <br> (최소 2번 요청 필요)|
  |서버 주도|- 서버가 요청 헤더 검증해 제공 버전 선택 <br> - 클라이언트는 선호도(q값) 제공 <br> - 서버는 `Vary` 헤더로 검증 헤더 안내 | 클라이언트 주도보다 빠름|헤더에 맞는 것 없으면 서버가 추측해야|
  |투명|투명한 중간장치가 서버 대신 협상 <br> (주로 프록시 캐시)|- 서버의 협상부담 절감 <br> -클라이언트 주도보다 빠름|정형화된 명세 부재|

### 17.2 클라이언트 주도 협상
+ 2회의 서버 응답
  1) 선택 목록 제공
     * 여러 버전에 대한 링크 & 설명 담긴 HTML 페이지 제공하거나
     * `300(Multiple Choices)` 응답 제공 가능(HTTP/1.1)
  2) 선택한 사본 제공
+ 여러 URL 필요  
  - 주 URL과 조건별 하부 URL 필요
  - URL 공유/저장 시 불명확함 초래

### 17.3 서버 주도 협상
+ 내용 협상 헤더
  - HTTP는 stateless하므로 매 요청마다 헤더로 안내 해야 (선호도 저장X)
  - 선호도 q값: 0.0 ~ 1.0  
  - 종류
    |명칭|상세|대응 엔터티 헤더|
    |---|---|---|
    |Accept|수신 가능한 미디어타입 안내|Content-Type|
    |Accept-Language|수신 가능한 언어 안내|Content-Language|
    |Accept-Charset|수신 가능한 차셋 안내|Content-Type|
    |Accept-Encoding|수신 가능한 인코딩 안내|Content-Encoding|

    > 내용 협상 헤더는 수신 가능한 리소스 버전을 안내하고 엔터티 헤더는 콘텐츠의 속성을 안내  
  
  - ex)
    ```
    Accept-Language: en;q=0.7, fr;q=0.0, ko;q=1.0
    ```
+ 추가 헤더
  - User-Agent 헤더  
    : 클라이언트의 호화성에 따라 페이지 제공 가능 (예: 스크립트 제거 버전 등)   
  - Vary 헤더  
    : 캐시나 다운스트림 장치에게 버전 결정에 어떤 요청헤더 참고하는지를 안내 
+ 서버 측 확장  
  - MS의 ASP의 경우 내용협상 기능을 서버가 확장 
  - 참조: [MS 공식문서](https://docs.microsoft.com/ko-kr/aspnet/web-api/overview/formats-and-model-binding/content-negotiation)

#### 아파치 서버의 내용협상
+ 개괄
  - 색인 페이지를 여러 버전으로 제공하려는 경우 
  - 우선 각 버전의 파일을 적절한 디렉토리에 모두 저장하고
  - `type-map` 파일이나 `MultiViews` 지시어 사용해 내용협상 설정 가능
+ type-map 파일 사용
  - 서버 설정 파일에 `type-map` 파일 접미사를 명시한 핸들러를 추가
  - ex) `AddHandler type-map .var`
  - type-map 파일을 추가
  - ex)
    ```
    URI: main.html
  
    URI: main.en.html
    Content-Type: text/html
    Content-Language: en
    
    URI: main.ko.jp.html
    Content-Type: text/html;charset=utf-8
    Content-Language: ko, jp
    ``` 

  - ex)
    ```
    URI: foo
    
    URI: foo.jpeg
    Content-type: image/jpeg; qs=0.8
    
    URI: foo.gif
    Content-type: image/gif; qs=0.5
    
    URI: foo.txt
    Content-type: text/plain; qs=0.01  
    ``` 

+ MultiViews 지시어 사용
  - `access.conf` 파일의 적절한 절에 Options 지시어 사용해 해당 디렉터리의 `MultiViews`를 켜야
  - `MultiViews`가 설정된 경우 요청된 리소스 명에 따라 서버가 `type-map` 파일을 자동으로 생성
  - 서버는 이름에 근거해 적절한 협상 헤더를 추측 
  - ex) index 요청 시 => `index.en.html` 등 검색해 제공
+ 참조
  - [아파치 공식문서](http://httpd.apache.org/docs/current/ko/content-negotiation.html) 

### 17.4 투명 협상 (transparent negotiation)
: 클라이언트 입장에서 협상하는 중개자 프록시를 이용해 협상
+ 특성
  - 클라이언트와의 메세지 교환 최소화
  - 서버 주도 협상의 부하 절감
  - 캐시 내부에 설치된 범용 트랜스코더로 트랜스코딩 가능
  - 캐시는 올바른 배리언트(=alternate) 반환 위해 내용협상 헤더 사용
+ Vary 헤더
  - Vary 응답헤더는 서버가 문서선택/커스텀 콘텐츠 생성에 고려한 요청 헤더를 나열 
  - 캐시는 반드시 내용협상 헤더와 서버가 고려하는 요청 헤더를 모두 확인해야
  - ex) `Vary: User-Agent, Cookie`   
        => User-Agent와 Cookie 값 따라 배리언트 생성됨

### 17.5 트랜스코딩
: 클라이언트에게 맞춰 커스터마이징 된 페이지를 서버가 자동으로 생성 

+ 종류
  - 포맷변환
    * 데이터를 클라이언트가 볼 수 있는 포맷으로 변환 => 효율적/안전한 전송
    * ex) 고해상도->저해상도, 칼라->흑백
  - 정보합성 (info synthesis)
    * 문서에서 정보의 요점 추출
    * ex) 개요 생성, 광고/로고 제거, 웹페이지 자동분류 
  - 콘텐츠 주입 (Content-Injection)
    * 페이지에 동적으로 콘텐츠를 추가
    * ex)사용자 대상 광고, 사용자 추적/통계 시스템
+ 대안
  - 정적 사본 미리 생성
    * 페이지 수정 시 모든 사본 수정 필요 => 저장/관리 비용 증대
    * 타깃 광고 삽입 어려워짐
  - 루트 페이지를 필요에 따라 변환
    * 대기시간 증가 등 비용 초래
    * 계산/변환을 프록시나 캐시의 외부 에이전트에 넘길 경우 서버 부담 감소

### 17.6 다음 단계
+ HTTP 내용협상은 성능 제약을 초래
  - variant 탐색 및 추측 비용 간소화 위해 `RFC2295` `RFC2296` 등을 논의
+ 콘텐츠 협상 작업 그룹
  - HTTP는 내용협상이 필요한 유일한 프로토콜 아님 (예: 미디어 스트리밍과 팩스 등) 
  - 일반적인 내용협상 프로토콜을 TCP/IP에서 구현하기 위해 논의 (현재는 그룹 닫힘)
