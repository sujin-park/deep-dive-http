# 14. 보안 HTTP

* 사용자를 식별하고 인증을 도와주는 HTTP의 기법들은 적대행위가 일어날 수 있는 커뮤니티에서 중요한 트랜잭션을 보호하기에 부족하다.
* 디지털 암호화를 이용해 도청이나 위조로부터 HTTP 트랜잭션을 안전하게 보호하는 기술을 알아본다.

## 14.1 HTTP를 안전하게 만들기

* 기본 인증과 다이제스트 인증, 메시지 무결성(요약 qop="auth-int")을 제공하는 방법은 대체로 유용하지만, 대량 구매, 은행 업무, 보안 자료 접근에는 충분히 강력하지 않다.
* 보다 중요한 트랜잭션을 위해 HTTP와 디지털 암호화 기술을 결합해야 한다.
* HTTP의 보안 버전은 효율적이고 이식성이 좋아하며, 관리가 쉽고 현실 세계의 변화에 대한 적응력이 좋아야 한다. 또한 사회와 정부의 요구사항에도 맞아야 한다.
  * 서버 인증: 클라이언트는 자신이 위조된 서버가 아닌 진짜와 이야기하고 있음을 알 수 있어야 한다.
  * 클라이언트 인증: 서버는 자신이 가짜가 아닌 진짜 사용자와 이야기하고 있음을 알 수 있어야 한다.
  * 무결성: 클라이언트와 서버는 도청에 대한 걱정 없이 서로 대화할 수 있어야 한다.
  * 효율: 저렴한 클라이언트나 서버도 이용할 수 있도록 알고리즘은 충분히 빨라야 한다.
  * 편재성(Ubiquity): 프로토콜은 거의 모든 클라이언트와 서버에서 지원되어야 한다.
  * 관리상 확장성: 누구든 어디서든 즉각적인 보안 통신을 할 수 있어야 한다.
  * 적응성: 현재 알려진 최선의 보안 방법을 지원해야 한다.
  * 사회적 생존성: 사회의 문화적, 정치적 요구를 만족시켜야 한다.

### 14.1.1 HTTPS

* HTTPS는 HTTP를 안전하게 만드는 방식 중에서 가장 인기있는 것이다.
  * 넷스케이프 커뮤니케이션 주식회사에서 개척하였으며 모든 주류 브라우저와 서버에서 지원한다.
* 웹페이지에 HTTPS로 접근하고 있는 경우 URL이 http:// 대신 https:// 로 시작하는 것을 보고 그 사실을 알아챌 수 있다.(몇몇 브라우저는 보안 아이콘을 표시한다)
* HTTPS를 사용할 때 모든 HTTP 요청과 응답 데이터는 네트워크로 보내지기 전에 암호화된다.
* HTTPS는 HTTP 하부에 전송 레벨 암호 보안 계층을 제공함으로써 동작하는데, 이 보안 계층은 안전 소켓 계층(Secure Sockets Layer, SSL) 혹은 이를 계승한 전송 계층 보안(Transport Layer Security, TLS)을 이용해 구현된다.
* SSL과 TLS는 매우 비슷해서 이 책에서는 두 가지 모두를 표현하는 용어로 'SSL'을 사용한다.
* 어려운 인코딩 및 디코딩 작업은 대부분 SSL 라이브러리 안에서 일어나기 때문에 보안 HTTP를 사용하기 위해 웹 클라이언트와 서버가 프로토콜 처리 로직을 크게 변경할 필요는 없다.
* 대부분 TCP 입력/출력 호출을 SSL 호출로 대체하고, 보안 정보 설정과 관리를 위한 몇 호출을 추가하기만 하면 된다.

## 14.2 디지털 암호학

* 암호: 텍스트를 아무나 읽지 못하도록 인코딩하는 알고리즘
* 키: 암호의 동작을 변경하는 숫자로 된 매개변수
* 대칭키 암호 체계: 인코딩과 디코딩에 같은 키를 사용하는 알고리즘
* 비대칭키 암호 체계: 인코딩과 디코딩에 다른 키를 사용하는 알고리즘
* 공개키 암호법: 비밀 메시지를 전달하는 수백만 대의 컴퓨터를 쉽게 만들 수 있는 시스템
* 디지털 서명: 메시지가 위조 혹은 변조되지 않았음을 입증하는 체크섬
* 디지털 인증서: 신뢰할 만한 조직에 의해 서명되고 검증된 신원 확인 정보

### 14.2.1 비밀 코드의 기술과 과학

* 암호법(cryptography)은 메시지 인코딩과 디코딩에 대한 과학이자 기술(art)이다.
* 암호법은 단순히 메시지를 볼 수 없도록 암호화하는 것뿐 아니라, 메시지의 변조를 방지하기 위해 사용할 수도 있으며, 누군가 어떤 메시지나 트랜잭션의 저자임을 증명하는 데도 사용할 수 있다.

