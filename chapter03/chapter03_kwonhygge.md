# HTTP 메시지

## 3.1 메시지의 흐름
- HTTP 메시지: HTTP 애플리케이션 간에 주고받은 데이터들의 블록
  - 메시지의 내용과 의미를 설명하는 텍스트 메타 정보로 시작
  - 그 다음에 선택적으로 데이터가 올 수 있음
- 클라이언트, 서버, 프락시 사이를 흐름
- 메시지의 방향(트랜잭션 방향)을 의미하는 용어: '인바운드', '아웃바운드', '업스트림', '다운스트림'

### 3.1.1 인바운드와 아웃바운드
- 인바운드: 메시지가 원 서버로 향하는 것
- 아웃바운드: 모든 처리가 끝난 뒤에 메시지가 사용자 에이전트로 돌아오는 것

### 3.1.2 다운스트림과 업스트림
- 요청 메시지냐 응답 메시지냐에 관계없이 모든 메시지는 다운 스트림으로 흐름
- 메시지의 발송자는 수신자의 업스트림

## 3.2 메시지의 각 부분
- 각 메시지는 클라이언트로부터의 요청이나 서버로부터의 응답 중 하나를 포함
- 메시지는 시작줄, 헤더 블록, 본문 이렇게 세 부분으로 이루어짐
  - 시작줄: 이것이 어떤 메시지인지 서술
  - 헤더 블록: 속성
  - 본문: 데이터를 담고 있음, 아예 없을 수도 있다
- 시작줄과 헤더: 줄 단위로 분리된 아스키 문자열
  - 각 줄은 캐리지 리턴(ASCII 13)과 개행 문자(ASCII 10)로 구성된 두 글자의 줄바꿈 문자열로 끝남
  - 이 줄바꿈 문자열은 CRLF라고 씀 -> 빈줄
  - 견고한 문자열이라면 그냥 개행 문자도 받아들일 수 있어야 함
  - 오래되거나 잘못 만들어진 HTTP 애플리케이션들 중에서는 캐리지 리턴과 개행 문자 모두를 항상 전송하지 않는 것들도 있음
- 엔터티 본문이나 메시지 본문은 단순히 선택적인 데이터 덩어리
  - 시작줄이나 헤더와 달리 텍스트나 이진 데이터를 포함할 수도 있고 그냥 비어있을 수도 있음

### 3.2.1 메시지 문법
- 모든 HTTP 메시지는 요청 메시지나 응답 메시지로 분류됨
- 요청 메시지: 서버에 어떤 동작을 요구
- 응답 메시지: 요청의 결과를 클라이언트에게 돌려줌
- 요청과 응답 모두 기본적으로 구조가 같음

- 요청 메시지의 형식
  
  <메서드> <요청 URL> <버전>
  
  <헤더>
  
  
  <엔터티 본문>
  
- 응답 메시지의 형식
  
  <버전> <상태 코드> <사유 구절>
  
  <헤더>
  
  
  <엔터티 본문>

* 각 부분에 대한 설명
- 메서드
  - 클라이언트 측에서 서버가 리소스에 대해 수행해주길 바라는 동작
  - 'GET', 'HEAD', 'POST'와 같이 한 단어로 되어있음
- 요청 URL
  - 요청 대상이 되는 리소스를 지칭하는 완전한 URL 혹은 URL의 경로 구성요소
  - 완전한 URL이 아닌 URL의 경로 구성요소라고 해도, 클라이언트가 서버와 직접 대화하고 있고 경로 구성요소가 리소스를 가리키는 절대 경로이기만 하면 대체로 문제 없음
  - 서버는 URL에서 생략된 호스트/포트가 자신을 가리키는 것으로 간주할 것
- 버전
  - HTTP/<메이저>.<마이너>
  - 메이저와 마이너는 모두 정수
- 상태 코드
  - 요청 중에 무엇이 일어났는지 설명하는 세자리의 숫자
  - 각 코드의 첫번째 자릿수는 상태의 일반적인 분류('성공','에러' 등)을 나타냄
