## 3장 HTTP 메시지

### HTTP 메시지
: HTTP 어플리케이션 간의 주고받은 데이터 블록들
+ 방향
  
  |명칭|상세|
  |---|---|
  |인바운드 | 클라이언트 -> 서버 |
  |아웃바운드| 서버 -> 사용자 에이전트 |
  |업스트림  | 메시지의 발송자 |
  |다운스트림 | 메시지의 수신자 |
  
  > 모든 데이터는 다운스트림 방향으로 흐른다

+ 구조

  |명칭|기능|상세|
  |---|---|---|
  |시작줄 | 어떤 메시지인지 서술| 라인(CRLF)으로 구분된 아스키 문자열 <br> 구분문자:캐리지리턴 + 개행문자 <br> 필드구분: 공백문자|
  |헤더블록 | 속성 서술|형식: 이름 + 콜론 + 공백 + 필드값 + CRLF <br>여러 줄 입력: 문두에 스페이스나 탭 입력 <br>content-type: 본문 종류 <br> content-length: 본문크기(Byte) |
  |본문 | 데이터 내포| 예: 텍스트, 이진 데이터, 빈 값 |

+ 문법
  - 요청메시지  
    > <메소드><요청URL><HTTP버전>  
      <헤더>    
      <엔터티 본문>
       
  - 응답메시지 
    > <HTTP버전><상태코드><사유구절>  
      <헤더>    
      <엔터티 본문>
  - HTTP/0.9  
    : 메소드, 요청URL, 엔터티 본문만 존재

### 메소드
: 클라이언트가 요청한 서버의 리소스 처리 동작 

+ 안전한 메소드  
  : 통상 서버에서의 작용이 없는 메소드 ex) GET, HEAD
+ 확장 메소드  
  : 추가적으로 구현한 메소드 (HTTP명세 확장)

  |명칭|상세|
  |---|---|
  |LOCK |리소스 락 => 동시편집 방지 |
  |MKCOL |문서 생성 |
  |COPY |서버 리소스 복사 |
  |MOVE |서버 리소스 이동 |

  * 구현 안 된 경우 501(Not implemented) 코드로 응답