### 14.2.2 암호(cipher)

* 암호법은 암호라 불리는 비밀 코드에 기반한다. 암호란 메시지를 인코딩하는 어떤 특정 방법과 나중에 그 비밀 메시지를 디코딩하는 방법이다.
* 인코딩되기 전 원본 메시지는 흔히 텍스트 혹은 평문이라고 불린다. 암호가 적용되어 코딩된 메시지는 보통 암호문이라고 한다.

### 14.2.3 암호 기계

* 암호는 상대적으로 간단한 알고리즘으로 시작햇는데, 사람이 직접 인코딩하고 디코딩해야 했기 때문이다.
* 기술이 진보하며 사람들은 보다 복잡한 암호로 메시지를 빠르고 정확하게 인코딩하고 디코딩하는 기계를 만들기 시작했다. 이 기계는 암호를 깨뜨리기 어렵게 하기 위해, 단순히 회전하는 대신 글자를 대체하고 순서를 바꾸며 메시지를 자르고 토막내었다. (ex. 제2차 세계대전 당시 독일이 사용한 에니그마 암호 기계)

### 14.2.4 키가 있는 암호

* 코드 알고리즘과 기계가 적의 손에 들어갈 수 있기 때문에 대부분 기계에는 암호 동작방식을 변경할 수 있는 큰 숫자로 된 다른 값을 설정할 수 있는 다이얼이 달려있다. 누가 기계를 훔쳐도 올바른 다이얼 설정(키 값)이 없이는 디코더가 동작하지 않을 것이다.
* 이런 암호 매개변수를 키라고 부른다. 디코딩 과정을 바르게 동작시키려면 올바른 키를 암호 기계에 입력해야 한다.
* 암호 키는 하나의 암호 기계를 여러 가상 암호 기계의 집합처럼 만들어준다. 이 가상 암호 기계는 서로 다른 키 값을 갖으며 제각각 다르게 동작한다.
* 오늘날 대부분의 암호 알고리즘은 키를 사용한다.

### 14.2.5 디지털 암호

* 디지털 계산의 도래로 두 가지 주요한 발전이 있다.
  * 속도 및 기능에 대한 기계 장치의 한계에서 벗어남으로써 복잡한 인코딩과 디코딩 알고리즘이 가능해졌다.
  * 매우 큰 키를 지원하는 것이 가능해져서, 단일 암호 알고리즘으로 키의 값마다 다른 수조 개의 가상 암호 알고리즘을 만들어낼 수 있게 되었다. 키가 길수록 인코딩의 많은 조합이 가능해지고 무작위로 추측한 키에 의한 크래킹이 어려워진다.
* 기계 장치의 물리적인 금속 키나 다이얼 설정과 달리, 디지털 키는 그냥 숫자에 불과하다.
* 디지털 키 값은 인코딩과 디코딩 알고리즘에 대한 입력값이다. 코딩 알고리즘은 데이터 덩어리를 받아서 알고리즘과 키 값에 근거하여 인코딩하거나 디코딩하는 함수다.
* 평문 메시지 P, 인코딩 함수 E, 디지털 인코딩 키 e가 주어지면 부호화된 암호문 C를 생성할 수 있다.
  * 암호문 C를 디코더 함수 D와 디코딩 키 d를 사용해서 원래 평문 P로 다시 디코딩할 수 있다.
  * 디코딩과 인코딩 함수는 서로의 역이다. P의 인코딩에 대한 디코딩은 원래 메시지 P를 돌려준다.

## 14.3 대칭키 암호법

* 많은 디지털 암호 알고리즘은 대칭키 암호라 불린다. 인코딩을 할 때 사용하는 키가 디코딩을 할 때와 같기 때문이다. (e = d, k)
* 대칭키 암호에서 발송자와 수신자 모두 통신을 위해 비밀 키 k를 똑같이 공유할 필요가 있다. 발송자는 공유된 비밀 키를 메시지를 암호화하고 그 결과인 암호문을 수신자에게 발송하기 위해 사용한다.
* 수신자는 암호문을 받은 뒤 같은 공유된 키를 사용해 원래 평문을 복원하기 위해 해독 함수를 적용한다.
* 알려진 대칭키 암호 알고리즘으로는 DES, Triple-DES, RC2, RC4 등이 있다.

### 14.3.1 키 길이와 열거 공격(Enumeration Attack)

