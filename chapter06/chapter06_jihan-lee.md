# HTTP 완벽가이드 정리
## 6장 프락시
### 6.1 웹 중개자

웹 프락시 서버는 클라이언트 입장에서 ```트랜젝션을 수행```하는 중개인이다.

HTTP 프락시 서버는 웹 서버이기도 하며 웹 클라이언트이기도 하다.

![프락시](https://upload.wikimedia.org/wikipedia/commons/thumb/2/27/Open_proxy_h2g2bob.svg/2880px-Open_proxy_h2g2bob.svg.png)
출처 : https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9D%EC%8B%9C_%EC%84%9C%EB%B2%84

#### 6.1.1 개인 프락시와 공유 프락시

하나의 클라이언트만을 위한 프락시를 ```개인 프락시```라고 부르며, 여러 클라이언트가 함께 사용하는 프락시는 ```공용 프락시```라 부른다.

* 공용 프락시
  대부분의 프락시는 공용이며 공유된 프락시다. 중앙 집중형 프락시를 관리하는 게 더 비용효율이 높고 쉽다.
  캐시 프락시 서버와 같은 몇몇 프락시 애플리케이션은 프락시를 이용하는 사용자가 많을수록 유리하다.
  이유는 여러 사용자들의 공통 요청에서 이득을 취할 수 있기 때문이다.

* 개인 프락시
  브라우저의 기능을 확장하거나 성능을 개선하거나 무료 ISP 서비스를 위한 광고를 운영하기 위해 작은 프락시를
  컴퓨터에서 직접 실행한다.

#### 6.1.2 프락시 vs 게이트웨이

프락시 : 같은 프로토콜을 사용하는 둘 이상의 애플리케이션을 연결
게이트웨이 : 서로 다른 프로토콜을 사용하는 둘 이상을 연결

실질적으로 프락시와 게이트웨이의 차이점은 모호하다. 프락시는 떄때로 약간의 프로토콜 변환을 하기도 한다.

---

### 6.2 왜 프락시를 사용하는가?

프락시 서버는 보안을 개선하고, 성능을 높여주며, 비용을 절약한다.

* 어린이 필터
  어린이들에게 교육 사이트를 제공하며 동시에 성인 콘텐츠를 차단하는 필터링 프락시를 사용할 수 있다.
* 문서 접근 제어자
  각각 다른 조직에서 관리되는 다양한 종류의 수많은 웹 서버들에 대한 접근 제어를 수시로 갱신할 필요 없이,
  중앙 프락시 서버에서 접근 제어를 설정할 수 있다.
* 보안 방화벽
  보안을 강화하기 위해 프락시 서버를 사용한다. 프락시 서버는 조직 안에 들어오거나 나가는 응용 레벨 프로토콜의 흐름을
  네트워크의 한 지점에서 통제한다.
* 웹 캐시
  프락시 캐시는 자주 요청하는 문서의 로컬 사본을 관리하고 해당 문서에 대한 요청이 오면
  빠르게 제공하여, 느리고 비싼 인터넷 커뮤니케이션을 줄인다.
* 대리 프락시(리버스 프락시)
  대리 프락시는 공용 콘텐츠에 대한 느린 웹 서버의 성능을 개선하기 위해 사용될 수 있다. 흔히 서버 가속기 라고 부른다.
  또한, 콘텐츠 라우팅 기능과 결합되어 주문형 복제 콘텐츠의 분산 네트워크를 만들기 위해 사용될 수 있다.
* 콘텐츠 라우터
  인터넷 트래픽 조건과 콘텐츠의 종류에 따라 요청을 특정 웹 서버로 유도하는 콘텐츠 라우터로 동작할 수 있다.
* 트랜스코더
  콘텐츠를 클라이언트에게 전달하기 전에 본문 포맷을 수정할 수 있다.
  크기를 줄이기 위해 자신을 거쳐가는 GIF 이미지를 JPG 이미지로 변환할 수 있다.
  한국어 텍스트를 스페인어 텍스트로 변환하고, HTML 문서를 단순 텍스트로 변환하는 트랜스코딩을 할 수 있다.
* 익명화 프락시
  HTTP 메시지에서 신원을 식별할 수 있는 특성들을 제겋마으로써 개인 정보 보호화 익명성 보장에 기여한다.

---

### 6.3 프락시는 어디에 있는가?

#### 6.3.1 프락시 서버 배치

* 출구 프락시
  로컬네트워크와 더 큰 인터넷 사이를 오가는 트래픽을 제어하기 위해 프락시를 로컬 네트워크의 **출구**에 넣는다. 
* 접근(입구) 프락시
  모든 요청을 종합적으로 처리하기 위해 프락시는 ISP 접근 지점에 위치하기도 한다. ISP는 사용자들의 다운로드 속도 개선,
  인터넷 대역폭 비용을 줄이기 위해 캐시 프락시를 사용해 많이 찾는 문서들의 사본을 저장한다.
* 대리 프락시
  웹 서버들의 바로 앞에 위치하여 모든 요청을 처리하고 필요할 때만 웹 서버에게 자원을 요청한다.
  일반적으로 웹 서버의 이름과 IP 주소로 스스로를 가장하기 때문에, 모든 요청은 서버가 아닌 프락시로 간다.
* 네트워크 교환 프락시
  네트워크 사이의 인터넷 피어링 교환 지점들에 놓인다.

#### 6.3.2 프락시 계층

프락시들은 ```프락시 계층``` 이라고 불리는 연쇄를 구성할 수 있다.
프락시 계층에서 프락시 서버들은 부모와 자식의 관계를 갖는다. 다음번 인바운드 프락시(서버에 가까운 쪽)을 부모,
다음번 아웃바운드 프락시(클라이언트에 가까운 쪽)는 자식이라고 부른다.

* 프락시 계층 콘텐츠 라우팅

  프락시 계층은 정적이나, 계층이 반드시 정적이어야 하는 것은 아니다.
  프락시 서버는 여러가지 판단 근거에 의해 메시지를 다양하고 유동적인 프락시 서버와 원 서버들의 집합에게 보낸다.

  * 부하 균형
    자식 프락시는 부하를 분산하기 위해 현재 부모들의 작업량 수준에 근거하여 부모 프락시를 고른다.
  * 지리적 인접성에 근거한 라우팅
    자식 프락시는 원 서버의 지역을 담당하는 부모를 선택할 수도 있다.
  * 프로토콜/타입 라우팅
    URI에 근거하여 다른 부모나 원 서버로 라우팅 할 수 있다. 어떤 특정 종류의 URI를 갖고 있는 요청의 경우,
    특별한 프락시 서버로 보내져 특별한 프로토콜로 처리될 수도 있다.
  * 유료 서비스 가입자를 위한 라우팅
    빠른 서비스를 위해 추가금을 지불했다면, 그들의 URI는 대형 캐시나 성능 개선을 위한 압축 엔진으로 라우팅 될 수 있다.
  
  동적 부모 라우팅 로직은 제품마다 다르게 구현된다.

#### 6.3.3 어떻게 프락시가 트래픽을 처리하는가

클라이언트 트래픽이 프락시로 가도록 만드는 방법은 다음 네 가지가 있다.

1)  클라이언트를 수정한다
   * 웹 클라이언트들은 수동 혹은 자동 프락시 설정을 지원
   * 프락시 설정이 되어있다면, 클라이언트는 HTTP 요청을 바로 프락시에게 전달