+ 대표적 메소드 

  |명칭|본문<br>존재|상세|
  |---|---|---|
  |GET |x| 서버에서 리소스 취득| 
  |HEAD |x |서버에서 리소스의 헤더만 취득 <br> => 타입, 존재/변경 여부 확인 가능 | 
  |POST |O |서버가 처리할 입력 데이터 전송 | 
  |PUT |O |서버에 메세지 본문 갱신/생성 + 저장 | 
  |TRACE |x |프록시 등 서버에 도달하는 과정 추적 <br> => 서버가 Loopback 진단 + 요청 메세지 리턴 <br> [** 보안이슈: 쿠키 및 사용자정보 탈취 위험 p.19](https://repository.kisti.re.kr/bitstream/10580/6350/1/2016-056%20웹%20어플리케이션%20취약점%20조치방법.pdf)| 
  |OPTIONS |x |특정 리소스에 대한 서버의 지원 메소드 목록 제공 | 
  |DELETE |x |서버의 리소스 제거 (처리 보장 X)| 
  * 주의: 서버마다 구현 범위 다름


### HTTP 버전번호
: 애플리케이션의 처리능력과 메세지 형식 등 호환성 정보 제공 
+ 형식: HTTP/x.y
+ 주의: x와 y를 분리된 숫자로 취급 (예: 2.22 > 2.3 ; 22 > 3)

### 상태 코드
: 요청 중 발생한 사건을 설명하는 세 자리 숫자

+ 100번대 - 정보

  |코드|사유구절|의미|
  |---|---|---|
  |100 |Continue| 요청 시작 부 수신됐으니 나머지 전송하시오 <br> * 클라이언트가 바로 entitiy 전송하는 경우 응답 생략해야 <br> * 홉 서버가 HTTP/1.1 이전 버전인 경우 프록시는 417(Expectation Failed) 오류 응답해야|
  |101 |Switching Protocols|서버 프로토콜 변경됨 (클라이언트의 upgrade 헤더 나열 중 하나로)|

+ 200번대 - 성공

  |코드|사유구절|의미|포함정보|
  |---|---|---|---|
  |200 |OK| 요청 정상 | 엔티티: 요청된 리소스|
  |201 |Created|리소스 생성 요청 성공 | Location 헤더: 리소스의 구체적 참조 <br> 엔티티: 리소스의 URL|
  |202 |Accepted|요청 수신됨| 엔티티: 요청에 대한 상태 및 추정완료시점|
  |203 |Non-Authoritative Info|엔티티 헤더의 정보가 리소스 사본에서 기원 (리소스 메타정보 검증 실패)|엔티티 헤더|
  |204 |No Content|엔티티 본문 존재X <br> => 이동 없이 문서 갱신||
  |205 |Reset Content|브라우저에게 현재 페이지의 폼 데이터 리셋 요청| |
  |206 |Partial Content|부분/범위 요청 처리 성공|필수:Content-Range, Date헤더, Etag/Content-Location 헤더|

+ 300번대 - 리다이렉션 에러

  |코드|사유구절|의미|
  |---|---|---|
  |300 |Multiple Choices| 여러 리소스 참조하는 URL 요청 시 리소스 목록 리턴 <br> * Location 헤더에 선호 URL 포함 가능|
  |301 |Moved Permanently| 요청한 URL이 이동됨 <br> * Location 헤더에 이동된 현재 URL 포함|
  |302 |Found| 요청한 URL이 임시적으로 이동됨  <br> * Location 헤더에 이동된 URL 포함 <br> [(GET이나 HEAD 요청에 대해서만 응답해야)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/302)|
  |303 |See Other| 리소스를 다른 URL에서 가져와야 <br> * Location 헤더에 새 URL 포함|
  |304 |Not Modified|조건부 요청에 대한 응답, 최근에 수정 없음 <br> * 엔티티 본문 없어야|
  |305 |Use Proxy| 프록시로 접근 필요 <br> * Location 헤더에 프록시 위치 포함|
  |306 | - | (현재는 사용 X)|
  |307 |Temporary Redirect| 302와 동일 <br> [(요청 메소드 변경 안 됨 => 더 안전)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/307)|
  |308 |Permanent Redirect | 어플리케이션에 따라 다름 <br> [예) 일반 서버, 구글 드라이브](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/308)|

  * [참고: 302, 307, 308 오류 차이](https://perfectacle.github.io/2017/10/16/http-status-code-307-vs-308/)


+ 400번대 - 클라이언트 에러

  |코드|사유구절|의미|
  |---|---|---|
  |400 |Bad Request| 잘못된 요청 수신|
  |401 |Unauthorized| 인증 필요|
  |402 |Payment Required| [결제 필요 (아직 미도입)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/402)|
  |403 |Forbidden| 요청 거부|
  |404 |Not Found| 존재하지 않는 URL|
  |405 |Method Not Allowed| 지원하지 않는 메소드 (요청에 Allow 헤더 포함된 경우)|
  |406 |Not Acceptrable| 클라이언트가 수신 불가능한 리소스|
  |407 |Proxy Authentification| 프록시 서버의 인증 필요 |
  |408 |Request Timeout| 서버에 설정된 일정 시간 내 요청 완수 실패|
  |409 |Conflict| 리소스 충돌 경고 <br> * 본문에 충돌 설명 포함해야 |
  |410 |Gone| 리소스 제거됨 (원래는 있었지만)|
  |411 |Length Required| 요청에 Content-Length 헤더 필요|
  |412 |Precondition Failed| 조건부 요청 중 일부가 실패|
  |413 |Request Entity Too Large| 요청이 서버의 처리 범위 초과|
  |414 |Request URI Too Long| 요청 URL 길이가 서버의 처리 범위 초과|
  |415 |Unsupported Media Type| 인식/지원 불가능한 엔티티|
  |416 |Requested Range Not Satisfiable| 잘못된 요청 리소스 범위|
  |417 |Expectation Failed| Expect 요청 헤더 내용 지원 불가|

+ 500번대 - 서버 에러

  |코드|사유구절|의미|
  |---|---|---|
  |500 |Internal Server Error|서버에서 처리할 수 없는 에러 발생 |
  |501 |Not Implemented| 지원하지 않는 요청(메소드 등)|
  |502 |Bad Gateway|프록시/게이트웨이가 다음 링크에서 가짜 응답 수신 <br> ex) 부모 게이트웨이 접속 불가한 경우|
  |503 |Service Unavailable|현재는 처리 불가 (나중에는 가능) <br> => 응답에 Retry-After 헤더 포함 가능|
  |504 |Gateway Timeout| 게이트웨이나 프록시에서 타임아웃 발생|
  |505 |HTTP Version Not Supported|지원하지 않는 HTTP 프로토콜 요청|

