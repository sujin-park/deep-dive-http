
## 2장 URL과 리소스

### URL (Uniform Rsc Locator)
: 인터넷의 리소스를 가리키는 표준이름 => 위치/접근정보 제시

+ 구성
  - 스킴://서버위치/리소스경로
  - ex)
    
    |프로토콜| URL|
    |---|---|
    |HTTP | http://www.test.kr/board/sub01 |
    |Email | mailto:nobody@bhouse.kr |
    |FTP | ftp://some.ftp.com:2121/user_list.xlsx |
    |RTSP | rtsp://www.kbss.com:554/live/video |

+ 문법
  - <스킴>://<사용자 이름>:<비밀번호>@<호스트>:<포트>/<경로>;<파라미터>?<쿼리>#<프래그먼트>
  - URL 컴포넌트 (9개)
  
    |명칭|상세|기타|
    |---|---|---|
    |스킴 | 서버 접근 위한 프로토콜 | 시작문자:영문자 <br> 구분문자:URL의 첫번째 콜론 <br>대소문자 구별X|
    |사용자명| 리소스 접근 위한 사용자명 | 기본값: anonymous |
    |패스워드 | 사용자의 비밀번호 | 기본값: 브라우저 따라 다름 |
    |호스트 | 서버의 호스트명이나 IP주소| www.test.co.kr <br> 161.28.111.3 |
    |포트 | 호스팅 서버가 열어놓은 포트 번호| 프로토콜 별 기본값 <br> HTTP: 80, HTTPS: 443, FTP: 21|
    |경로 | 슬래시(/)로 구분된 리소스의 서버 상 위치| 경로조각마다 자체 파라미터 사용 가능|
    |파라미터 | 특정 스킴에서 이용되는 입력 파라미터 | 구조:이름-값 쌍 리스트 <br> 구분문자:세미콜론 <br> www.test.co.kr/board;cate=01/list.html;dark=true;sort=date|
    |쿼리 | 스킴에서 어플리케이션(DB, 게이트웨이 등)으로 파라미터 전달 | 구조: URL 끝에 ? 달고 입력 (자유 포맷) <br> www.test.co.kr/search.do?keyword=gain&sort=rate|
    |프래그먼트 | 리소스 조각/일부분 지정 <br> 클라이언트에서만 이용 (서버에 전달 X) | 구조: URL 끝에 # 달고 입력 <br> www.test.co.kr/about.do#contact|