* 비밀 키는 누설되면 안 된다. 대부분 인코딩 및 디코딩 알고리즘은 공개적으로 알려져 있으므로 키만이 유일한 비밀이다.
* 무차별로 모든 키 값을 대입해보는 공격을 열거 공격이라고 한다.
* 가능한 키 값의 개수는 키가 몇 비트이며 얼마나 많은 키가 유효한지에 달려있다.
* 대칭키 암호에서는 보통 모든 키 값이 유효하다. 8비트 키라면 256가지 값이 가능하며, 40비트라면 2<sup>40</sup>(약 1조)가지, 128비트라면 약 340,000,000,000,000,000,000,000,000,000,000,000,000가지 값이 가능하다.
* 평범한 대칭키 암호에서 40비트 키는 작고 그다지 중요하지 않는 것에는 충분할 수 있다. 그러나 초당 수십억 번의 계산이 가능한 오늘날 빠른 워크스테이션에게는 쉽게 깨질 수 있다.
  * 사용자가 암호를 자주 바꾸지 않는 이상 40비트 키는 안전하지 않다.
* 128비트 키를 사용한 대칭키 암호는 매우 강력한 것으로 간주된다. 미국 정부는 미국 국가안보국(NSA)이 깨뜨릴 수 없는 암호문을 적대적인 조직이 만들어 낼 가능성을 차단하기 위해 긴 키를 사용하는 암호화 소프트웨어의 수출을 통제한다. 암호 기반 보안에 있어 키의 길이란 이처럼 중요하다.
* Triple-DES 키와 비슷한 크기인 128비트 DES 키는 누가 얼마를 들이든 무차별 대입으로는 실질적으로 깨뜨릴 수 없다고 알려져 있다. (그러나 아예 불가능 한 것은 아니다.)

### 14.3.2 공유키 발급하기

* 대칭키 암호의 단점 중 하나는 발송자와 수신자가 서로 대화하려면 둘 다 공유키를 가져야 한다는 것이다.
  * 누군가 어떤 온라인 상점에서 물건을 주문려면 그와 상점 웹사이트만의 개인 비밀 키를 발급해야 한다. 그러려면 비밀 키를 생성하고 그것을 기억할 방법이 필요하다. 모든 손님과 상점이 수천 개의 키를 생성하고 기억해야 한다.
  * N개의 노드가 있고, 각 노드가 상대 N-1과 대화를 나눠야 한다면 대략 총 N<sup>2</sup>개의 비밀 키가 필요하다.

## 14.4 공개키 암호법

