## 4장 커넥션 관리

### TCP 커넥션
: 패킷 교환 네트워크 프로토콜의 계층화된 집합
+ 연결과정

  ||
  |---|
  |1. 브라우저가 URL의 호스트명 추출 |
  |2. 추출된 호스트명의 IP주소 검색 |
  |3. 브라우저가 URL의 포트번호를 취득 |
  |4. 취득한 IP주소와 포트로 TCP 커넥션 생성 |
  |5. 서버로 HTTP 요청 메세지 전송 |
  |6. 서버로부터 HTTP 응답 메세지 수신 |
  |7. 브라우저가 커넥션을 종료 |

+ 특성  

  |명칭|상세|
  |---|---|
  | 신뢰성 | 충돌없이 데이터를 순서대로 정확하게 전달 <br> => PC/네트워크에 문제 없는 한 안전한 전송 보장
  | 병렬성 | 서로 다른 포트를 이용해 여러 TCP 커넥션 구축
  | 유일성 | 발신지IP + 발신지포트 + 수신지IP + 수신지포트 <br> => 유일한 커넥션 조합

+ 패킷
  - 데이터를 세그먼트 단위로 나누고 이를 패킷에 담아 전달  
  ![](https://media.geeksforgeeks.org/wp-content/uploads/TCPSegmentHeader-1.png)
  - 참고: [TCP헤더구조](https://evan-moon.github.io/2019/11/10/header-of-tcp/)
+ 계층

  |HTTP 계층|HTTPS 계층|상세|
  |---|---|---|
  |HTTP |HTTP |어플리케이션 계층 |
  |- |TLS/SSL |보안 계층 |
  |TCP |TCP |전송계층 |
  |IP |IP |네트워크 계층 |
  |Network <br> Interface |Network <br> Interface |데이터링크 계층 |

+ 소켓 API 
  - 공통함수
  
    |함수|상세|
    |---|---|
    |socket() |새로운 소켓 생성 (미연결) |
    |bind() |소켓에 로컬 포트 및 인터페이스 할당 |
    |connect() |로컬 소켓과 원격 호스트 사이에 TCP연결 생성 |
    |listen() |로컬 소켓이 연결 요청 수신하도록 허용 |
    |accept() |로컬포트에 연결 성립되는 걸 대기 |
    |read() |소켓이 버퍼에 읽기 시도 |
    |write() |소켓이 버퍼에 쓰기 시도 |
    |close() |TCP 연결 종료 |
    |shutdown() |TCP 연결 입출력만 닫음 |
    |getsockopt() |로컬 소켓 설정값 취득 |
    |setsockopt() |로컬 소켓 설정값 설정 |

    * 참고: [파이썬 예제](https://velog.io/@gwak2837/네트워크-응용-설계-소켓-API) 

  - 연결과정
    
    |주체|처리|
    |---|---|
    |서버 | - socket: 소켓생성 <br> - bind: 포트 설정 <br> - listen: 연결 허용 <br> - accept: 연결 대기 |
    |클라이언트 | - IP/포트 취득 <br> - socket: 소켓생성 <br> - connect: 서버에 연결요청|
    |서버 | - 연결성립 통지 <br> - read: 요청 읽기(대기)|
    |클라이언트 | - 연결성공 <br> - write: HTTP요청 전송 <br> -read: HTTP응답 읽기(대기)|
    |서버 | - HTTP요청 처리 <br> - write: HTTP응답 전송 <br> - close: 연결 닫기|
    |클라이언트 | - HTTP응답 처리 <br> - close: 연결 닫기|

### HTTP 커넥션 지연
+ HTTP 트랜잭션 지연 요소
  - 하드웨어 성능
  - 네트워크/서버 전송속도
  - 요청/응답 메세지 크기
  - 클라이언트-서버 간 거리
  - TCP의 기술적 복잡성 
+ TCP 성능 요소
 
 |명칭|상세|특성|
 |---|---|---|
 |TCP 커넥션 <br> 핸드셰이크 | 커넥션 생성 위한 <br> SYN/SYN+ACK 패킷 교환 | 매 생성마다 지연 발생|
  |TCP 확인응답 편승 | 데이터 송출 패킷 있을 경우 <br>확인응답 패킷을 편승(piggyback) | 효율적이나 지연 발생|
  | TCP slow-start | 데이터 전송 성공한 커넥션에 높은 속도(전송가능 패킷 수) 허용 | 혼잡제어 효과|
  | nagle 알고리즘 | 작은 패킷은 버퍼에 저장해 한꺼번에 전송 <br> <-> [silly window synndrome](https://programming119.tistory.com/147)  | - 작은 메세지의 긴 지연 <br> - 확인응답 편승 지연 악화 <br> - TCP_NODELAY 파라미터로 off 가능 |
  |TIME_WAIT 지연 및 포트 고갈 |커넥션 종료 시 일정 기간(2MSL) 재연결 방지 | - 이전 커넥션 패킷 유입 방지 <br> - 성능시험 시 발생 가능|
  - silly window syndrome  
    : 1 Byte 패킷 대량 전송하는 반사회적 전송행위
  - 포트고갈  
    : 초당 요청량이 트랜잭션 처리량 초과시 새로운 포트 이용 불가  
    => 가상 IP로 이용가능 커넥션(IP+포트) 조합 증설 필요  
  - 2MSL  
    : 라우터의 세그먼트 생명주기 * 2 (=2분에서 줄어듬)

### HTTP 커넥션 관리
+ Connection 헤더  
  : 트랜잭션 종류 후 연결유지 여부 결정
  - close  
  : 메세지 전송 후 연결 종료 
  - 쉼표로 구분된 HTTP 헤더 목록 (보통 keep-alive만 해당)  
  : 발신자와 첫 번째 기기와의 연결을 정의 (포워드 시 삭제)  
+ 순차처리
  - 객체크기 모름 => 로딩 중에 화면 표시 불가 =>사용자가 심리적 지연 느낌
+ 병렬 커넥션  
  : 동시에 여러 개의 TCP 커넥션 이용
  
  |장점|단점|
  |---|---| 
  |대역폭 충분 + 지연시간 겹침 <br> => 총 지연시간 감소 가능 | 커넥션 생성 지연 <br> 메모리 부하 발생 가능| 

+ 지속 커넥션   
  : 서버나 클라이언트가 연결 종료하기 전까지는 커넥션 재사용  
  => 생성 지연 제거 but 커넥션 쌓이지 않도록 관리 필수
  
  |명칭|상세|
  |---|---|
  |HTTP/1.0+ Keep-Alive | - HTTP1.1에서 제거 <br> - 강제성 없음 (응답헤더에 없으면 거절로 인식) <br> - 본문 길이값 알거나 chunk 전송 인코딩 써야 유지 가능 <br> 예) Connection: Keep-Alive <br> Keep-Alive: timeout=120, max=5 |
  |HTTP/1.1 Persistent | - 기본값: 종료 헤더 전달 전까지 연결 지속 <br> - 강제성 없음 (Connection: close 없어도 종료 가능) <br> - 본문 길이값 알거나 chunk 전송 인코딩 필요 <br> - 과부하 방지: 클라이언트가 2개 정도 유지하는 게 바람직|
  |HTTP/1.1 Pipelining| - 기본 비활성화: 지속커넥션일 때만 이용 가능 <br> - 응답 기다리지 않고 요청 계속 전달 <br> - 순서대로 비멱등 요청만 발송해야|
  |HTTP/2.0 Multiplexing| -단일 커넥션 내에서 다중화 <br>=> 프레임화된 여러 메세지 스트림을 동시적으로 전송  |

  ![](https://mdn.mozillademos.org/files/13727/HTTP1_x_Connections.png)

  - 멍청한(dumb) 프록시 문제
    * Keep-Alive 이해 못하고 그대로 전달 -> 프록시만 커넥션 종료 대기 -> 행 & 타임아웃
    * Proxy-Connection 헤더 이용해도 중간에 멍청한 프록시가 있으면 문제 발생
  - 멱등(idempotent) 요청  
    : 연산 여러 번 수행해도 결과 동일한 요청   ex) GET 조회요청
  - 비멱등(nonidempotent) 요청  
    : 연산마다 결과 달라질 수 있는 요청 ex) POST 가입요청

  ![](https://developers.google.com/web/fundamentals/performance/http2/images/multiplexing01.svg?hl=ko)

  - [구글 HTTP/2 소개](https://developers.google.com/web/fundamentals/performance/http2?hl=ko)
    * 바이너리 프레이밍: HTTP 통신을 바이너리 인코딩된 프레임 교환으로 세분화
    * 스트림: 연결 내에서 전달되는 양방향 바이트 흐름으로 하나 이상의 메시지 전달 가능
    * 메시지: 요청/응답 메시지에 매핑되는 프레임 전체 시퀀스
    * 프레임: HTTP/2 통신의 최소 단위
    * 프레임 헤더: 프레임의 소속 스트림 식별 위한 최소한의 정보
+ 커넥션 종료
  - 언제든지 임의/오류로 종료 가능 => 재시도 등 대응 필요 (비멱등 요청은 예외)
  - Content-Length 불일치 시 서버에 확인 필요
  - 종류

    |명칭|상세|
    |---|---|
    |전체 끊기 | close()로 입/출력 채널 모두 종료 | 
    |절반 끊기 | shutdown()으로 입/출력 채널 중 하나만 종료 <br> => 출력채널만 종료 권장 (종료된 입력채널에 쓰기 오류 방지)| 

    *  connection reset by peer 오류   
    : 종료된 입력채널 전송 시 OS가 버퍼에 저장된 읽히지 않은 데이터 모두 삭제