### 단축 URL
+ URL 경로
  - 절대 URL: 리소스 접근에 필요한 컴포넌트 정보 충분
  - 상대 URL 
    * 스킴 & 호스트 생략 
    * 리소스 base 태그에 명시된 기저URL 이용해 절대 URL로 변환
    * 리소스의 기저URL 상속해 절대 URL로 변환
    * 리소스 (http://wwww.test.co.kr/list.do) + 상대URL (./item1.do)  
      => 절대URL (http://wwww.test.co.kr/item1.do) 
+ URL 확장 
  - 호스트 명 확장: google -> wwww.google.com
  - 히스토리 확장: http://wwww.face -> (방문기록 참조) -> http://wwww.facebook.com 

### URL 인코딩 규칙
+ URL 문자집합
  - 안전한 전송 + 가독성 보장을 위해 이용 가능 문자 제한
  - 이스케이프 기능: 제한된 문자 전송 후 재인코딩 
+ URL 인코딩
  : 안전하지 않은 문자 => % + ASCII 코드(두 자리 16진수) => 이스케이프 문자

  |문자	| ASCII 코드 | 예시|
  |---|---|---|
  |빈 문자	| 32(0x20) |	http://www.test.co.kr/more%20about.html |
  |%	| 37(0x25)	| http://www.test.co.kr/50%25sale.html |
  |~	| 126(0x7E) |	http://www.test.co.kr/%7EKim |
  |+ | 43(0x2B)| %2B |
  |/ | 47(0x2F)| %2F |
  |= | 61(0x3D)| %3D |

+ 문자 제한
  : URL에서 선점하거나 게이트웨이-프로토콜 혼동 문제 등으로 제한된 문자 존재

  |문자	| 선점/제한|
  |---|---|
  |% | 이스케이프 토큰 |
  |/ | 경로 컴포넌트 구분자 |
  |. | 경로 컴포넌트: 현위치 |
  |.. | 경로 컴포넌트: 부모 위치 |
  |? | 질의 구분자 |
  |# | 프래그먼트 구분자 |
  |; | 파라미터 구분자 |
  |: | 스킴, 사용자명/비밀번호, 호스트/포트 구분자 |
  |$ , + | 선점 |
  |@&= |특정 스킴에서 이용 |
  |{ } | \ ・~ [ ] ' |게이트웨이 등의 전송 에이전트에서 불안전하게 다루므로 제한|
  |< > " | 안전하지 않음. 인코딩 필수|
  | 0x00-0x1F, 0x7F| 인쇄 가능한 US-ASCII 문자 아니므로 제한|
  | > 0x7F| 7비트 US-ASCII 문자 아니므로 제한|

### URI 스킴 목록

W3C의 URI 스킴 목록
http://www.w3c.org/Addressing/schemes.html

IANA의　URI 스킴 목록
http://www.iana.org/assignments/uri-schemes

|스킴|상세|형식|예시|
|---|---|---|---|
|http | 하이퍼텍스트 전송 프로토콜 | http://<호스트>:<포트>/<경로>?<질의>#<프래그먼트> | |
|https | HTTP 커넥션의 양 끝단에 SSL(보안소켓계층) 사용해 암호화| https://<호스트>:<포트>/<경로>?<질의>#<프래그먼트> | |
|mailto | | mailto:<RFC-822-addr-spec> | mailto:whois@goo.gle.co.kr|
|ftp | 파일전송 프로토콜 <br> 기능:파일 업로드/다운로드/목록조회| ftp://<사용자 이름>:<비밀번호>@<호스트>:<포트>/<경로>;<파라미터>| |
|rtsp, rtspu | 실시간 스트리밍 프로토콜의 미디어 리소스 식별자 <br> rtspu -> UDP 이용| rtsp://<사용자 이름>:<비밀번호>@<호스트>:<포트>/<경로> <br> rtspu://<사용자 이름>:<비밀번호>@<호스트>:<포트>/<경로>| |
|file | 호스트 기기에서 바로 접근 가능한 파일표시 <br> 기본 호스트: 로컬 | file://<호스트>/<경로>| |
|news | 특정 문서/뉴스그룹 접근에 이용 <br> 리소스 위치 정보 미포함 => 뉴스정보 서버 지정 필요| news:\<newsgroup\> <br> news:\<news-article-id\> | news:rec.arts.samtoban|
|telnet | 대화형 서비스 접근에 이용 | telnet://<사용자 이름>:<비밀번호>@<호스트>:<포트>| telnet://user1:pass1@www.test.com:8080/ |


### URN(Uniform Resource Names)
: 영구적이며 소장위치에 관계없이 정보자원을 식별할 수 있는 식별자

+ 기능적 요건 (RFC1737)
  - 단순한 위치만을 나타내지 않는 범세계적인 이름이어야 한다. 
  - 똑같은 URN이 서로 다른 정 보자원에 할당되지 않도록 고유해야 한다. 
  - URN은 영구적으로 지속되어 평생 하나의 정보자원에 대한 참조로 사용된다. 
  - URN은 네트워크에서 이용가능한 모든 정보자원에 할당되어야 한다. 
  - 스키마는 모든 기존의 네이밍 시스템을 지원해야 한다. 
  - URN의 모든 스키마는 향후 확장성을 가져야 한다. 
  - 조건을 결정하는 것은 이름을 부여하는 권한의 책임으로서 독립적이다. 
  - URN은 변환이 가능해야 한다.
+ 구문
  - urn:<NID>:<NSS>
  - urn:<네임스페이스식별자 > :<네임스페이스상세문자열 > 
+ 문자제한
  - 예약문자 = "%" | "/" | "?" | "#"
  - 기타문자 = "(" | ")" | "+" | "," | "-" | "." | ":" | "=" | "@" | ";" | "$" | "_" | "!" | "*" | "'"
+ 활용사례
  - DOI (디지털 객체 식별자)  
    : 1997년 미국출판협 회(AAP) 주도로 과학·기술·의학분야 전자저널(E-Journal)의 식별과 유통을 위해 고안된 URN 시스템 

    * IDF (국제 DOI재단): DOI 체계를 확립/관장/정책수립, 시스템 서비스 제공자 지정 
    * RA (DOI 등록관리기관): 신청기관에게 DOI 등록 시 필요한 DOI 접두부를 부여 
    * [한국DOI센터](https://www.doi.or.kr/wordpress/about_doi/)
    * [해시넷위키](http://wiki.hash.kr/index.php/URN#cite_note-1)