* 공개키 암호 방식은 두 개의 비대칭 키를 사용한다. 하나는 호스트 메시지를 인코딩하기 위한 것, 다른 하나는 호스트의 메시지를 디코딩하기 위한 것이다.
* 인코딩 키는 모두를 위해 공개되어 있다. 그래서 이름이 공개키 암호 방식이다. 하지만 호스트만이 개인 디코딩 키를 알고 있다.
* 공개키 암호 방식은 각 호스트마다 누구나 사용할 수 있는 인코딩 키가 할당되어 있기 때문에 대칭 키의 쌍이 N<sup>2</sup>로 폭발적으로 증가하는 것을 피할 수 있다.
* 모든 사람이 노드 X에게 보내는 메시지를 같은 키로 인코딩할 수 있으나 X를 제외한 누구도 그 메시지를 디코딩할 수 없다. 오직 X만이 디코딩 개인 키를 갖고 있기 때문인다.
* 키의 분리는, 누구나 메시지 인코딩을 할 수 있도록 해주는 동시에 메시지를 디코딩하는 것은 소유자에게만 부여한다. 이는 서버의 공개키만 있으면 되기 때문에 노드가 서버로 안전하게 메시지를 발송하는 것을 더 쉽게 해준다.
* 공개키 암호화 기술은 보안 프로토콜을 전 세계 모든 컴퓨터 사용자에게 적용하는 것을 가능하게 한다.
* 표준화된 공개키 기술 묶음을 만드는 것의 중요성 때문에 거대한 공개 키 인프라(Public-Key Infrastructure, PKI) 표준화 작업이 진행되었으며, 2014년 기준 최신 명세는 2008년에 등록된 [RFC 5280](https://tools.ietf.org/html/rfc5280)이다.

### 14.4.1 RSA

* 공개키 비대칭의 과제는 악당이 다음 내용을 알아도 비밀인 개인 키를 계산할 수 없다는 것을 확신시켜주는 것이다.
  * 공개키(공개니까 누구나 얻을 수 있다)
  * 가로채서 얻은 암호문의 일부(네트워크를 스누핑해서 획득)
  * 메시지와 그것을 암호화한 암호문(인코더에 임의 텍스트를 넣고 실행해서 획득)
* 이 모든 요구를 만족하는 공개키 암호 체계 중 유명한 것은 MIT에서 발명하고 RSA 데이터 시큐리티에서 상용화된 RSA 알고리즘이다.
  * 공개키, 평문의 일부, 공개키로 평문을 인코딩하여 얻은 평문에 대한 암호문, RSA 구현의 소스코드까지 주어졌다 하더라도 암호를 크래킹하여 해당하는 개인 키를 찾아내는 것은 컴퓨터 과학 전 분야에서 가장 어려운 문제 중 하나인 큰 수소를 계산하는 문제만큼 어렵다고 한다.

### 14.4.2 혼성 암호 체계와 세션 키

* 비대칭 공개키 암호 방식은 누구나 공개키만 알면 그 키에 대응되는 공개 서버에 안전하게 메시지를 보낼 수 있게 해준다. 두 노드가 안전하게 의사소통하려고 할 때 개인 키에 대한 협상을 먼저 해야 할 필요가 없다.
* 그러나 공개키 암호 방식 알고리즘은 계산이 느린 경향이 있다. 실제로 대칭과 비대칭 방식을 섞어서 쓴다.
  * 흔히 노드 사이 안전한 의사소통 채널을 수립할 때는 편리하게 공개 키 암호를 사용하고, 이렇게 만들어진 채널을 통해 임시의 무작위 대칭 키를 생성/교환하여 이후 나머지 데이터를 암호화할 때는 빠른 대칭키를 사용한다.

## 14.5 디지털 서명

* 암호 체계는 메시지를 암호화하고 해독하는 것 뿐 아니라 누가 메시지를 썼는지 알려주고, 그 메시지가 위조되지 않았음을 증명하기 위해 메시지에 서명을 하도록 하는 데에 이용할 수 있다.

### 14.5.1 서명은 암호 체크섬이다

* 디지털 서명은 메시지에 붙어있는 특별한 암호 체크섬이다.
* 디지털 서명의 이점
  * 서명은 메시지를 작성한 저자가 누구인지 알려준다. 저자는 저자의 극비 개인 키를 갖고 있기 때문에, 오직 저자만이 이 체크섬을 계산할 수 있다. 체크섬은 저자의 개인 '서명'처럼 동작한다.
  * 서명은 메시지 위조를 방지한다. 악의적인 공격자가 송신 중인 메시지를 수정했다면, 체크섬은 더 이상 그 메시지와 맞지 않게 된다. 체크섬은 저자의 비밀 개인 키에 관련되어 있어서 침입자는 그 위조 메시지에 대한 올바른 체크섬을 날조해낼 수 없다.
* 디지털 서명은 보통 비대칭 공개키에 의해 생성된다. 개인 키는 오직 소유자만이 알고 있어서 일종의 '지문'처럼 사용된다.
* 노드 간 메시지를 보내고 서명하는 방식
  * 노드 A는 가변 길이 메시지를 정제하여 고정된 길이의 요약(digest)로 만든다.
  * 노드 A는 요약에 사용자의 개인 키를 매개변수로 하는 '서명'함수를 적용한다. 오직 그 사용자만이 개인 키를 알고 있기 때문에, 올바른 서명 함수는 서명자가 소유자임을 보여준다.
  * 한번 서명이 계산되면, 노드 A는 그를 메시지 끝에 덧붙이고 메시지와 그에 대한 서명 둘 다 노드 B에게 전송한다.
  * 메시지를 받은 노드 B가 만약 그 메시지를 쓴 것이 정말 노드 A이며 위조되지도 않았다는 것을 확인하고 싶으면, 노드 B는 서명을 검사할 수 있다. 노드 B는 개인 키로 알아보기 어렵게 변형된 서명에 공개키를 이용한 역함수를 적용한다. 풀어낸 요약이 노드 B가 갖고 있는 버전의 요약과 일치하지 않으면 메시지가 송신 중 위조되었거나 발송자가 노드 A의 개인 키를 갖고 있지 않은 것이다.(메시지를 쓴 것도 노드 A가 아님)

## 14.6 디지털 인증서

* 디지털 인증서(흔히 'certs'라 불리는)는 인터넷의 신분증이다. 신뢰할 수 있는 기관으로부터 보증받은 사용자나 회사에 대한 정보를 담고 있다.

### 14.6.1 인증서의 내부

* 디지털 인증서에는 공식적으로 '인증 기관'에 의해 디지털 서명된 정보의 집합이 담겨있다.
  * 대상자의 이름(사람, 서버, 조직 등)
  * 유효 기간
  * 인증서 발급자(누가 이 인증서를 보증하는가)
  * 인증서 발급자의 디지털 서명
* 디지털 인증서는 대상과 사용된 서명 알고리즘에 대한 서술적인 정보 뿐 아니라 보통 대상의 공개키도 담고 있다.
* 누구나 디지털 인증서를 만들 수 있지만 그 모두가 인증서의 정보를 보증하고 인증서를 개인 키로 서명할 수 있는, 널리 인정받는 서명 권한을 얻을 수 있지는 않다.
* 일반적인 디지털 서명의 포맷
  ```
    - 인증서 포맷 버전 번호   ⎫
    - 인증서 일련번호        ⎪
    - 인증서 서명 알고리즘    ⎪
    - 인증서 발행자         ⎟
    - 유효기간             ⊢   디지털 서명 함수
    - 대상 이름            ⎪       ⎪
    - 대상의 공개키         ⎪       ⎟
    - 다른 확장 정보        ⎭       ⎟
      ...                        ⎜
    - 디지털 서명                  ↵
  ```

### 14.6.2 X.509 v3 인증서

* 디지털 인증서에 대한 전 세계적 단일 표준은 없지만, 오늘날 사용되는 대부분 인증서가 그들 정보를 X.509라 불리는 표준화된 서식에 저장하고 있다.
* X.509 v3 인증서는 인증 정보를 파싱 가능한 필드에 넣어 구조화하는 표준화된 방법을 제공한다.
* X.509 인증서 필드
  * 버전: 이 인증서가 따르는 X 509 인증서 버전의 번호. 요즘 보통 버전 3.
  * 일련번호: 인증기관에 의해 생성된 고유한 정수. CA로부터 각 인증서는 반드시 고유한 일련 번호를 가져야 한다.
  * 서명 알고리즘 ID: 서명을 위해 사용된 암호 알고리즘. ex) RSA 암호화를 이용한 MD2 요약
  * 인증서 발급자: 인증서를 발급하고 서명한 기관의 이름. X.500 포맷으로 기록되어 있다.
  * 유효 기간: 인증서가 유효한 기간. 시작일과 종료일로 정의된다.
  * 대상의 이름: 인증서에 기술된 사람이나 조직과 같은 엔터티. X.500 포맷으로 기록되어 있다.
  * 발급자 고유 ID(선택적): 발급자의 이름이 겹치는 경우를 대비한, 인증서 발급자에 대한 선택적 고유한 식별자.
  * 대상의 고유 ID(선택적): 대상의 이름이 겹치는 경우를 대비한, 인증 대상에 대한 선택적 고유한 식별자.
  * 확장: 선택적인 확장 필드의 집합(버전 3 이상에서 지원) 각 확장 필드는 중요한 것인지 아닌지가 표시되어 있다. 중요한 확장은 인증서 사용자에 의해 반드시 이해되어야 한다. 인증서 사용자가 중요한 확장 필드를 이해하지 못하면 인증서를 거절해야 한다.
    * 기본 제약: 대상과 인증기관과의 관계
    * 인증서 정책: 인증서가 어떤 정책하에 승인되었는지
    * 키 사용: 공개키가 어떻게 사용될 수 있는지에 대한 제한
  * 인증기관 서명: 위 모든 필드에 대한 인증기관의 디지털 서명. 명시된 서명 알고리즘을 사용한다.
