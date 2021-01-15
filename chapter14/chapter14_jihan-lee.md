# HTTP 완벽가이드 정리
## 14장 보안 HTTP
### 14.1 HTTP를 안전하게 만들기

인증(기본, 다이제스트)과 메시지 무결성(qop="auth-int")을 제공하는 방법은 대체로 쓸만하지만   
보안에 강력하지는 않다. 더 중요한 트랜젝션을 위해서, ```HTTP와 디지털 암호화 기술을 결합```해야한다.

다음음 제공해 줄 수 있는 HTTP 보안 기술이 필요하다.

* ```서버 인증```   
  클라이언트는 자신이 위조된 서버가 아닌 진짜 서버와 이야기하고 있음을 알 수 있어야 함   
* ```클라이언트 인증```   
  서버는 자신이 가짜가 아닌 진짜 사용자와 이야기하고 있음을 알 수 있어야 함   
* ```무결성```   
  클라이언트와 서버는 그들의 데이터가 위조되는 것으로부터 안전해야 함   
* ```암호화```   
  클라이언트와 서버는 도청에 걱정 없이 서로 대화할 수 있어야 함   
* ```효율```   
  저렴한 클라이언트나 서버도 이용할 수 있도록 알고리즘은 충분히 빨라야 함   
* ```편재성```   
  프로토콜은 거의 모든 클라이언트와 서버에서 지원되어야 함   
* ```관리상 확장성```   
  누구든 어디서든 즉각적인 보안 통신을 할 수 있어야 함   
* ```적응성```   
  현재 알려진 최선의 보안 방법을 지원해야 함   
* ```사회적 생존성```   
  사회의 문화적, 정치적 요구를 만족해야 함

#### 14.1.1 HTTPS

HTTPS는 HTTP를 안전하게 만드는 방식 중 가장 인기 있는 것이다.   
넷스케이프에서 개척하였으며 모든 주류 브라우저 및 서버에서 지원한다.