- 사유 구절(reason-pharse)
  - 숫자로 된 상태 코드의 의미를 사람이 이해할 수 있게 설명해주는 짧은 문구
  - 상태 코드 이후부터 줄바꿈 문자열까지가 사유 구절
  - 오로지 사람에게 읽히기 위한 목적으로만 존재
- 헤더들
  - 이름, 콜론(:), 선택적인 공백, 값, CRLF가 순서대로 나타나는 0개 이상의 헤더들.
  - 이 헤더의 목록은 빈 줄(CRLF)로 끝나 헤더 목록의 끝과 엔터티 본문의 시작을 표시
  - HTTP/1.1과 같은 몇몇 버전의 HTTP는 요청이나 응답에 어떤 특정 헤더가 포함되어야만 유효한 것으로 간주됨
- 엔터티 본문
  - 임의의 데이터 블록을 포함함
  - 모든 메시지가 엔터테 본문을 갖는 것은 아니므로, 때때로 메시지는 그냥 CRLF로 끝나게 됨
  - 헤더나 엔터테 본문이 없더라도 HTTP 헤더의 집합은 항상 빈 줄(그냥 CRLF)로 끝나야 함
  - 하지만 빠뜨리는 사람들을 위해 CRLF 없이 끝나는 메시지도 받아들일 수 있어야 함

### 3.2.2 시작줄
- 모든 HTTP 메시지는 시작줄로 시작
  - 요청 메시지의 시작줄: 무엇을 해야하는지
  - 응답 메시지의 시작줄 : 무슨 일이 일어났는지
- 요청줄
  - 요청 메시지는 서버에게 리소스에 대해 무언가를 해달라고 부탁
  - 시작줄에는 서버에서 어떤 동작이 일어나야 하는지 설명해주는 메서드와 그 동작에 대한 대상을 지칭하는 요청 URL이 들어있음
  - 또한 요청줄은 클라이언트가 어떤 HTTP 버전으로 말하고 있는지 서버에게 알려주는 HTTP 버전도 포함
  - 이 모든 필드는 공백으로 구분됨
- 응답줄
  - 응답 메시지는 수행 결과에 대한 상태 정보와 결과 데이터를 클라이언트에게 돌려줌
  - 응답 메시지의 시작줄 혹은 응답줄에는 응답 메시지에서 쓰인 HTTP의 버전, 숫자로 된 상태코드, 수행 상태에 대해 설명해주는 텍스트로 된 사유구절이 들어있음
  - 이 모든 필드는 공백으로 구분됨
- 메서드
  - 요청의 시작줄은 메서드로 시작, 서버에게 무엇을 해야 하는지 말해줌
  - HEAD, GET, POST 같은 것을 말함
  - 확장 메서드: 위의 메소드들 이외에도 HTTP 명세를 확장해 추가로 구현된 메서드
- 상태 코드
  - 클라이언트에게 무엇이 일어났는지 말해줌
  - 상태 코드는 응답의 시작줄에 위치함
  - 상태 코드들은 세 자리 숫자로 된 그들의 코드값을 기준으로 묶임
    - 200~299까지는 성공을 나타냄
    - 300~399까지는 리소스가 옮겨졌음을 뜻함
    - 400~499까지는 클라이언트가 뭔가 잘못된 요청을 했음을 의미
    - 500~599까지는 서버에서 뭔가 실패했음을 의미
- 사유 구절
  - 응답 시작줄의 마지막 구성요소
  - 상태 코드에 대한 글로 된 설명 제공
- 버전 번호
  - HTTP/x.y 형식으로 요청과 응답 메시지 양쪽 모두에 기술됨
  - HTTP 애플리케이션들이 자신이 따르는 프로토콜의 버전을 상대방에게 말해주기 위한 수단이 됨
  - HTTP로 대화하는 애플리케이션들에게 대화 상대의 능력과 메시지의 형식에 대한 단서를 제공해주기 위한 것
  - HTTP/1.1이라고 된 응답은 응답을 보낸 애플리케이션이 HTTP/1.1까지 이해할 수 있음을 의미하는 것
  - HTTP/2.22 > 2.3 -> 두 수를 분리해서 봐야함 (22가 3보다 큼)