* X.509 기반 인증서에는 (가장 중요한)웹 서버 인증서, 클라이언트 이메일 인증서, 소프트웨어 코드 사인(code-signing) 인증서, 인증기관 인증서를 비롯한 몇 가지 변종이 있다.

### 14.6.3 서버 인증을 위해 인증서 사용하기

* 사용자가 HTTPS를 통한 안전한 웹 트랜잭션 시작 시, 최신 브라우저는 자동으로 접속한 서버에서 디지털 인증서를 가져온다. 서버가 인증서를 갖고 있지 않다면, 보안 커넥션은 실패한다. 서버 인증서는 다음을 포함한 많은 필드를 갖고 있다.
  * 웹 사이트의 이름과 호스트 명
  * 웹 사이트의 공개 키
  * 서명 기관의 이름
  * 서명 기관의 서명
* 브라우저가 인증서를 받으면 서명 기관을 검사한다. 그 기관이 신뢰할만한 곳이면 브라우저는 그 공개키를 이미 알고 있을 것이며(브라우저는 여러 서명 기관의 인증서가 미리 설치된 채로 출하된다), 브라우저는 그 서명을 검증할 수 있다.
* 모르는 서명 기관이면, 브라우저는 그 서명 기관을 신뢰해야 할지 확인할 수 없으므로 대개 사용자가 서명 기관을 신뢰하는지 확인하는 대화상자를 보여준다.
* 서명 기관은 같은 사용자가 다니는 회사의 IT 부서이거나 소프트웨어 개발사일 수도 있다.

## 14.7 HTTPS의 세부사항

* HTTPS는 HTTP의 가장 유명한 보안 버전으로 널리 주류 상용 브라우저와 서버에 구현되어 있다.
* HTTPS는 HTTP 프로토콜에 대칭, 비대칭 인증서 기반 암호 기법의 강력한 집합을 결합한 것으로 HTTPS를 안전하고 동시에 유연하며 관리하기 쉽게 만들어 준다.
* HTTPS는 인터넷 애플리케이션의 성장을 가속한 동시에 웹 기반 전자상거래의 고속 성장을 이끄는 주력이다. 또한 분산된 웹 애플리케이션의 광역 보안관리에 있어 대단히 중요하다.

