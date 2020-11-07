
# 1부 HTTP 기초

## 1장 HTTP 개관
+ [HTTP (Hypertext Transfer Protocol)](https://developer.mozilla.org/ko/docs/Web/HTTP/Overview)
  - 전 세계 웹브라우저, 서버, 웹 어플리케이션의 통신 공용어
  - 신뢰성 있는 데이터전송 프로토콜 (어플리켕션 계층)
  - 전송방식
    * HTTP 요청 (클라이언트 -> 서버)
    * HTTP 응답 (클라이언트 <- 서버)
  - 버전
    https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/Evolution_of_HTTP

    |버전|상세|
    |---|---|
    |HTTP/0.9 |GET 메서드만 존재 <br> 헤더/버전번호 미지원 => HTML만 전송 가능|
    |HTTP/1.0 |버전번호, 헤더, 메서드 추가 <br> 멀티미디어 처리 가능|
    |HTTP/1.0+ |1990년대 WWW 팽창 <br> keep-alive, 가상호스팅, 프록시 지원|
    |HTTP/1.1 |1997년 초에 공개된 첫 번째 표준 <br> 성능최적화 <- 커낵션 재사용, 청크응답, 파이프라이닝 등|
    |HTTP/2.0 |2010년 전반에 구현된 Google의 SPDY 기반 <br> 2015년에 공식 표준화 <br> 이진 프로토콜, 다중화 프로토콜 (병렬 요청 가능), 오버헤드 제거, 서버푸쉬 => 응답성 증가|

+ 리소스
  - 웹에 콘텐츠를 제공하는 모든 것
  - 종류
    * 정적 파일 ex) txt, jpg, html
    * 동적 콘텐츠 생산 프로그램 ex) stream, search result
  - MIME(Multipurpose Internet Mail Extention) 타입
    * 모든 HTTP객체에 붙는 데이터 포맷 라벨
    * 구성: primary_obj_type/specific_subtype
    * ex) text/html, text/plain, image/jpeg, video/quicktime, application/vnd.ms-powerpoint
  - 통합 자원 식별자 (Uniform Rsc Identifier, URI)  
    * 정보 리소스를 고유 식별 및 위치 지정 
    * 종류
     
      |명칭|구성|특징|
      |---|---|---|
      |[URL (Uniform Rsc Locator)](https://www.ietf.org/rfc/rfc2396.txt)<br>통합 자원 지시자|프로토콜 + 서버주소 + 리소스| 특정 서버 상의 구체적인 리소스 위치 서술|
      |[URN (Uniform Rsc Name)](https://www.ietf.org/rfc/rfc2141.txt)<br>유니폼 리소스 이름| 리소스의 유일한 이름으로 참조 | 리소스 위치 분석 인프라 지원 필요 <br> => 리소스 위치, 접속 프로토콜, 복사 여부 등에 영향X | 

+ 트랜잭션
  - 요청 메시지(명령, URI 등) + 응답 메시지(트랜잭션 결과 등)  
    ![](https://mdn.mozillademos.org/files/13687/HTTP_Request.png)  
    ![](https://mdn.mozillademos.org/files/13691/HTTP_Response.png)
  - 하나의 작업 위해 여러 트랜잭션 발생 가능 
    ex) HTML 뼈대 + 이미지 + 자바 트랜잭션 => 웹 페이지 로드
  - HTTP 요청 메서드
  
    |종류|상세|
    |---|---|
    |GET |클라이언트로 지정 리소스를 전송하라 |
    |PUT |클라이언트가 보낸 데이터를 지정 이름으로 저장하라 |
    |DELETE |지정 리소스를 삭제하라 |
    |POST |클라이언트 데이터를 서버 게이트웨이 어플리케이션으로 전송하라 |
    |HEAD |지정 리소스에 대한 응답 중 HTTP 헤더만 전송하라 |

  - HTTP 응답 상태코드

    |종류|상세|
    |---|---|
    |200 |성공. 문서가 바르게 반환됨 |
    |302 |재전송하라. 다른 위치에서 리소스를 가져가라 |
    |404 |없음. 리소스 검색 실패 |

    * 사유구절은 설명용 => 응답처리에는 상태코드만 사용

+ 메시지
  - 단순한 줄 단위의 문자열
  - 구성
    * 시작줄: 요청-명령, 응답-결과
    * 헤더: 0개 이상 존재, 콜론(:)으로 구분된 필드 쌍, 빈 라인으로 종료
    * 본문: 클라이언트가 요청한 데이터 (문자열, 이진 등 종류 무관)  

    !()[https://mdn.mozillademos.org/files/13827/HTTPMsgStructure2.png] 

### TCP 연결
+ TCP (Transmission Control Protocol)
  - 기능
    * 순서에 맞는 전달 => 순서대로 패킷 도착
    * 오류 없는 데이터 전송 => 메시지 유실/손상 발생X
    * 조각나지 않는 데이터 스트림 => 언제/어떤 크기로든 전송 가능
  - 전송계층 
    * 네트워크/HW 특성 감춤 => 범용성
    * 패킷 교환 네트워크 프로토콜의 집합 
+ IP (Internet Protocol)
  - 네트워크 계층
  - 구성: IP주소 + 포트번호(기본값:80)
  - 변환: DNS(도메인 이름 서비스)를 통해 URL 호스트명을 IP 주소로 변환
  - 통신순서
    * 웹브라우저가 URL에서 호스트명 추출
    * 웹브라우저가 서버 호스트명을 IP로 변환 
    * 웹브라우저가 URL에서 포트번호 추출
    * 웹브라우저-서버의 TCP 연결 성립
    * 웹브라우저가 HTTP 요청 전송
    * 서버가 HTTP 응답 리턴
    * TCP연결 종료 + 웹브라우저가 결과 표시
+ 텔넷
  - 원격 터미널 세션에 주로 사용
  - 컴퓨터 포트로 TCP 커넥션 연결해 서버와 직접 통신
+ netcat
  - 텔넷보다 기능 다양
  - TCP나 UDP 통신 가능
  - 튜토리얼
    * https://www.binarytides.com/netcat-tutorial-for-beginners/
    * https://www.digitalocean.com/community/tutorials/how-to-use-netcat-to-establish-and-test-tcp-and-udp-connections-on-a-vps 

### 웹의 구성요소
+ 프록시
  - 클라이언트-서버 사이의 HTTP 중개자
  - 기능
    * 클라이언트의 모든 요청을 서버에 대신 전달 (보통 요청을 수정)
    * 요청/응답을 필터링 => 보안성 증가
+ 캐시
  - 자주 방문한 페이지 사본을 클라이언트 가까이에 저장하는 HTTP 저장소
  - 기능
    *  효율성 증가
    *  프라이버시 보호
+ 게이트웨이
  - 다른 어플리케이션과 연결된 특별한 웹 서버 
  - 기능
    * 다른 서버 간의 중개자로 동작
    * 클라이언트는 진짜 서버로 인식
    * HTTP 트래픽을 다른 프로토콜로 변환 ex) HTTP <-> FTP
+ 터널
  - HTTP 통신을 단순전달만 하는 특별한 프록시 
  - 기능
    * 두 연결 사이에서 raw 데이터를 열지 않고 그대로 전달
    * 주로 비 HTTP 데이터를 하나 이상의 HTTP 연결을 통해 전송하기 위해 사용 
    * ex) HTTPS 정보를 방화벽 통과시키는 HTTP/SSL 터널
+ 에이전트
  - HTTP 요청 생성하는 웹클라이언트
  - 종류
    * 사용자 에이전트: 웹 브라우저
    * 자동화된 준지능적(semi-intelligent) 에이전트: 스파이더, 웹로봇 등