### 3.2.3 헤더
- 시작줄 다음에는 0개, 1개 혹은 여러개의 HTTP 헤더가 옴
- HTTP 헤더 필드는 요청과 응답 메시지에 추가 정보를 더함
- 이들은 기본적으로 이름/값 쌍의 목록임
- 헤더 분류
  - 일반 헤더: 요청과 응답 양쪽에 모두 나타날 수 있음
  - 요청 헤더: 요청에 대한 부가 정보를 제공
  - 응답 헤더: 응답에 대한 부가 정보를 제공
  - Entity 헤더: 본문 크기와 콘텐츠, 혹은 리소스 그 자체를 서술
  - 확장 헤더: 명세에 정의되지 않은 새로운 헤더


| 헤더의 예          | 설명                |
| ---------------- | ------------------ |
| 헤더의 예 | 설명 |
| ------- | --- |
| Date: Tue,3 Oct 1997 02:16 03 GMT | 서버가 응답을 만들어 낸 시각 |
| Content-length: 15040 | 15,040바이트의 데이터를 포함한 엔터티 본문 |
| Content-type:image/gif | 엔터티 본문은 GIF 이미지다. |
| Accept: image/gif, image/jpeg, text/html | 클라이언트는 GIF, JPEG 이미지와 HTML을 받아들일 수 있다. |


- 헤더를 여러 줄로 나누기
  - 긴 헤더 줄은 그들을 여러 줄로 쪼개서 더 읽기 좋게 만들 수 있음, 추가 줄 앞에는 최소 하나의 스페이스 혹은 탭 문자가 와야 함

### 3.2.4 엔터티 본문
- HTTP 메시지의 세번째 부분은 선택적인 엔터티 본문
- HTTP 메시지의 화물 -> HTTP가 수송하도록 설계된 것들
- HTTP 메시지는 이미지, 비디오, HTML 문서, 소프트웨어 애플리케이션, 신용카드 트랜잭션, 전자우편 등 여러 종류의 디지털 데이터를 실어 나를 수 있음

### 3.2.5 버전 0.9 메시지
- HTTP 버전 0.9는 HTTP 프로토콜의 초기 버전
- 오늘날 HTTP가 갖고 있는 요청과 응답 메시지의 시초
- 지나친 단순함 때문에 다양한 상황에 대응할 수가 없음

## 3.3 메서드
### 3.3.1 안전한 메서드(Safe Method)
- HTTP는 안전한 메서드라 불리는 메서드의 집합 정의
- GET과 HEAD 메서드는 안전 -> 이 메서드를 사용하는 요청의 결과로 서버에 어떤 작용도 없음
### 3.3.2 GET
- 주로 서버에게 리소스를 달라고 요청하기 위해 쓰임
### 3.3.3 HEAD
- 정확히 GET처럼 행동하지만, 서버는 응답으로 헤더만을 돌려줌
- 엔터티 본문은 결코 반환되지 않음 -> 클라이언트가 리소스를 실제로 가져올 필요 없이 헤더만을 조사할 수 있도록 해줌
- HEAD 사용하여 얻는 것
  - 리소스를 가져오지 않고도 그에 대해 무엇인가를 알아낼 수 있다
  - 응답의 상태 코드를 통해, 개체가 존재하는지 확인할 수 있다
  - 헤더를 확인하여 리소스가 변경되었는지 검사할 수 있다