### 14.7.1 HTTPS 개요

* HTTPS는 그냥 보안 전송 계층을 통해 전송되는 HTTP이다. 암호화되지 않은 HTTP 메시지를 TCP를 통해 전 세계 인터넷 곳곳으로 보내는 대신, HTTPS는 HTTP 메시지를 TCP로 보내기 전에 먼저 그것을 암호화하는 보안 계층으로 보낸다.
* HTTP 전송 단계에서의 보안
  * HTTP
    ```
    - HTTP             --- 애플리케이션 계층
    - TCP              --- 전송 계층
    - IP               --- 네트워크 계층
    - 네트워크 인터페이스   --- 데이터 링크 계층
    ```
  * HTTPS
    ```
    - HTTP             --- 애플리케이션 계층
    - SSL 또는 TLS      --- 보안 계층
    - TCP              --- 전송 계층
    - IP               --- 네트워크 계층
    - 네트워크 인터페이스   --- 데이터 링크 계층
    ```
* 오늘날 HTTPS 보안계층은 SSL과 이의 현대적 대체품인 TLS로 구현되어 있다.

### 14.7.2 HTTPS 스킴

* 오늘날 보안 HTTP는 선택적이다. 따라서 웹 서버로의 요청을 만들 때 웹 서버에게 HTTP의 보안 프로토콜 버전을 수행한다고 말해줄 방법이 필요하며, 이는 URL의 스킴을 통해 이루어진다.
* 보안이 없는 일반 HTTP는 URL의 스킴 접두사가 `http`이다. 보안이 되는 HTTPS 프로토콜에서의 URL 스킴 접두사는 `https`이다.
* (웹브라우저 등) 클라이언트는 웹 리소스에 대한 트랜잭션을 수행을 요청받으면 URL의 스킴을 먼저 검사한다.
  * URL이 http 스킴을 갖고 있으면 클라이언트는 서버에 80번(기본값) 포트로 연결하고 평범한 HTTP 명령을 전송한다.
  * URL이 https 스킴을 갖으면, 클라이언트는 서버에 443번(기본값) 포트로 연결하고 서버와 바이너리 포맷으로 된 몇몇 SSL 보안 매개변수를 교환하며 '핸드셰이크'를 하고, 암호화된 HTTP 명령이 뒤를 잇는다.
* SSL 트래픽은 바이너리 프로토콜이라 HTTP와 완전히 다르다. 그 트래픽은 다른 포트(SSL은 보통  443 포트를 통해 전달된다)로 전달된다. SSL과 HTTP 트래픽 모두 80번 포트로 도착한다면 대부분 웹브라우저는 바이너리 SSL 트래픽을 잘못된 HTTP로 해석하고 커넥션을 닫을 것이다.
* 보안 서비스가 HTTP 쪽으로 좀 더 계층 통합이 되도록 하면 포트가 둘 이상 필요할 이유가 없지만 사실 이게 그렇게 심각한 문제를 일으키지 않는다.

### 14.7.3 보안 전송 셋업

* 암호화되지 않은 HTTP에서 클라이언트는 웹 서버의 80번 포트로 TCP 커넥션을 열고, 요청 메시지를 보내고, 응답 메시지를 받고, 커넥션을 닫는다.
* HTTPS에서의 절차는 SSL 보안 계층때문에 약간 더 복잡하다.
  * HTTPS에서 클라이언트는 먼저 웹 서버의 443 포트(보안 HTTP의 기본 포트)로 연결한다.
  * 일단 TCP 연결이 되고 나면 클라이언트와 서버는 암호법 매개변수와 교환 키를 협상하면서 SSL 계층을 초기화한다.
  * 핸드셰이크가 완료되면 SSL 초기화는 완료되며 클라이언트는 요청 메시지를 보안 계층에 보낼 수 있다. 이 메시지는 TCP로 보내지기 전에 암호화된다.
  * SSL 닫힘을 통지하고 TCP 커넥션이 닫힌다.

### 14.7.4 SSL 핸드셰이크

* 암호화된 HTTP 메시지를 보낼 수 있게 되기 전, 클라이언트와 서버는 SSL 핸드셰이크를 할 필요가 있다.
* 핸드셰이크에서 일어나는 일
  * 프로토콜 버전 번호 교환
  * 양쪽이 알고 있는 암호 선택
  * 양쪽의 신원을 인증
  * 채널을 암호화하기 위한 임시 세션 키 생성