![](https://user-images.githubusercontent.com/39042837/104666915-56ade280-5718-11eb-81c6-8c30d4957f27.png)
출처 : http://www.ktword.co.kr/abbr_view.php?m_temp1=3132

HTTPS는 HTTP의 하부에 전송 레벨 암호 보안 계층을 제공함으로써 동작하는데,   
이 보안 계층은 ```안전 소켓 계층(Secure Sockets Layer, SSL)``` 혹은 이를 계승한   
```전송 계층 보안(Transport Layer Security, TLS)```을 이용하여 구현된다.

어려운 인코딩 및 디코딩 작업은 대부분 **SSL 라이브러리 안에서** 일어나기 때문에,   
보안 HTTP를 사용하기 위해 웹 클라이언트와 서버가 프로토콜을 처리하는 로직을 크게 변경할 필요는 없다.   
대부분, **TCP 입력/출력 호출을 SSL 호출로 대체**하고 **보안 정보를 설정하고 관리하기 위한 몇 가지 호출**을 추가하면 된다.

---

### 14.2 디지털 암호학

* ```암호```   
  텍스트를 아무나 읽지 못하도록 인코딩 하는 알고리즘   
* ```키```   
  암호의 동작을 변경하는 숫자로 된 매개변수   
* ```대칭키 암호 체계```   
  인코딩과 디코딩에 같은 키를 사용하는 알고리즘   
* ```비대칭키 암호 체계```   
  인코딩과 디코딩에 다른 키를 사용하는 알고리즘   
* ```공개키 암호법```   
  비밀 메시지를 전달하는 수백만 대의 컴퓨터를 쉽게 만들 수 있는 시스템   
* ```디지털 서명```   
  메시지가 위조 혹은 변조되지 않았음을 입증하는 체크섬   
* ```디지털 인증서```   
  신뢰할 만한 조직에 의해 서명되고 검증된 신원 확인 정보

#### 14.2.1 비밀 코드의 기술과 과학

수천년간 암호법은 비밀 메시지를 보내는데 적용해왔다. 단순 메시지를 암호화하는 것뿐 아니라,   
메시지의 변조를 방지하기 위해 사용할 수도 있다. 또한 어떤 메시지나 트랜젝션의 저자임을 증명하는 데도 사용된다.

#### 14.2.2 암호(cipher)

암호란?   
메시지를 인코딩하는 어떤 특정한 방법과 나중에 그 비밀 메시지를 디코딩하는 방법이다.

#### 14.2.3 암호 기계

암호는 상대적으로 간단한 알고리즘으로 시작했는데, 사람이 직접 인코딩 디코딩을 해야 했기 때문이다.   
그러나 똑똑한 사람들이라면 간단하게 암호를 해독하는 것도 가능했다.

기술이 발전하면서, 사람들은 보다 복잡한 암호로 메시지를 빠르고 정확하게 인코딩, 디코딩 하는 기계를 만들었다.   

#### 14.2.4 키가 있는 암호

코드 알고리즘과 기계가 적에 손에 들어갈 수 있기 때문에, 대부분의 기계들에는 암호의 동작방식을 변경할 수 있는   
큰 숫자로 된 다른 값을 설정할 수 있는 다이얼이 달려있다.

이러한 ```암호 매개변수를 키``` 라고 부른다.

디코딩 과정을 동작시키리면 올바른 키를 암호 기계에 입력할 필요가 있다. 암호 키는 하나의 암호 기계를 여러 가상 암호   
기계의 집합처럼 만들어준다. 이 가상 암호 기계들은 ```서로 다른 키 값```을 갖고 있기 때문에 제각각 다르게 동작한다.

#### 14.2.4 디지털 암호

디지털 계산의 도래로 주요한 발전 두 가지가 있었다.

* 속도 및 기능에 대한 기계 장치의 한계에서 벗어남으로써, 복잡한 인코딩, 디코딩 알고리즘 가능   
* 매우 큰 키를 지원하는 것이 가능, 단일 암호 알고리즘으로 키의 값마다 다른 수조 개의 가상 암호 알고리즘을   
  만들어낼 수 있다. 키가 길 수록 인코딩의 조합이 많아지며, 무작위로 추측한 키에 의한 크래킹이 어려워짐.

물리적인 금속 키나 다이얼과 달리, 디지털 키는 그냥 숫자에 불과하다.   
디지털 키 값은 인코딩과 디코딩 알고리즘에 대한 입력값이다.

---

### 14.3 대칭키 암호법

> 인코딩, 디코딩 할 때 키가 같은 암호 알고리즘을 대칭키 암호라 한다.

대칭키 암호에서, ```발송자와 수신자 모두 통신을 위해 비밀 키 k```를 똑같이 ```공유```해야 한다.

예를들어, 발송자는 공유키 k 키를 이용하여 메시지를 암호화하고, 그 결과인 암호문을 수신자에게 발송한다.   
수신자는 암호문을 받은 뒤 공유키 k를 사용하여 원래의 평문으로 복호화 한다.

잘 알려진 대칭키 암호 알고리즘으로 DES, Triple-DES, RC2, RC4 등이 있다.

#### 14.3.1 키 길이와 열거 공격(Enumeration Attack)

비밀 키가 누설되면 안 된다는 것은 매우 중요하다.   
알고리즘은 대부분 공개적으로 알려져 있으므로, 키만이 유일한 비밀이다!   

좋은 알고리즘은 공격자가 크래킹하려면 우주에 존재하는 모든 가능한 키 값을 시도해보는 것 외에 다른 방법이 없게 만든다.   
무차별로 모든 키 값을 대입해보는 공격을 ```열거 공격```이라고 한다.

가능한 키 값의 개수는 키가 몇 비트이며 얼마나 많은 키가 유효한지에 달려있다.

대칭키 암호에서는, 보통 모든 키 값이 유효하다. 8비트 키면 256가지 값이 가능하고,   
40비트 키라면 2<sup>40</sup>(약 1조)가지가 가능하다.

평범한 대칭키 암호에서 작고 중요하지 않은 업무에는 40비트 키가 충분하다고 판단한다.   
그러나 초당 수십억번의 계산이 가능한 오늘날에는 이는 쉽게 깨진다.

이에 반해 128비트 키를 사용한 대칭키 암호는 매우 강력한 것으로 간주된다.

---

### 14.4 공개키 암호법 (비대칭키)

> 공개키 암호 방식은 두 개의 비대칭 키를 사용한다.

하나는 호스트의 메시지를 인코딩하기 위한 것이며, 다른 하나는 그 호스트의 메시지를 디코딩하기 위한 것이다.   
인코딩 키는 모두를 위해 공개되어있다. (그래서 공개키 암호법 이라고 명명됨)   

하지만, ```호스트만이 개인 디코딩 키```를 알고 있다.

대칭키 방식과 달리 공개키 방식을 사용하면 모든 호스트가 공개된 키를 통해 인코딩할 때 사용 가능하므로   
대칭키 처럼 쌍이 N<sup>2</sup>만큼 증가하는 것을 피할 수 있다.

모든 사람이 동일한 키로 메시지를 인코딩 해서 X에게 보내지만, 메시지를 받는 X를 제외하고는 그 누구도   
메시지를 디코딩할 수 없다. 오직 메시지를 받는 X만 개인 디코딩 키를 갖고 있기 때문이다.

#### 14.4.1 RSA

공개키 비대칭 암호의 과제는, 악당이 아래의 내용을 알고 있다 하더라고 비밀인 개인키를 계산할 수 없음을   
확신시켜 주는것이다.

* 공개키(공개니까 누구나 얻을 수 있음)
* 가로채서 얻은 암호문의 일부(네트워크를 스누핑해서 획득)
* 메시지와 그것을 암호화한 암호문(인코더에 임의의 텍스트를 넣고 실행해서 획득)

이 모든 요구를 만족하는 공개키 암호 체계 중 유명한 하나는 MIT에서 발명되고 이어서 RSA 데이터 시큐리티에서   
상용화된 ```RSA 알고리즘``` 이다.

#### 14.4.2 혼성 암호 체계와 세션 키

공개키 암호 방식의 알고리즘은 계산이 느린 경향이 있다. 실제로는 **대칭과 비대칭 방식을 섞은 것**이 쓰인다.   

노드들 사이의 안전한 의사소통 채널을 수립할 때는 공개 키 암호를 사용하고,   
안전한 채널이 수립되면 임시의 무작위 대칭 키를 생성하고 교환하여 이후의 나머지 데이터를 암호화 할 때는   
빠른 대칭 키를 사용하는 방식이 쓰인다.

---

### 14.5 디지털 서명

> 암호 체계는 메시지 암 복호화 뿐만 아니라, 누가 메시지를 썼는지 알려주고, 그 메시지가 위조되지 않음을   
증명하기 위해 메시지에 서명을 하는 데에 이용될 수 있다.

#### 14.5.1 서명은 암호 체크섬이다

> 디지털 서명은 메시지에 붙어있는 특별한 암호 체크섬이다.

다음의 두 가지 이점을 가진다.

* 서명은 메시지를 작성한 저자가 누구인지 알려준다.   
  저자는 저자의 개인 키를 갖고 있기 때문에, 오직 저자만이 이 체크섬을 계산할 수 있다. 체크섬은 저자의 개인 '서명' 처럼 동작한다.

* 서명은 메시지 위조를 방지한다.   
  악의적인 공격자가 송신중인 메시지를 수정했다면, 체크섬은 더 이상 그 메시지와 맞지 않게 된다.   
  체크섬은 저자의 비밀 개인 키에 관련되어 있기 때문에, 침입자는 그 위조된 메시지에 대한 올바른 체크섬을 날조할 수 없다.

디지털 서명은 보통 ```비대칭 공개키에 의해 생성```된다.   
개인키는 오직 소유자만이 알고 있기 떄문에, 저자의 개인 키는 일종의 **'지문'** 처럼 사용된다.

다음은 디지털 서명 절차이다.

1. A는 메시지를 요약하여, 그 요약에 사용자의 개인 키를 매개변수로 하는 **'서명'** 함수를 적용한다.   
2. 서명이 계산되면, A는 그것을 메시지의 끝에 붙이고 메시지와 서명 둘 다를 B에게 전송한다.   
3. 메시지를 받은 B는 공개키를 이용하여 A가 보낸 서명을 역함수를 적용하여 풀어낸다.   
   만약, 풀어낸 요약이 B가 갖고 있는 버전의 요약과 일치하지 않는다면 메시지가 송신 중 위조되었거나   
   발신자가 개인 키를 갖고있지 않은 것이다(A가 아니다.)

---

### 14.6 디지털 인증서

> 디지털 인증서는 신뢰할 수 있는 기관으로부터 보증 받은 사용자나 회사에 대한 정보를 담고 있다.

#### 14.6.1 인증서의 내부

디지털 인증서에는 공식적으로 '인증 기관'에 의해 디지털 서명된 정보의 집합이 담겨있다.

* 대상의 이름(사람, 서버, 조직 등)
* 유효 기간
* 인증서 발급자(누가 이 인증서를 보증하는지)
* 인증서 발급자의 디지털 서명

추가적으로, 보통 대상의 공개키도 담고 있다. 누구나 디지털 인증서를 만들 수 있지만, 그 모두가 인증서의   
정보를 보증하고 인증서를 개인 키로 서명할 수 있는 널리 인정받는 서명 권한을 얻을 수 있는 것은 아니다.

![image](https://user-images.githubusercontent.com/39042837/104678683-4d7c4000-572f-11eb-9c65-ffd46a2a5e4d.png)
출처 : http://www.ktword.co.kr/abbr_view.php?m_temp1=1004

그림은 일반적인 인증서 구조이다.

#### 14.6.2 X.509 v3 인증서

디지털 인증서에 대한 전 세계적인 단일 표준은 없다.   
그러나 오늘날 사용되는 대부분의 인증서가 그들의 정보를 X.509라 불리는 표준화된 서식에 저장하고 있다.   

X.509 기반 인증서에는 (가장 중요한) 웹 서버 인증서, 클라이언트 이메일 인증서, 소프트웨어 코드사인 인증서,   
인증기관 인증서를 비롯한 몇 가지 변종이 존재한다.

#### 14.6.3 서버 인증을 위해 인증서 사용하기

사용자가 HTTPS를 통한 웹 트랜젝션을 시작할 때, 브라우저는 자동으로 ```접속한 서버에서 디지털 인증서를 가져온다```.   
**서버가 인증서를 갖고 있지 않다면, 보안 커넥션은 실패한다**.

서버 인증서는 다음을 포함한 많은 필드를 갖고 있다.

* 웹 사이트의 이름과 호스트명
* 웹 사이트의 공개키
* 서명 기관의 이름
* 서명 기관의 서명

브라우저는 인증서를 받으면 서명 기관을 검사한다. 그 기관이 신뢰할만한 서명 기관이라면 브라우저는 그것의   
공개키를 이미 알고 있을 것이다.   
만약 서명 기관이 모르는 곳이라면, 브라우저는 그 서명 기관을 신뢰할 것인지에 대한 판단을 사용자에게 넘긴다.   

---

### 14.7 HTTPS의 세부사항

#### 14.7.1 HTTPS 개요

> HTTPS는 보안 전송 계층을 통해 전송되는 HTTP이다.

HTTPS는 HTTP 메시지를 TCP로 보내기 전에 먼저 그것들을 암호화하는 보안 계층으로 보낸다.

#### 14.7.2 HTTPS 스킴

* 보안이 없는 HTTP URL의 스킴 접두사는 ```http``` 이다.   
  > http://www.google.com

* 보안이 되는 HTTPS URL의 스킴 접두사는 ```https``` 이다.   
  > https://www.google.com

클라이언트는 웹 리소스에 대한 트랜젝션 수행을 요청받으면 URL의 스킴을 검사한다.

* http 스킴을 가지고 있다면   
  클라이언트는 서버에 80(기본값) 포트로 연결하고 평범한 HTTP 명령을 전송한다.   
* https 스킴을 가지고 있다면   
  클라이언트는 서버에 443(기본값) 포트로 연결하고 서버와 바이너리 포맷으로 된 몇몇 SSL 보안 매개변수를 교환하면서   
  '핸드셰이크'를 하고, 암호화된 HTTP 명령이 뒤를 잇는다.

#### 14.7.3 보안 전송 셋업

* HTTP   
  1. (클라이언트 -> 서버) 80 포트로 TCP 커넥션 수립
  2. (클라이언트 -> 서버) TCP를 통해 보내진 HTTP 요청
  3. (클라이언트 <- 서버) TCP를 통해 보내진 HTTP 응답
  4. TCP 커넥션 닫힘

* HTTPS   
  1. (클라이언트 -> 서버) 443 포트로 TCP 커넥션 수립
  2. (클라이언트 <-> 서버) SSL 보안 매개변수 핸드셰이크
  3. (클라이언트 -> 서버) SSL을 통해 보내진 HTTP 요청 / TCP를 통해 보내진 암호화된 요청
  4. (클라이언트 <- 서버) SSL을 통해 보내진 HTTP 응답 / TCP를 통해 보내진 암호화된 응답
  5. (클라이언트 <-> 서버) SSL 닫힘 통지
  6. TCP 커넥션 닫힘

#### 14.7.4 SSL 핸드셰이크

암호화된 HTTP 메시지를 보내기 전에, 클라이언트와 서버는 SSL 핸드셰이크를 할 필요가 있다.

* 프로토콜 버전 번호 교환
* 양쪽이 알고 있는 암호 선택
* 양쪽의 신원을 인증
* 채널을 암호화하기 위한 임시 세션 키 생성

암호화된 HTTP 데이터가 네트워크를 오가기도 전에, SSL은 통신을 시작하기 위해 상당한 양의 핸드셰이크 데이터를 주고받는다.

단순화한 SSL 핸드셰이크의 핵심은 다음과 같다.

1. 클라이언트가 암호 후보들을 보내고 인증서를 요구한다.
2. 서버는 선택된 암호와 인증서를 보낸다.
3. 클라이언트가 비밀정보를 보낸다. 클라이언트와 서버는 키를 만든다.
4. 클라이언트와 서버는 서로에게 암호화를 시작한다고 말해준다.

#### 14.7.5 서버 인증서

보안 HTTPS 트랜젝션은 항상 서버 인증서를 요구한다. 서버에 신용카드나 개인정보를 보내기 전에   
그 서버를 얼마나 신뢰할 수 있는지 평가하는것을 도와줄 것이다.

#### 14.7.6 사이트 인증서 검사

* 날짜검사   
  브라우저는 인증서가 유효함을 확인하기 위해 인증서의 시작 및 종료일을 검사한다.   

* 서명자 신뢰도 검사   
  모든 인증서는 서버를 보증하는 어떤 인증 기관(Certificate Authority, CA)에 의해 서명되어 있다.   
  여러 수준의 인증서가 있는데, 예를 들어 전자상거래 서버 인증서를 발급 받고자 한다면,   
  사업체로서의 법인에 대한 법적 증명을 제시해야 한다.   

* 서명 검사   
  한번 서명 기고나이 믿을 만하다고 판단하면, 브라우저는 서명기관의 공개키를 서명에 적용하여   
  그의 체크섬과 비교해봄으로써 인증서의 무결성을 검사한다.   

* 사이트 시원 검사   
  대부분의 브라우저는 인증서의 도메인 이름이 대화중인 서버의 도메인 이름과 비교하여 맞는지 검사한다.   
  보통은 단일 도메인 이름이 들어있지만 몇몇 CA는 서버 클러스터나 서버 팜을 위해 서버 이름의 목록이나   
  서버 이름들에 대한 와일드 카드 표현이 들어있는 인증서를 만든다.   

#### 14.7.7 가상 호스팅과 인증서

가상 호스트(하나의 서버에 여러 호스트 명)로 운영되는 사이트의 보안 트래픽을 다루는것은   
까다로운 경우도 많다. 몇몇 인기 있는 웹 서버 프로그램은 오직 하나의 인증서만을 지원한다.   

---

### 14.8 진짜 HTTPS 클라이언트

SSL은 복잡한 바이너리 프로토콜이다.

#### 14.8.1 OpenSSL

OpenSSL은 SSL과 TLS의 가장 인기 있는 오픈 소스 구현이다.   

---

#### 14.9 프락시를 통한 보안 트래픽 터널링

클라이언트가 서버로 보낼 데이터를 서버의 공개키로 암호화하기 시작하면,   
**프락시는 더이상 HTTP 헤더를 읽을 수 없다.**

프락시가 HTTP 헤더를 읽을 수 없다면, 프락시는 요청을 어디로 보내야 하는지 알 수 없게 된다.

위의 문제를 해결하기 위해 HTTPS SSL 터널링 프로토콜을 사용하면 된다.   

* HTTP는 CONNECT라 불리는 새로운 확장 메서드를 이용해서 평문으로 된 종단 정보를 전송한다.
* CONNECT 메서드는 프락시에게 희망하는 호스트와 포트번호로 연결해달라고 요청한다.
* 완료되면 클라이언트와 서버 사이에서 데이터가 직접적으로 오갈 수 있게 해주는 터널을 만든다.

```
// 구조
CONNECT 호스트:포트 HTTP버전CRLF

<SSL로 암호화된 데이터>

// 예시
CONNECT home.netscape.com:443 HTTP/1.0
User-agent: Mozilla/1.1N

<SSL로 암호화된 데이터>
```

요청의 빈 줄 다음에, 클라이언트는 프락시로부터 응답을 기다린다. 프락시는 요청을 판단 후,   
적번하다면 목적지 서버로 연결하고 성공하면 200 Connection Established 응답을 클라이언트에게 보낸다.

```
HTTP/1.0 200 Connection established
Proxy-agent: Netscape-Proxy/1.1
```