2)  네트워크를 수정한다
   * 네트워크 인프라를 가로채서 웹 트래픽을 프락시로 가도록 조정
   * HTTP 트래픽을 지켜보고 가로채어 클라이언트 모르게 트래픽을 프락시로 보내는 **스위칭 장치 / 라우팅 장치**가 필요
   * 인터셉트 프락시라고 부른다
3)  DNS 이름공간을 수정한다
   * DNS 이름 테이블을 수동으로 편집하거나, 특별한 동적 DNS 서버를 이용해서 조정
4)  웹 서버를 수정한다
   * 웹 서버는 HTTP 리다이렉션 명령(305)을 클라이언트에게 돌려줌으로써 클라이언트의 요청을 프락시로 리다이렉트 한다
  
---

### 6.4 클라이언트 프락시 설정

* 수동 설정
  프락시를 사용하겠다고 명시적으로 설정

* 브라우저 기본설정
   브라우저 벤더나 배포자는 브라우저를 소비자에게 전달하기 전에 프락시를 미리 설정할 수 있다.

* 프락시 자동 설정(Proxy auto-configuration, PAC)
  자바스크립트 프락시 자동 설정(PAC) 파일에 대한 URI 제공
  클라이언트는 프락시를 써야 하는지, 어떤 프락시를 써야하는지 판단하기 위해 위의 자바스크립트 파일을 실행한다