* 암호화된 HTTP 데이터가 네트워크를 오가기도 전에 SSL은 통신을 시작하기 위해 상당한 양의 핸드셰이크 데이터를 주고 받는다.
* 단순화한 SSL 핸드셰이크
  * SSL 보안 매개변수 핸드셰이크 키를 만든다.
  * 클라이언트가 암호 부호들을 보내고 인증서를 요구한다.
  * 서버는 선택된 암호와 인증서를 보낸다.
  * 클라이언트가 비밀정보를 보낸다. 클라이언트와 서버는 키를 만든다.
  * 클라이언트와 서버는 서로에게 암호화를 시작한다고 말해준다.

### 14.7.5 서버 인증서

* SSL은 서버 인증서를 클라이언트로 나르고 다시 클라이언트 인증서를 서버로 날라주는 상호 인증을 지원한다.
* 그러나 오늘날 클라이언트 인증서는 웹 브라우징에선 흔히 쓰이지 않는다. 대부분 사용자는 개인 클라이언트 인증서를 갖고 있지도 않는다. 웹 서버는 클라이언트 인증서를 요구할 수 있으나 실제로 좀처럼 일어나지 않는 일이다.
* 보안 HTTPS 트랜잭션은 항상 서버 인증서를 요구한다.
  * 보안 트랜잭션을 수행할 때 대화하는 조직이 실제 그 조직이 맞는지 확인하고 싶을 것이고, 잘 알려진 인증기관에 의해 서명된 서버 인증서는 비밀 정보를 보내기 전 그 서버를 얼마나 신뢰할 수 있는지 평가하는 것을 돕는다.
* 서버 인증서는 조직의 이름, 주소, 서버 DNS 도메인 이름과 그 외 정보를 보여주는 X.509 v3에서 파생된 인증서이다.
  * HTTPS 인증서는 사이트 정보가 더해진 X.509 인증서다.

### 14.7.6 사이트 인증서 검사

* SSL 자체는 사용자에게 웹 서버 인증서 검증을 요구하지 않지만, 최신 웹브라우저 대부분은 인증서에 대해 간단한 기본적인 검사를 하고 그 결과를 더 철저한 검사를 할 수 있는 방법과 함께 알려준다.
* 넷스케이프가 제안한 웹 서버 인증서 검사 알고리즘의 수행 단계
  * 날짜 검사
      * 먼저 브라우저는 인증서가 여전히 유효한지 확인하기 위해 인증서의 시작 및 종료일을 검사한다.
      * 인증서가 만료되었거나 아직 활성화되지 않았다면 인증서 검사는 실패하고 브라우저는 에러를 보여준다.
  * 서명자 신뢰도 검사
      * 모든 인증서는 서버를 보증하는 어떤 인증 기관(Certificate Authority, CA)에 의해 서명되어 있다.
      * 여러 수준의 인증서가 있는데 각각 다른 수준의 배경 검증을 요구한다. 전자상거래 서버 인증서를 발급받고자 한다면 사업체로서 법적인 법인 증명을 제시해야 한다.
      * 누구나 인증서를 생성할 수 있으나 몇 CA는 인증서 지원자의 신원과 신뢰도를 입증하는 알기 쉬운 절차를 가진, 잘 알려진 기관이다. 브라우저는 신뢰할만한 서명 기관의 목록을 포함한 채 배포된다.
      * 브라우저가 알려져있지 않은 인증기관으로부터 서명된 인증서를 받으면 브라우저는 보통 경고를 보여준다.
      * 브라우저는 신뢰할 만한 CA가 간접적으로 서명한 인증서를 받아들이는 것을 선택할 수 있다.
  * 서명 검사
      * 한번 서명기관이 신뢰할 수 있다고 판단하면, 브라우저는 서명기관의 공개키를 서명에 적용해 그의 체크섬과 비교하여 인증서의 무결성을 검증한다.
  * 사이트 신원 검사
      * 서버가 다른 이의 인증서를 복사하거나 트래픽을 가로채는 것을 방지하기 위해 대부분 브라우저는 인증서의 도메인 이름이 대화 중인 서버의 도메인 이름과 맞는지 검사한다.
      * 서버 인증서에는 보통 단일 도메인 이름이 들어있으나, 몇 CA는 서버 클러스터나 서버 팜을 위해 서버 이름의 목록이나 서버 이름들에 대한 와일드카드 표현이 들어있는 인증서를 만든다.
      * 호스트명이 인증서의 신원과 맞지 않으면 클라이언트는 반드시 이 사실을 사용자에게 알리거나 잘못된 인증서 에러와 함께 커넥션을 끊어야 한다.

### 14.7.7 가상 호스팅과 인증서