### 사유구절 (reason-phrase)
: 상태코드의 의미를 '사람이 이해할 수 있게' 설명하는 짧은 문구  
* 규칙 없음  
* 사유구절 달라도 상태코드 같으면 동등한 의미  
   ex) 200 OK = 200 NOT OK

### 헤더
: 클라이언트와 서버가 무엇을 하는지 결정하기 위해 함께 사용  
![](https://mdn.mozillademos.org/files/13821/HTTP_Request_Headers2.png)

+ 일반 헤더  
: 용청/응답 양쪽에 모두 존재 가능

  - 일반 정보 헤더

    |헤더|상세|
    |---|---|
    |Connection | 클라이언트-서버 연결 옵션 설정|
    |Date | 메세지 생성 시각|
    |MIME-Version | 발송자가 사용한 MIME 버전|
    |Trailer chunked transfer | 인코딩된 메세지 끝 부분의 헤더 목록 나열|
    |Transfer-Encoding | 메세지에 적용된 인코딩|
    |Upgrade | 발송자가 업그레이드 원하는 새 버전/프로토콜|
    |Via | 메세지 중개자(프록시, 게이트웨이) 목록|

  - 일반 캐시 헤더  
  : 언제 어떻게 캐시가 되어야 하는지 지시자 제공

    |헤더|상세|
    |---|---|
    |Cache-Control | 메세지 및 캐시 지시자 전달|
    |Pragma | 메세지 및 지시자 전달 <br> (HTTP/1.1에서 Cache-control로 대체)|

+ 요청 헤더  
: 요청에 대한 부가정보(클라이언트의 능력, 선호 등) 제공

  - 요청정보 헤더

    |헤더|상세|
    |---|---|
    |Client-IP | 클라이언트 PC의 IP주소|
    |From | 클라이언트 사용자의 메일주소|
    |Host |요청한 서버의 호스트명과 포트번호 |
    |Referer | 현재의 요청 URL을 포함하고 있는 문서의 URL |
    |UA-Color |클라이언트 기기의 디스플레이 색상 능력 정보|
    |UA-CPU |클라이언트의 CPU 종류 및 제조사|
    |UA-Disp |클라이언트의 디스플레이 능력|
    |UA-OS |클라이언트의 OS명 및 버전|
    |UA-Pixels |클라이언트의 디스플레이 픽셀 정보|
    |User-Agent |요청 보낸 어플리케이션 이름|

  - Accept 관련 헤더

    |헤더|상세|
    |---|---|
    |Accept |클라이언트가 수신 가능한 미디어 종류|
    |Accept-Charset |클라이언트가 수신 가능한 문자집합|
    |Accept-Encoding |클라이언트가 수신 가능한 인코딩|
    |Accept-Language |클라이언트가 수신 가능한 언어|
    |TE |클라이언트가 수신 가능한 확장 전송 코딩(Transfer-Encoding) ??? |

  - 조건부 요청 헤더

    |헤더|상세|
    |---|---|
    |Expect | 요청에 필요한 서버 행동 열거|
    |If-Match | 주어진 엔티티 태그와 일치하는 경우 리소스 취득|
    |If-None-Match |주어진 엔티티 태그와 불일치하는 경우 리소스 취득|
    |If-Modified-Since |주어진 날짜 이후 리소스가 변경된 경우 리소스 취득|
    |If-Unmodified-Since |주어진 날짜 이후 리소스가 그대로인 경우 리소스 취득|
    |If-Range | 문서의 특정 범위에 대해 요청|
    |Range | 리소스의 특정 범위에 대해 요청|

  - 요청 보안 헤더

    |헤더|상세|
    |---|---|
    |Authorization | 클라이언트가 서버에 제공하는 인증 정보|
    |Cookie | 클라이언트가 서버에 토큰 전달 시 이용|
    |Cookie2 | 요청자가 지원는 쿠키 버전 제공|


  - 프록시 요청 헤더

    |헤더|상세|
    |---|---|
    |Max-Forwards|서버 중간에 거칠 수 있는 프록시/게이트의 최대 전달횟수|
    |Proxy-Authorization | 프록시가 서버에 제공하는 인증 정보|
    |Proxy-Connection | 프록시-서버 연걸 시 옵션 설정|

+ 응답 헤더  
: 응답에 대한 부가정보(응답자, 응답자의 능력 등) 제공

  - 응답 정보 헤더

    |헤더|상세|
    |---|---|
    |Age | 응답이 얼마나 오래됐는지|
    |Public | 서버가 특정 리소스에 대해 지원하는 메소드 목록|
    |Retry-After | 리소스가 다시 사용 가능해지는 일시|
    |Server | 서버 어플리케이션의 이름 & 버전|
    |Title | HTML문서의 제목|
    |Warning | 사유구절보다 자세한 경고 메세지|

  - 협상 헤더  
  : 협상 가능한 리소스 정보 전달

    |헤더|상세|
    |---|---|
    |Accept-Ranges | 서버의 수용 범위 형태|
    |Vary | 서버가 확인해야 하는 헤더 목록|

  - 응답 보안 헤더

    |헤더|상세|
    |---|---|
    |Proxy-Authenticate | 프록시에서 클라이언트에 요총한 인증요구 목록|
    |WWW-Authenticate | 서버에서 클라이언트에 요총한 인증요구 목록|
    |Set-Cookie | 서버가 클라이언트 측에 토큰 설정 시 이용|
    |Set-Cookie2 | RFC2965로 정의된 쿠키 ???|

+ Entity 헤더  
: 본문 크기, 콘텐츠/리소스 등 엔티티 관련 광범위한 정보 제공

  - 엔티티 정보 헤더  

    |헤더|상세|
    |---|---|
    |Allow | 엔티티에 적용 간으한 요청 메소드 목록|
    |Location | 엔티티 실제 위치|

  - 콘텐츠 헤더  
  : 콘텐츠의 종류, 크기 등 구체적 정보 제공

    |헤더|상세|
    |---|---|
    |Content-Base | 기저 URL|
    |Content-Encoding |본문의 인코딩|
    |Content-Language | 본문에 가장 적합한 자연어|
    |Content-Length | 본문 길이/크기|
    |Content-Location | 리소스 실제 위치|
    |Content-MD5 | 본문의 MD5 체크썸|
    |Content-Range | 전체 리소스 중 차지 범위|
    |Content-Type | 객체 타입|

  - 엔티티 캐싱 헤더  
  : 언제 어떻게 캐시가 되어야 하는지 지시자 제공

    |헤더|상세|
    |---|---|
    |ETage |엔티티 태그 (리소스의 특정 버전에 대한 식별자) <br> [=> 변하지 않은 경우 캐시 이용, 동시수정 방지](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/ETag)|
    |Expires |유효성 상실 시점|
    |Last-Modified |최종변경일시|

+ 확장 헤더  
: 명세에 정의되지 않은 새로운 헤더 => 의미 몰라도 용인 & 전달해야