### 3.3.4 PUT
- PUT 메서드는 서버에 문서를 씀
- PUT 메서드의 의미는, 서버가 요청의 본문을 가지고 요청 URL의 이름대로 새 문서를 만들거나, 이미 URL이 존재한다면 본문을 사용해서 교체하는 것
- PUT은 콘텐츠를 변경할 수 있게 하기 때문에, 많은 웹 서버가 PUT을 수행하기 전에 사용자에게 비밀번호를 입력해서 로그인을 하도록 요구할 것
### 3.3.5 POST
- 서버에 입력 데이터를 전송하기 위해 설계됨
- HTML 폼을 지원하기 위해 흔히 ㅅ용됨
- 채워진 폼에 담긴 데이터는 서버로 전송되며, 서버는 이를 모아서 필요로 하는 곳에 보냄
### 3.3.6 TRACE
- 클라이언트가 어떤 요청을 할 때, 그 요청은 방화벽, 프락시, 게이트웨이 등의 애플리케이션을 통과할 수 있음
- 이들에게는 원래의 HTTP 요청을 수정할 수 있는 기회가 있음
- TRACE 메서드는 클라이언트에게 자신의 요청이 서버에 도달했을 때 어떻게 보이게 되는지 알려줌
- TRACE 요청은 목적지 서버에서 '루프백' 진단을 시작함
- 요청 전송의 마지막 단계에 있는 서버는 자신이 받은 요청 메시지를 본문에 넣어 TRACE 응답을 되돌려줌
- 클라이언트는 자신과 목적지 서버 사이에 있는 모든 HTTP 애플리케이션의 요청/응답 연쇄를 따라가면서 자신이 보낸 메시지가 망가졌거나 수정되었는지, 만약 그렇다면 어떻게 변경되었는지 확인할 수 있음
- 주로 진단을 위해 사용됨
- 중간 애플리케이션이 어떤 메서드를 사용할지는 구분할 수가 없다는 것이 문제
- TRACE 요청은 어떠한 엔터티 본문도 보낼 수 없음
- TRACE 응답의 엔터티 본문에는 서버가 받은 요청이 그대로 들어있다
### 3.3.7 OPTIONS
- 웹 서버에게 여러가지 종류의 지원 범위에 대해 물어봄
- 서버에게 특정 리소스에 대해 어떤 메소드가 지원되는지 물어볼 수 있음
- 여러 리소스에 대해 실제로 접근하지 않고도 그것들을 어떻게 접근하는 것이 최선인지 확인할 수 있는 수단을 클라이언트 애플리케이션에게 제공
### 3.3.8 DELETE
- 서버에게 요청 URL로 지정한 리소스를 삭제할 것을 요청
- 그러나 클라이언트는 삭제가 수행되는 것을 보장하지 못함
- 왜냐하면 HTTP 명세는 서버가 클라이언트에게 알리지 않고 요청을 무시하는 것을 허용하기 때문
### 3.3.9 확장 메서드
- HTTP는 필요에 따라 확장해도 문제가 없도록 설계되어 있음
- 대표적인 예
  - LOCK : 사용자가 리소스를 잠글 수 있게 해줌, 예를 들어, 문서롤 편집하는 동안 다른 사람이 동시에 같은 문서를 편집하지 못하도록 문서를 잠글 수 있음
  - MKCOL: 사용자가 문서를 생성할 수 있게 해줌
  - COPY: 서버에 있는 리소스를 복사
  - MOVE: 서버에 있는 리소스를 옮김
- 모든 확장 메서드가 형식을 갖춘 명세로 정의된 것은 아니고, 어떤 애플리케이션은 이해할 수 없을 수도
- 이런 상황에서는 확장 메서드에 대해 관용적인 것이 최고
- 프락시는, 종단 간 행위를 망가뜨리지 않을 수 있다면, 알려지지 않은 메서드가 담긴 메시지를 다운스트림 서버로 전달하려고 시도
- 그렇지 않다면 프락시는 501 Not Implemented 상태 코드로 응답해야 함
- 확장 메서드를 다룰 때는 "엄격하게 보내고 관대하게 받아들여라"는 오랜 규칙에 따르는 것이 가장 좋음

## 3.4 상태 코드
- 상태 코드는 클라이언트에게 그들의 트랜잭션을 이해할 수 있는 쉬운 방법을 제공함

### 3.4.11 100-199: 정보성 상태 코드
| 상태 코드        | 사유 구절          | 의미  |
| ------------- |-------------| -----|
| 100     | Continue | $1600 |
| col 2 is      | centered      |   요청의 시작 부분 일부가 받아들여졌으며, 클라이언트는 나머지를 계속 이어서 보내야함을 의미, 서버는 반드시 요청을 받아 응답해야 함 |
| 101 | Switching Protocols     |    클라이언트가 Upgrade 헤더에 나열한 것 중 하나로 서버가 프로토콜을 바꾸었음을 의미 |
- HTTP/1.1에서 도입됨
- HTTP/1.1에서 도입됨