* WPAD 프락시 발견
  대부분의 브라우저는 자동설정 파일을 다운받을 수 있는 서버를 찾아주는, **웹 프락시 자동발견 프로토콜(WPAD)**을
  제공한다.

#### 6.4.1 클라이언트 프락시 설정 : 수동

각 브라우저의 설정에서 프락시의 호스트와 포트를 지정한다.

#### 6.4.2 클라이언트 프락시 설정: PAC 파일

프락시 자동 설정(PAC) 파일은 프락시 설정에 대한 보다 동적인 해결책이며,
프락시 설정을 그때그때 상황에 맞게 계산해주는 작은 자바스크립트 프로그램이다.

PAC 파일은 일반적으로 *.pac* 확장자를 가지며 MIME 타입은 *'application/x-ns-proxy-autoconfig'* 이다.

각 PAC 파일은 반드시 URI에 접근할 때 사용할 적절한 프락시 서버를 계산해주는 **FindProxyForUrl(url, host)** 라는
함수를 정의해야 한다.

함수의 반환값은 다음과 같다.

|FindProxyForURL 반환값|설명
|---|---
DIRECT|프락시 없이 연결이 직접 이루어진다
PROXY host:port|지정한 프락시를 사용해야한다
SOCKS host:port|지정한 SOCKS 서버를 사용해야한다


예시)
```javascript
function FindProxyForURL(url, host) {
  if (url.substring(0, 5) == "http:") {
    return "PROXY http-proxy.mydomain.com:8080";
  } else if (url.substring(0, 4) == "ftp:") {
    return "PROXY ftp-proxy.mydomain.com:8080";
  } else {
    return "DIRECT";
  }
}
```

#### 6.4.3 클라이언트 프락시 설정: WPAD

WPAD는 여러 발견 메커니즘들의 상승 전략을 이용해 브라우저에게 알맞은 PAC 파일을 자동으로 찾아주는 알고리즘이다.

WPAD 프로토콜이 구현된 클라이언트가 하는 일은 다음과 같다.

* PAC URI를 찾기 위해 WPAD를 사용
* 주어진 URI에서 PAC 가져오기
* 프락시 서버를 알아내기 위해 PAC 파일 실행
* 알아낸 프락시 서버를 이용해서 요청 처리

---

### 6.5 프락시 요청의 미묘한 특징들

#### 6.5.1 프락시 URI는 서버 URI와 다르다

클라이언트가 웹 서버로 요청을 보낼 때,
```
GET /index.html HTTP/1.0
User-Agent: SuperBrower v1.3
```

클라이언트가 프락시로 요청을 보낼 때,
```
GET http://www.google.com/index.html HTTP/1.0
User-Agent: SuperBrowser v1.3
```

프락시로 요청을 보낼 경우, 완전한 URI 형태를 갖는다.