* 가상 호스트(하나의 서버에 여러 호스트 명)로 운영되는 사이트의 보안 트래픽을 다루는 것은 까다로운 경우도 많다.
* 몇 웹 서버 프로그램은 오직 하나의 인증서만을 지원한다. 사용자가 인증서 이름이 정확히 맞지 않는 가상 호스트명에 도착하면 경고를 할 것이다.
  * ex) `cajun-shop.com` 사이트의 호스팅 제공자는 공식이름을 `cajun-shop.securesites.com`로 제공한다. `https://www.cajun-shop.com`으로 사용자가 접근했을 때, 서버 인증서에 나열된 공식 호스트명(`*.securesites.com`)은 사용자가 브라우징한 가상 호스트명(`www.cajun-shop.com`)과 맞지 않으므로 경고가 나타난다.

## 14.8 진짜 HTTPS 클라이언트

* SSL은 복잡한 바이너리 프로토콜이다. 몇 가지 SSL 클라이언트와 서버 프로그래밍을 쉽게 만들어주는 상용/오픈 소스 라이브러리들이 있다.

### 14.8.1 OpenSSL

* [OpenSSL](http://www.openssl.org)은 SSL과 TLS의 가장 인기 있는 오픈 소스 구현이다. OpenSSL 프로젝트는 강력한 다목적 암호법 라이브러리이며, SSL과 TLS 프로토콜을 구현했다. 강건하고 완전한 기능을 갖춘 사용 수준의 툴킷을 개발하고자 자원봉사자들이 협업하였다.
* OpenSSL은 SSLeay 라이브러리를 계승하였으며 인터페이스가 매우 비슷하다.

### 14.8.2 간단한 HTTPS 클라이언트

(책 내용 참고)

### 14.8.3 우리의 단순한 OpenSSL 클라이언트 실행하기

(책 내용 참고)

## 14.9 프락시를 통한 보안 트래픽 터널링

* 클라이언트는 종종 그들을 대신해 웹 서버에 접근하는 웹 프락시 서버를 이용한다. 많은 회사가 기업 네트워크와 공공 인터넷을 잇는 경계에 보안을 위한 프락시를 설치한다.
* 프락시는 방화벽 라우터가 HTTP 트래픽 교환을 허락한 유일한 장치이며 바이러스 검사나 기타 콘텐츠 제어를 수행한다.
* 클라이언트가 서버로 보낼 데이터를 서버의 공개키로 암호화하기 시작했다면 프락시는 HTTP 헤더를 읽을 수 없고, 프락시는 요청을 어디로 보내야 하는지 알 수 없게 된다.
* HTTPS가 프락시와도 잘 동작할 수 있게 하는 인기 있는 기법 중 하나는 HTTPS SSL 터널링 프로토콜이다.
  * 클라이언트는 프락시에게 자신이 연결하고자 하는 안전한 호스트와 포트를 말해준다.
  * 클라이언트는 이 내용을 프락시가 읽을 수 있도록 암호화가 시작되기 전의 평문으로 말해준다.
* HTTP는 CONNECT라 불리는 새로운 확장 메서드를 이용해 평문으로 된 종단 정보를 전송하기 위해 사용된다.
* CONNECT 메서드는 프락시에게 희망 호스트와 포트번호로 연결해달라고 말해주며, 그것이 완료되면 클라이언트와 서버 사이에서 데이터가 직접적으로 오갈 수 있게 해주는 터널을 만든다.
* CONNECT 메서드는 안전한 원 서버의 호스트명과 포트를 콜론으로 구분한, 한 줄로 된 텍스트 명령이다.
  * 호스트:포트에 뒤이어 스페이스 하나와 HTTP 버전 문자열, CRLF가 순서대로 온다.
  * 0개 이상 HTTP 요청 헤더줄이 이어지고 난 다음 빈 줄 하나가 온다.
  * 빈 줄 다음, 커넥션을 수립하기 위한 핸드셰이크가 성공했다면 SSL 데이터 전송이 시작된다.
  ```
  CONNECT home.netscape.com:443 HTTP/1.0
  User-agent: Mozilla/1.1N

  <SSL로 암호화된 데이터가 이 다음에 온다..>
  ```
* 요청의 빈 줄 다음, 클라이언트는 프락시로부터의 응답을 기다린다. 프락시는 요청을 평가하여 그것이 유효하고 사용자가 그런 커넥션을 요청할 수 있게 허가를 받았는지 확인한다.
* 모든 것이 적법하면 프락시는 목적지 서버로 연결하고 성공하면 `200 Connection Established` 응답을 클라이언트에게 보낸다.
  ```
  HTTP/1.0 200 Connection established
  Proxy-agent: Netscape-Proxy/1.1
  ```

## 14.10 추가정보
### 14.10.1 HTTP 보안
### 14.10.2 SSL과 TLS
### 14.10.3 공개키 인프라
### 14.10.4 디지털 암호