- 100 Continue는 HTTP 클라이언트 애플리케이션이 서버에 엔터티 본문을 전송하기 전에 그 엔터티 본문을 서버가 받아들일 것인지 확인하려고 할 때, 그 확인 작업을 최적화할 의도로 도입

- 클라이언트와 100 Continue
  - 클라이언트가 엔터티를 서버에게 보내려고 하고, 그 전에 100 Continue 응답을 기다리겠다면, 클라이언트는 값을 100-continue로 하는 Expect 요청 헤더를 보낼 필요가 있음
  - 만약 클라이언트가 엔터티를 보내지 않으려고 한다면, 100-Continue expect 헤더를 보내지 않아야 함 -> 클라이언트가 엔터티를 보낼 것이라고 생각하게 만들어 서버를 혼란에 빠뜨릴 뿐이기 때문
  - 100-continue는 여러 측면에서 최적화를 위한 것
  - 서버가 다루거나 사용할 수 없는 큰 엔터티를 서버에게 보내지 않으려는 목적으로만 사용해야 함
  - 100-continue값이 담긴 Expect 헤더를 보낸 클라이언트는 100 continue 응답을 막연히 기다리면 안됨, 약간의 타임아웃 후에 그냥 보내야 함
- 서버와 100 Continue
  - 서버가 100-continue 값이 담긴 Expect 헤더가 포함된 요청을 받는다면, 100 continue 응답 혹은 에러코드로 답해야 함
  - 서버는 절대로 100-continue 응답을 받을 것을 의도하지 않은 클라이언트에게 100 continue 상태 코드를 보내서는 안됨
  - 서버가 100 continue 응답을 보내기 전에 엔터티의 일부나 전체를 수신하였다면 상태 코드를 보낼 필요가 없음, 이미 계속 전송하기로 결정되었기 때문
  - 그러나 서버가 요청을 끝까지 다 읽은 후에는 그 요청에 대한 최종 응답을 보내야 함
  - 마지막으로 만약 서버가 100 continue 응답을 받을 것을 의도한 요청을 받고난 상태에서 엔터티 본문을 읽기 전에 요청을 끝내기로 결정했다면 서버는 그냥 응답을 보내고 연결을 닫아서는 안됨, 클라이언트가 응답을 받을 수가 없게 되기 때문
- 프락시와 100 Continue
  - 클라이언트로부터 100-continue 응답을 의도한 요청을 받은 프락시는 몇가지 해야 할 일이 있음
  - 만약 다음 홉서버가 HTTP/1.1을 따르거나 혹은 어떤 버전을 따르는지 모른다면, Expect 헤더를 포함시켜서 요청을 다음으로 전달해야 함
  - 만약 다음 홉의 서버가 1.1보다 이전 버전의 HTTP를 따른다는 것을 알고 있다면, 프락시는 417 Expectation Failed 에러로 응답해야 함
  - 만약 프락시가 HTTP/1.0이나 이전 버전을 따르는 클라이언트를 대신하여 Expect 헤더와 100-continue 값을 요청에 포함시키기로 결정했다면, 프락시는 100 Continue 응답을 클라이언트에 전달해서는 안 됨
    - 왜냐하면 클라이언트는 그것을 어떻게 해야하는 지 모를 것
  - 프락시가 다음 홉 서버들에 대한 상태 몇가지와 그들이 지원하는 HTTP 버전을 기억해둔다면, 100-continue 응답을 기대한 요청을 더 잘 다룰 수 있게 되므로 프락시에게 이득이 됨
### 3.4.2 200-299: 성공 상태 코드
- 클라이언트가 요청을 보내면, 그 요청은 대개 성공