* 클라이언트가 프락시를 사용하지 않도록 설정되어 있다면, 부분 URI를 보낸다
* 클라이언트가 프락시를 사용하도록 설정되어 있다면, 완전한 URI를 보낸다

#### 6.5.2 가상 호스팅에서 일어나는 같은 문제

가상으로 호스팅 되는 웹 서버는 여러 웹 사이트가 같은 물리적 웹 서버를 공유한다.
요청 하나가 부분 URI로 오면, 가상 호스팅 되는 웹 서버는 그 요청이 접근하고자 하는 웹 사이트의
호스트 명을 알 필요가 있다.

위의 프락시와 비슷하지만 다른 방법으로 해결되었다.

* 가상으로 호스팅 되는 웹 서버는 호스트와 포트에 대한 정보가 담겨 있는 ```Host 헤더```를 요구한다.

#### 6.5.3 인터셉트 프락시는 부분 URI를 받는다

클라이언트는 자신이 프락시와 대화하고 있음을 항상 알고 있는 것은 아니다.
이유는 몇몇 프락시는 클라이언트에게 보이지 않을 수 있기 때문이다.

대리 프락시나 인터셉트 프락시를 지날 때, 클라이언트는 자신이 웹 서버와 대화하고 있다고 생각하여
완전한 URI를 보내지 않을 것이다.

#### 6.5.4 프락시는 프락시 요청과 서버 요청을 모두 다룰 수 있다

프락시는 명시적인 프락시 요청에 대해서는 **완전한 URI**를 사용하고 아니면 **부분 URI**를 사용해야한다.
웹 서버 요청의 경우에는 가상 Host 헤더를 사용해야 한다.

규칙은 다음과 같다.

* 완전한 URI가 주어졌다면, 그것을 사용
* 부분 URI가 주어졌고 Host 헤더가 있다면, Host 헤더를 이용
* 부분 URI가 주어졌으나 Host 헤더가 없다면,
  * 대리 프락시라면, 프락시에 실제 서버의 주소와 포트번호가 설정되어있다
  * 이전에 인터셉트 프락시가 가로챈 트래픽에서 사용한 IP 주소와 포트번호를 사용
  * 모두 실패라면, 충분한 정보가 없는것이므로 반드시 에러 메시지를 반환해야한다

#### 6.5.5 전송 중 URI 변경

프락시 서버는 요청 URI의 변경에 매우 신경을 써야 한다. 무해해 보이는 사소한 URI 변경이라도
다운스트림 서버와 상호운용성 문제를 일으킬 수 있다.

#### 6.5.6 URI 클라이언트 자동확장과 호스트 명 분석

브라우저는 프락시의 존재 여부에 따라 요청 URI를 다르게 분석한다.
프락시가 없다면 사용자가 타이핑한 URI를 가지고 그에 대응하는 IP 주소를 찾는다.

#### 6.5.7 프락시 없는 URI 분석

브라우저는 명시적인 프락시가 존재하지 않는 경우 부분 호스트 명을 자동으로 확장한다.

#### 6.5.8 명시적인 프락시를 사용할 때의 URI 분석

명시적인 프락시를 사용한다면, **브라우저의 URI가 프락시를 그냥 지나쳐버리기 때문에** 확장 기능을 수행할 수 없다.

#### 6.5.9 인터셉트 프락시를 이용한 URI 분석

클라이언트는 성공할 때까지 모든 IP 주소에 대해 접속을 시도하지만,
어떤 IP 주소들은 죽은 것일 수 있다. 그러나, 인터셉트 프락시와 함께라면, 
**첫 번째 접속 시도 원 서버가 아닌 프락시 서버에 의해 종료된다.**

클라이언트는 성공적으로 웹 서버와 대화했다고 믿지만, 웹 서버는 죽어있다.

