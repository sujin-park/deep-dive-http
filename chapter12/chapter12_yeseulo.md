# 12. 기본 인증

* 웹의 모든 정보와 업무가 공용은 아니기 때문에 허가된 사람만이 데이터에 접근하고 처리할 수 있어야 한다. 그러기 위해서 서버는 사용자가 누구인지 식별할 수 있어야 한다.
* 서버가 사용자가 누구인지 알면 그 사용자가 어떤 작업, 리소스에 접근할 수 있는지 결정할 수 있다.

## 12.1 인증

* 인증은 당신이 누구인지 증명하는 것이다. 보통 사용자 이름과 비밀번호를 입력하여 인증한다.

### 12.1.1 HTTP의 인증요구/응답 프레임워크

* HTTP는 사용자 인증에 사용하는 자체 인증 요구/응답 프레임워크를 제공한다.
* 웹 애플리케이션이 HTTP 요청 메시지를 받으면 서버는 요청 처리 대신 현재 사용자가 누구인지 알 수 있게 비밀번호와 같은 개인정보를 요구하는 '인증 요구'로 응답할 수 있다.
* 사용자가 다시 인증 요청을 보낼 때는 인증 정보(사용자 이름과 비밀번호)를 첨부해야 한다.
* 인증 정보가 맞지 않으면 서버는 클라이언트에게 다시 인증 요구를 보내거나 에러를 낼 수 있다.
* 인증 정보가 맞으면 문제 없이 요청이 처리된다.

### 12.1.2 인증 프로토콜과 헤더

* HTTP는 필요에 따라 고쳐쓸 수 있는 제어 헤더를 통해, 다른 인증 프로토콜에 맞춰 확장할 수 있는 프레임워크를 제공한다.
* 인증 단계마다 헤더 형식과 내용은 인증 프로토콜에 따라 달라진다. 인증 프로토콜을 HTTP 인증 헤더에 기술되어 있다.
* HTTP 인증 프로토콜: 기본 인증, 다이제스트 인증(13장 참고)이라는 두 공식 인증 프로토콜이 있다.
  * 현대에 HTTP 인증요구/응답 프로토콜을 사용하는 인증 프로토콜로 OAuth가 있다. 모바일 기기 같은 다양한 애플리케이션에서 API 인증을 위해 사용하는 최신 인증 프로토콜이다. [RFC6749](https://tools.ietf.org/html/6749) 참고.
* 네 가지 인증 단계
  * 요청: 첫 번째 요청에는 인증 정보가 없다. `GET` 메서드.
  * 인증 요구: 서버는 사용자에게 사용자 이름과 비밀번호를 제공하라는 지시 의미로 `401 Unauthorized` 상태 정보와 함께 요청을 반려한다. 서버에 각각 다른 비밀번호가 있는 영역들이 있을 것이므로 서버는 `WWW-Authenticate` 헤더에 해당 영역을 설명한다.
  * 인증: 클라이언트는 요청을 다시 보내고 인증 알고리즘과 사용자 이름, 비밀번호를 기술한 `Authorization` 헤더를 같이 보낸다. `GET` 메서드.
  * 성공: 인증 정보가 정확하면 서버는 `200 OK` 상태 코드를 반환하며 문서와 함께 응답한다. 어떤 인증 알고리즘은 선택적 헤더인 `Authentication-Info`에 인증 세션에 관한 추가 정보를 기술할 수도 있다.

### 12.1.3 보안 영역

* 웹 서버는 기밀 문서를 보안 영역(realm) 그룹으로 나누고, 보안 영역은 저마다 다른 사용자 권한을 요구한다.
  * 예) 회사의 재정 정보와 개인 가족 문서: 각 사용자는 서로 다른 영역으로 접근한다.
* realm 파라미터가 기술된 기본 인증 예
  ```
  HTTP/1.0 401 Unauthroized
  WWW-Authenticate: Basic realm="Corporate Financials"
  ```
* realm은 "Corporate Financials(회사 재무)"처럼 해설 형식으로, 사용자가 권한 범위를 이해하는 데 도움이 되어야 한다.
  * realm에 서버 호스트명을 넣을 수도 있다.

## 12.2 기본 인증

* 기본 인증은 가장 잘 알려진 HTTP 인증 규약이다. 거의 모든 주요 클라이언트와 서버에 구현되어 있다.
* 원래 HTTP/1.0에 기술되어 있었지만 HTTP 인증의 상세 내용을 다루는 RFC 2617로 옮겨졌다.
* 기본 인증에서 웹 서버는 클라이언트 요청을 거부하고 유효한 사용자 이름과 비밀번호를 요구할 수 있다.

### 12.2.1 기본 인증의 예

* 사용자가 특정 파일에 접근을 요청하면, 서버는 `WWW-Authenticate` 헤더와 함께 특정 파일에 접근하는데 필요한 비밀번호를 요구하는 `401 Authorization Required` 응답을 반환한다.
* 브라우저가 401 응답을 받고 사용자 이름과 비밀번호를 요구하는 대화상자를 띄운다. 사용자가 사용자 이름과 비밀번호를 입력하면 브라우저는 이를 콜론으로 이어 붙여 base-64 방식으로 인코딩하고, `Authorization` 헤더에 그 값을 담아 서버로 보낸다.
* 서버는 사용자 이름과 비밀번호를 디코딩하고 그 값이 정확한지 검사한 후, 문제가 없으면 `HTTP 200 OK` 메시지와 함께 요청받은 문서를 보낸다.
* 기본 인증 헤더
  * 인증요구(서버에서 클라이언트로): 각 사이트는 보안 영역마다 다른 비밀번호가 있을 것이다. realm은 요청 받은 문서 집합의 이름을 따옴표로 감싼 것으로, 사용자는 이 정보를 보고 어떤 비밀번호를 사용해야 하는지 알 수 있다.
    * `WWW-Authenticate: Basic realm=<따옴표로 감싼 문서 집합 정보>`
  * 응답(클라이언트에서 서버로): 사용자 이름, 비밀번호를 콜론으로 잇고 base-64로 인코딩하여 사용자 이름, 비밀번호에 쉽게 국제문자를 포함할 수 있게 하고, 네트워크 트래픽에 사용자 이름과 비밀번호가 노출되지 않게 한다.
    * `Authorization: Basic <base-64로 인코딩한 사용자 이름과 비밀번호>`