| 상태 코드        | 사유 구절          | 의미  |
| ------------- |-------------| -----|
| 200     | Ok | 요청은 정상이고, 엔터티 본문은 요청된 리소스를 포함하고 있음 |
| 201      | Created      |   서버 개체를 생성하라는 요청을 위한 것. |
| 202 | Accepted     |    요청은 받아들여졌으나 서버는 아직 그에 대한 어떤 동작도 수행하지 않았다 |
| 203 | Non-Authoritative Information     |    엔터티 헤더에 들어있는 정보가 원래 서버가 아닌 리소스의 사본에서 왔다  |
| 204 | No Content     |    응답 메시지는 헤더와 상태줄을 포함하지만 엔터티 본문은 포함하지 않는다. 주로 웹브라우저를 새 문서로 이동시키지 않고 갱신하고자 할때  |
| 205 | Reset Content     |    주로 브라우저를 위해 사용되는 또 하나의 코드. 브라우저에게 현재 페이지에 있는 HTML 폼에 채워진 모든 값을 비우라고 말함 |
| 206 | Partial Content     |    부분 혹은 범위 요청이 성공함  |

### 3.4.3 300-399: 리다이렉션 상태 코드
- 리다이렉션 상태 코드는 클라이언트가 관심있어 하는 리소스에 대해 다른위치를 사용하라고 말해주거나 그 리소스의 내용 대신 다른 대안 응답을 제공
- 만약 리소스가 옮겨졌다면, 클라이언트에게 리소스가 옮겨졌으며 어디서 찾을 수 있는지 알려주기 위해 리다이렉션 상태 코드와 Location 헤더를 보낼 수 있음
- 리다이렉션 상태 코드 중 몇몇은 리소스에 대한 애플리케이션의 로컬 복사본이 원래 서버와 비교했을 때 유효한지 확인하기 위해 사용됨
- 일반적으로 HEAD가 아닌 요청에 대해 리다이렉션 상태 코드를 포함한 응답을 할 때, 리다이렉트될 URL에 대한 링크와 설명을 포함시키는 것은 좋은 습관

| 상태 코드        | 사유 구절          | 의미  |
| ------------- |-------------| -----|
| 300     | Multiple Choices | 클라이언트가 동시에 여러 리소스를 가리키는 URL을 요청한 경우, 그 리소스의 목록과 함께 반환함 |
| 301      | Moved Permanently      |   요청한 URL이 옮겨졌을 때 사용 |
| 302 | Found     |    301 상태 코드와 같음. 그러나 클라이언트는 Location 헤더로 주어진 URL을 리소스를 임시로 가리키기 위한 목적으로 사용해야 함 |
| 303 | See Other     |    클라이언트에게 리소스를 다른 URL에서 가져와야 한다고 말해주고자 할 때 쓰임  |
| 304 | Not Modified     |    클라이언트는 헤더를 이용해 조건부 요청을 만들 수 있음. 리소스가 수정 되었는지 체크  |
| 305 | Use Proxy     |    리소스가 반드시 프락시를 통해서 접근되어야 함을 나타내기 위해 사용 |
| 307 | Temporary Redirect    |    301 상태 코드와 비슷.  |

- 302 303 307 상태 코드 사이의 차이점은 HTTP/1.0과 HTTP/1.1 애플리케이션이 이 상태 코드를 다루는 방식의 차이점에 기인
  - HTTP/1.0 클라이언트가 POST 요청을 보내고 302 리다이렉트 상태 코드가 담긴 응답을 받으면, 클라이언트는 Location 헤더에 들어있는 리다이렉트 URL을 GET 요청으로 따라갈 것
  - HTTP/1.0 서버가 HTTP/1.0 클라이언트로부터 POST 요청을 받은 뒤 302 상태 코드를 보내는 상황이라면, 서버는 클라이언트가 리다이렉션 URL에 대한 GET 요청으로 리다이렉트를 따라가길 기대함
  - 그런데 HTTP/1.1이 303 상태 코드를 사용하여 혼란을 일으킴
  - 이 혼란을 막기 위해, HTTP/1.1 명세는 HTTP/1.1 클라이언트의 일시적인 리다이렉트를 위해 302 상태 코드 대신 307 상태 코드를 사용하라고 함
  - 그리고 서버는 302 상태 코드를 HTTP/1.0 클라이언트에게 사용하기 위해 남겨둘 수 있을 것
  