프락시가 최종적으로 원 서버와 상호작용할 준비가 되었을 때, 프락시는 그 IP 주소가 실제로는 다운된 서버를
가리키고 있음을 알게  될 것이다. 프락시는 호스트 헤더에 들어 있는 호스트 명을 다시 분석하든 IP 주소에 대한
역방향 DNS 룩업을 해서든 다른 IP 주소를 시도해야한다.

---

### 6.6 메시지 추적

프락시가 점점 더 흔해지면서, 서로 다른 스위치와 라우터를 넘나드는 IP 패킷의 흐름을
추적하는 것 못지않게 프락시를 넘나드는 메시지의 흐름을 추적하고 문제점을 찾아내는 것이 필요해졌다.

#### 6.6.1 Via 헤더

Via 헤더 필드는 메시지가 지나는 각 중간 노드의 정보를 나열한다.
메시지가 또 다른 노드를 지날 때마다, 중간 노드는 Via 목록의 끝에 반드시 추가되어야 한다.

```
Via: 1.1 proxy-62.irenes-isp.net, 1.0 cache.joes-hardware.com
```

첫 번째 프락시는 HTTP/1.1 프로토콜을 구현했으며 proxy-62.irenes-isp.net라 불리고,
두 번째 프락시는 HTTP/1.0 프로토콜을 구현했고, cache.joes-hardware.com로 불린다.

프락시는 또한 네트워크의 라우팅 루프를 탐지하기 위해 Via 헤더를 사용할 수 있다.
요청을 보내기 전에 자신을 가리키는 유일한(unique) 문자열을 Via 헤더에 삽입해야 하며
네트워크에 라우팅 루프가 있는지 탐지하기 위해 이 문자열이 들어온 요청에 있는지 검사해야 한다.

*Via 문법*

각 Via waypoint는 프로토콜 이름(선택. 기본은 HTTP), 프로토콜 버전(필수), 노드 이름(필수), 코멘트(선택)
최대 4개의 구성요소를 담을 수 있다.

*프로토콜 이름*

중개자가 받은 프로토콜. 프로토콜이 HTTP라면 프로토콜 이름은 없어도 된다.

*프로토콜 버전*

수신한 메시지의 버전. 버전의 포맷은 프로토콜에 따라 다르다.

*노드 이름*

호스트와 포트 번호.(포트 번호 생략됐을 시, 프로토콜의 기본 포트번호) 몇몇 조직은 정보 보호를 이유로
진짜 호스트 명을 밝히고 싶어 하지 않을 수 있는데, 그러한 경우 가명으로 대체

*Via 요청과 응답 경로*

요청과 응답 모두 프락시를 지나므로 Via 헤더를 가진다.
응답 메시지는 요청과 같은 경로를 되돌아간다.
만약 요청 메시지가 프락시 A, B, C를 지나간다면, 그에 대한 응답 메시지는
프락시 C, B, A를 지나간다.

*Via와 게이트웨이*

몇몇 프락시는 서버에게 비 HTTP 프로토콜을 사용할 수 있는 게이트웨이 기능을 제공한다.

*Server 헤더와 Via 헤더*

Server 응답 헤더 필드는 원 서버에 의해 사용되는 소프트웨어를 알려준다.

```
Server: Apache/1.3.14 (Unix) PHP/4.0.4
Server: Netscape-Enterprise/4.1
Server: Microsoft-IIS/5.0
```
응답 메시지가 프락시를 통과할 때, 프락시는 Server 헤더를 수정해서는 안 된다.

*Via가 개인정보 보호와 보안에 미치는 영향*

내부 네트워크 아키텍처의 설계와 토폴로지를 알아내기 어렵게 하기 위한 아주 강력한 보안 요구사항을
갖고 있는 조직을 위해, 프락시는 정렬된 일련의 Via 경유지 항목들(수신된 프로토콜 값이 동일한)을
하나로 합칠 수 있다.

```
Via: 1.0 foo, 1.1 devirus.company.com, 1.1 access-logger.company.com

Via: 1.0 foo, 1.1 concealed-stuff
```