* 기본 인증 프로토콜은 `Authenticate-Info` 헤더를 사용하지 않는다.

### 12.2.2 Base-64 사용자 이름/비밀번호 인코딩

* base-64 인코딩은 8비트 바이트로 이루어져 있는 시퀀스를 6비트 덩어리의 시퀀스로 변환한다. 각 6비트 조각은 대부분 문자와 숫자로 이루어진 특별한 64개의 문자 중 선택된다.
* 사용자 이름과 비밀번호로 기본 `Authorization` 헤더 생성하기
  * (a) 사용자 이름(`brian-totty`)과 비밀번호(`Ow!`)를 입력받는다.
  * (b) 사용자 이름과 비번을 콜론으로 잇는다. `brian-totty;Ow!`
  * (c) Base 64 인코딩한다. `BASE64ENC(brian-totty;Ow!)` -> `YnJpYW4tdG90dHk6T3ch`
  * (d) 인가 요청을 보낸다. `Authorization: Basic YnJpYW4tdG90dHk6T3ch`
* Base-64 인코딩은 바이너리, 텍스트, 국제 문자 데이터 문자열을 받아서 전송할 수 있도록, 전송 가능한 문자인 알파벳으로 변환하기 위해 발명되었다. 전송 중 원본 문자열 변질의 걱정 없이 원격에서 디코딩 할 수 있다.
* base-64 인코딩은 국제 문자나 HTTP 헤더에서 사용할 수 없는 문자(큰따옴표, 콜론, 캐리지 리턴)를 포함한 사용자 이름, 비밀번호를 보낼 때 유용하다.
* base-64는 어렵지 않게 사용자 이름과 비밀번호 문자를 섞을 수 있어서, 서버나 네트워크 관리 중 사용자 이름과 비밀번호 노출 문제를 예방하는데 도움이 된다.

### 12.2.3 프락시 인증

* 중개 프락시 서브를 통해 인증할 수도 있다. 회사 서버나 LAN, 무선네트워크에 접근 전 프락시 서버를 거치게 하여 사용자 인증을 한다.
* 프락시 인증은 웹 서버의 인증과 헤더, 상태코드만 다르고 절차는 같다.
* 서버 인증 VS 프락시 인증
  * 비인증 상태 코드: 401 -- 비인증 상태 코드: 407
  * `WWW-Authenticate` -- `Proxy-Authenticate`
  * `Authorization` -- `Proxy-Authorization`
  * `Authentication-Info` -- `Proxy-Authentication-Info`

## 12.3 기본 인증의 보안 결함

* 보안 결함의 예
  1. 기본 인증은 사용자 이름과 비밀번호를 쉽게 디코딩할 수 있는 형식으로 네트워크에 전송한다. base-64로 인코딩된 비밀번호는 사실상 '비밀번호 그대로' 보내느 것과 다름 없다. 모든 HTTP 트랜잭션을 SSL 암호화 채널을 통해 보내거나, 보안이 더 강화된 다이제스트 인증같은 프로토콜을 사용하는 것이 좋다.
  2. 보안 비밀번호가 디코딩하기에 더 복잡한 방식으로 인코딩되어 있어도 여전히 제3자는 읽기 힘든 사용자 이름과 비밀번호를 캡쳐하여 그대로 원 서버에 보내 인증에 성공하고 서버에 접근할 수 있다. 기본 인증은 이런 재전송 공격을 예방하지 않는다.
  3. 기본 인증이 보안이 뚫려도 치명적이지 않은 애플리케이션에 사용된다 해도, 일반 사용자들의 웹사이트를 사용하는 행태에 비추어 굉장히 위험할 수 있다. 사용자가 모든 사이트에 같은 아이디와 비밀번호를 사용할 경우, 사용자 이름과 비밀번호 문자열을 그대로 캡쳐하여 다른 중요한 사이트에 접근할 수도 있다.
  4. 메시지 인증 헤더를 건드리진 않지만, 다른 부분을 수정하여 트랜잭션의 본래 의도를 바꿔버리는 프락시나 중개자가 중간에 개입한 경우, 기본 인증은 정상적인 동작을 보장하지 않는다.
  5. 기본 인증은 가짜 서버의 위장에 취약하다. 사용자가 검증된 서버에 연결되어 있다고 믿는다면, 공격자는 사용자의 정보를 저장하고 에러가 난 척을 할 수 있다.
* 기본 인증은 일반적 환경에서 개인화나 접근 제어에 편리하며, 유출되어도 치명적이지 않은 경우에는 유용하다. 우연이나 사고로 정보에 접근하는 것을 예방하는 데 사용한다.
* 기본 인증은 SSL 같은 암호화된 데이터 전송과 연계하여 사용할 수 있다.

## 12.4 추가 정보

* [HTTP 인증 - MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/Authentication)