### 3.4.4 400-499: 클라이언트 에러 상태 코드
| 상태 코드        | 사유 구절          | 의미  |
| ------------- |-------------| -----|
| 400     | Bad Request | 클라이언트가 잘못된 요청을 보냈다고 말해줌 |
| 401      | Unauthorized    |   리소스를 얻기 전에 클라이언트에게 스스로를 인증하라고 요구하는 내용의 응답을 적절한 헤더와 함께 반환 |
| 402 | Payment Required     |    현재 이 상태 코드는 쓰이지 않지만, 미래에 사용될 가능성이 있음 |
| 403 | Forbidden     |    요청이 서버에 의해 거부되었음을 알려주기 위해 사용됨  |
| 404 | Not Found    |    서버가 요청한 URL을 찾을 수 없음을 알려주기 위해 사용됨  |
| 405 | Method Not Allowed     |    요청한 URL에 대해, 지원하지 않는 메서드로 요청받았을 때 사용함 |
| 406 | Not Acceptable    |    클라이언트는 자신이 어떤 종류의 엔터티를 받아들이고자 하는지에 대해 매개변수로 명시할 수 있음  |
| 407 | Proxy Authentication Required    |    401 상태 코드와 같으나, 리소스에 대해 인증을 요구하는 프락시 서버를 위해 사용됨  |

등등


### 3.4.5 500-599: 서버 에러 상태 코드
- 때때로 클라이언트가 올바른 요청을 보냈음에도 서버 자체에서 에러가 발생하는 경우가 있음


| 상태 코드        | 사유 구절          | 의미  |
| ------------- |-------------| -----|
| 500     | Internal Server Error | 서버가 요청을 처리할 수 없게 만드는 에러를 만났을 때 사용 |
| 501      | Not Implemented    |  클라이언트가 서버의 능력을 넘은 요청을 했을 때 사용됨 |
| 502 | Bad Gateway     |    프락시나 게이트웨이처럼 행동하는 서버가 그 요청 응답 연쇄에 있는 다음 링크로부터 가짜 응답에 맞닥뜨렸을 때 사용 |
| 503 | Service Unavailable     |    현재는 서버가 요청을 처리해 줄 수 없지만 나중에는 가능함을 의미하고자 할 때 사용  |
| 504 | Gateway Timeout    |    다른 서버에게 요청을 보내고 응답을 기다리다 타임아웃이 발생한 게이트웨이나 프락시에서 온 응답 |
| 505 | HTTP version Not Supported    |    서버가 지원할 수 없거나 지원하지 않으려고 하는 버전의 프로토콜로 된 요청을 받았을 때 사용됨 |


## 3.5 헤더
- 헤더와 메서드는 클라이언트와 서버가 무엇을 하는지 결정하기 위해 함께 사용됨
- 일반헤더
  - 일반 헤더는 클라이언트와 서버 양쪽 모두가 사용
  - 예를 들어, Date 헤더는 서버와 클라이언트를 가리지 않고 메시지가 만들어진 일시를 지칭하기 위해 사용하는 일반 목적 헤더
- 요청헤더
  - 이름에서 드러나는 것과 같이, 요청 헤더는 요청 메시지를 위한 헤더
  - 서버에게 클라이언트가 받고자 하는 데이터의 타입이 무엇인지와 같은 부가정보를 제공
- 응답 헤더
  - 응답 메시지는 클라이언트에게 정보를 제공하기 위한 자신만의 헤더를 가지고 있다
  - 클라이언트가 어떤 종류의 서버와 대화하고 있는지 같은
- 엔터티 헤더
  - 엔터티 본문에 대한 헤더
  - 엔터티 본문에 들어있는 데이터 타입이 무엇인지 말한다거나
- 확장 헤더
  - 애플리케이션 개발자들에 의해 만들어졌지만 아직 승인된 HTTP 명세에는 추가되지 않은 비표준 헤더
  - HTTP 프로그램은 확장 헤더들에 대해 설령 그 의미를 모른다 할지라도 용인하고 전달해야 할 필요가 있음

### Chapter 3 HTTP 메시지
  - HTTP, GET, POST 같은 것을 말함
| 203 | Non-Authoritative Information     |    엔터티  |
| 202 | Accepted     |    요청은 받아들여졌으나 서버는 아직 그에 대한 어떤 동작도 수행하지 않았다 |