수신된 프로토콜이 다르거나, 가명으로 교체되지 않은 이상 항목들을 합쳐서는 안 된다.

#### 6.6.2 TRACE 메서드

HTTP/1.1의 TRACE 메서드는 요청 메시지를 프락시의 연쇄를 따라가면서 어떤 프락시를
지나가고 어떻게 각 프락시가 요청 메시지를 수정하는지 관찰/추적할 수 있도록 해준다.

TRACE 요청이 목적지 서버에 도착했을 때, 서버는 전체 요청 메시지를 HTTP 응답 메시지의 본문에
포함시켜 송신자에게 그대로 돌려보낸다. TRACE 응답이 도착했을 때, 클라이언트는 서버가 받은 메시지와
그 메시지가 지나간 프락시들의 목록(Via 헤더 안)을 검사할 수 있다.

TRACE 응답의 Content-Type은 message/http이며 상태는 200 OK 이다

*Max-Forwards*

전달되는 메시지가 무한 루프에 빠지지 않는지 프락시 연쇄를 테스트 하거나 연쇄 중간의 특정 프락시 서버들의
효과를 체크할 때 유용하다.

---

### 6.7 프락시 인증

프락시는 **접근 제어 장치**로서 제공될 수 있다. HTTP는 사용자가 유효한 접근 권한 자격을 프락시에
제출하지 않는 한 콘텐츠에 대한 요청을 차단하는 프락시 인증이라는 메커니즘을 정의하고 있다.

* 제한된 콘텐츠에 대한 요청이 프락시 서버에 도착했을 때, 프락시 서버는
  접근 자격을 요구하는 **407 Proxy Authorization Required** 상태 코드를 어떻게 그러한
  자격을 제출할 수 있는지 설명해주는 **Proxy-Authenticate** 헤더 필드와 함께 반환할 수 있다.
* 클라이언트는 407 응답을 받으면, 로컬 데이터베이스를 확인해서든 사용자에게 물어봐서든 요구되는 자격을 수집한다.
* 자격을 획득하면, 클라이언트는 요구 자격을 Proxy-Authorization 헤더 필드에 담아서 요청을 다시 보낸다.
* 자격이 유효하면, 프락시는 원 요청을 연쇄를 따라 통과시킨다. 유효하지 않다면 407 응답을 보낸다.

---

### 6.8 프락시 상호운용성

#### 6.8.1 지원하지 않는 헤더와 메서드 다루기

프락시는 이해할 수 없는 헤더는 반드시 그대로 전달해야 하며, 같은 이름의 헤더 필드가 여러 개 있는 경우,
그들의 상대적인 순서도 반드시 유지해야 한다.

#### 6.8.2 OPTIONS: 어떤 기능을 지원하는지 알아보기

HTTP OPTIONS 메서드는 서버나 웹 서버의 특정 리소스가 어떤 기능을 지원하는지 클라이언트(혹은 프락시)가
알아볼 수 있게 해준다.

```
OPTIONS * HTTP/1.1
```
위의 요청처럼 별표(*)라면, 요청은 서버 전체의 능력에 대해 묻는 것이다.

```
OPTIONS http://www.joes-hardware.com/index.html HTTP/1.1
static HTML file wouldn't accept a POST method.
```
만약 URI가 실제 리소스라면, OPTIONS 요청은 특정 리소스에 대한 가능한 기능들을 묻는 것이다.

#### 6.8.3 Allow 헤더

요청 URI에 의해 식별되는 자원에 대해 지원되는 **메서드들이나 서버가 지원하는 모든 메서드(*인 경우)**를 열거한다.

```
Allow: GET, HEAD, PUT
```

만약 프락시가 지정된 모든 메서드를 이해할 수 없다해도, 프락시는 Allow 헤더 필드를 수정할 수 없다.
이유는 클라이언트는 원 서버와 대화하는 다른 경로를 갖고 있을 수도 있기 때문이다.