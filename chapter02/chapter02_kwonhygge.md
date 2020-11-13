# HTTP 완벽 가이드 2장

## URI와 리소스

- URL은 인터넷의 리소스를 가리키는 표준 이름

## 2.1 인터넷의 리소스 탐색하기

URI = URL + URN

URN : 현재 그 리소스가 어디에 존재하든 상관없이 그 이름만으로 리소스를 식별

URL: 리소스가 어디 있는지 설명해서 리소스를 식별

[http://www.joes-hardware.com/seasonal/index-fall.html](http://www.joes-hardware.com/seasonal/index-fall.html) 이라는 URL을 불러오고 싶을 때

- URL의 첫부분인 http는 URL의 스킴 : 웹 클라이언트가 리소스에 어떻게 접근하는지 알려줌(URL이 HTTP 프로토콜을 사용한다)
- www.joes-hardware.com은 서버의 위치 : 웹 클라이언트가 리소스가 어디에 호스팅 되어있는지 알려주는 것
- seasonal/index-fall.html은 리소스의 경로: 서버에 존재하는 로컬 리소스들 중에서 요청받은 리소스가 무엇인지 알려줌

- HTTP 프로토콜이 아닌 다른 가용한 프로토콜
    - mailto:president@whitehouse.gov : 이메일 주소
    - [ftp://ftp.lots-o-books.com/pub/complete-price-list.xls](ftp://ftp.lots-o-books.com/pub/complete-price-list.xls) : FTP(File Transfer Protocol) 서버에 올라가 있는 파일
    - rtsp://www.joes-hardware.com:554/interview/cto_video : 스트리밍을 제공하기 위해 비디오 서버에 호스팅하고 있는 영화

처음부터 일관된 명명 방식이 있었던 것은 아님

### 2.1.1 URL이 있기 전 암흑의 시대

- 웹과 URL이 있기 전에 사람들은 네트워크상에 산재해 있는 데이터에 접근하기 위해 각자 다른분류 방식을 사용

URL이 있기 전 친구와 complete-price-list.xls라는 파일을 공유하려고 했다면

1. ftp.joes-hardware.com에 FTP로 접속해라
2. 익명 사용자로 로그인한 다음 비밀번호로 네 이름을 입력해라
3. pub 디렉토리로 이동해서 바이너리 형식으로 전환해라
4. 이제 complete-catalog.xls란 이름의 파일을 내려받아라

하지만 URL을 사용하면 "[ftp://ftp.lots-o-books.com/pub/complete-price-list.xls](ftp://ftp.lots-o-books.com/pub/complete-price-list.xls)를 열어봐" 라고 간단히

### 2.2 URL 문법

- URL 문법은 스킴에 따라서 달라지지만 형태와 문법 면에서 유사하다
- 대부분의 URL 스킴의 문법
    - <스킴>://<사용자이름>:<비밀번호>@<호스트>:<포트>/<경로>;<파라미터>?<질의>#<프래그먼트>