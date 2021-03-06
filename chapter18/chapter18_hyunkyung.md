# 18장 웹 호스팅
- 조직이 가지고 있는 다양한 종류의 리소스를 웹 사이트에 편하게 배포하거나, 적절한 가격에 좋은 성능을 가진 웹서버에 배치하는 것은 매우 중요
- 콘텐츠 리소스를 저장, 중개, 관리하는 일을 통틀어 웹 호스팅이라 함
## 18.1 호스팅 서비스
- 웹이 대세가 되면서 한 회사가 서비스를 위한 모든 하드웨어, 네트워크를 관리할 여력이 되지 않았음
- 전문적으로 관리해주는 웹 호스팅 서비스를 제공하는 여러 신사업이 만들어짐
- 물리적인 장비 관리(공간, 냉난방, 연결)에서부터 고객이 직접 콘텐츠를 제공할 수 있는 총체적인 웹 호스팅까지 다양한 종류의 서비스들이 있음

## 18.2 가상 호스팅
- 가상 호스팅: 많은 웹 호스팅 업자는 컴퓨터 한 대를 여러 고객이 공유하게 해서 저렴한 웹 호스팅 서비스를 제공
- 최종 사용자의 관점에서 이는 전용 호스팅에서 받는 서비스와 구분할 수 없어야 함
- 가상 호스팅은 비용, 공간, 관리에 이점이 있으므로, 가상 호스팅을 제공하는 업체는 한 서버에 수십, 수백, 수천 개의 웹 사이트를 호스팅하려고 함
- 호스팅 업체는 서버 더미(서버팜)을 만들고 서버 팜에 부하를 분산할 수 있음
- 팜에 있는 각 서버는 다른 서버를 복제한 것이며, 수많은 가상 웹 사이트를 호스팅하고 있기 때문에 관리자는 훨씬 편해짐
### 18.2.1 호스트 정보가 없는 가상 서버 요청
- HTTP/1.0의 결함: 공용 웹 서버가 호스팅하고 있는 가상 웹 사이트에 누가 접근하고 있는지 식별하는 기능을 제공하지 않음
- http://www.joes-hardware.com과 www.marys-antiques.com이 같은 서버를 공유하고 있다고 해보자
- 클라이언트 A가 http://www.joes-hardware.com/index.html에 접속하려고 하면, 'GET /index.html' 요청이 공용 웹 서버에 전송됨
- 클라이언트 B가 www.marys-antiques.com/index.html에 접속하려고 하면, 위와 같은 "GET /index.html" 요청이 joes-hardware.com과 공유하고 
있는 공용 웹서버에 전송됨
- 웹 서버는 사용자가 어떤 웹 사이트로 접근하려고 하는지 아는데 필요한 정보가 충분하지 않음(두 요청이 똑같이 생김)
### 18.2.2 가상 호스팅 동작하게 하기
- 차선책: 모든 HTTP 요청 메시지에 경로 컴포넌트만 보내는 것이 아니라 완전한 URL도 보내게 함
- URL 경로를 통한 가상 호스팅: 서버가 어떤 사이트를 요청하는 것인지 알 수 있게 URL에 특별한 경로 컴포넌트 추가
   - 위의 예시에 나온 두 사이트는 아래와 같이 해결해볼 수 있음
    - http://www.joes-hardware.com/joe/index.html
    - www.marys-antiques.com/marys/index.html 
  - 혼선을 주고 좋지 않은 방법이라 거의 사용하지 않음
- 포트번호를 통한 가상 호스팅
  - 각 사이트에 다른 포트번호를 할당하여, 분리된 웹 서버의 인스턴스가 요청을 처리
  - 사용자는 URL에 비표준 포트를 쓰지 않고서도 리소스를 찾길 원하는 문제
- IP 주소를 통한 가상 호스팅
  - 각 가상 사이트에 별도의 IP 주소를 할당하고 모든 IP 주소를 장비 하나에 연결
  - 어떻게 동작하는지
    - 클라이언트 A는 http://www.joes-hardware.com/index.html를 요청
    - 클라이언트 A는 www.joes-hardware.com/index.html의 IP 주소를 요청해 209.172.34.3을 얻음
    - 클라이언트 209.172.34.3에 있는 공용 웹 서버에 TCP 커넥션을 맺음
    - 클라이언트는 'GET index.html' 요청을 보냄
    - 웹 서버가 응답을 전송하기에 앞서, 실제 목적지 IP 주소(209.172.34.3)을 기록하고, 이것이 죠의 웹사이트에 대한 가상 IP 주소라는 것을 판단하고, /joe
  하위 디렉터리에서 요청을 처리함, /joe/index.html 페이지를 반환
  - 가상 IP 호스팅은 잘 동작하지만, 규모가 아주 큰 호스팅 업자에게는 어려운 문제를 안겨줌
    - 일반적으로 컴퓨터 시스템이 연결할 수 있는 장비의 IP 개수에는 제한이 있음
    - IP 주소는 희소 상품, 충분한 주소를 얻지 못할 수도
    - IP 주소가 부족하면 호스팅 업자가 용량을 늘리려고 서버를 복제하면서 문제가 심각해짐 -> 부하 균형의 구조상, 각 복제된 서버에 IP 주소를 부여해야 하므로 IP
    주소는 복제 서버의 개수만큼 더필요해짐
- Host 헤더를 통한 가상 호스팅
  - HTTP/1.1의 공식 버전은 Host 요청 헤더를 정의함
### 18.2.3 HTTP/1.1 Host 헤더
- 문법과 사용 방법
  - Host 헤더에는 원본 URL에 있는 요청 리소스에 대한 인터넷 호스트와 포트번호를 기술
  - Host = "Host" ":" 호스트[ ":"포트]
  - Host 헤더에 포트가 기술되어 있지 않으면, 해당 스킴의 기본 포트를 사용함
  - URL에 IP 주소가 있으면, Host 헤더는 같은 주소를 포함해야 함
  - URL에 호스트 명이 기술되어 있으면, Host 헤더는 같은 호스트 명을 포함해야 함
  - URL에 호스트 명이 기술되어 있으면, Host 헤더는 URL의 호스트 명이 가리키는 IP 주소를 포함해서는 안됨, 여러 개의 가상 사이트를 한 개의 IP 주소에 연결한
  가상 호스트 서버에서 문제가 될 수 있기 때문
  - 클라이언트가 특정 프락시 서버를 사용한다면, Host 헤더에 프락시 서버가 아닌 원 서버의 호스트 명과 포트를 기술해야 함
  - 웹 클라이언트는 모든 요청 메시지에 Host 헤더를 기술해야 함
  - 웹 프락시는 요청을 전달하기 전에 요청 메시지에 Host 헤더를 추가해야 함
  - 서버는 Host 헤더 필드가 없다면 400 코드로 응답
  - Host 헤더 해석하기
    - HTTP 요청 메시지에 전체 URL에 기술되어 있으면, HOST 헤더에 있는 값은 무시하고 URL 사용
    - HTTP 요청 메시지에 있는 URL에 호스트 명이 기술되어 있지 않고 요청에 Host 헤더가 있으면, 호스트 명과 포트를 Host 헤더에서 가져옴
    - 1단계나 2단계에서 호스트를 결정할 수 없으면 400 응답 반환
  - Host 헤더와 프락시
    - 프락시를 Host 헤더에 담아 전송할 수 있으므로 Host에 프락시 서버 정보 담지말것
  
## 18.3 안정적인 웹 사이트 만들기증
- 서버에 문제가 생기는 상황들 
  - 서버 다운
  - 트래픽 폭증
  - 네트워크 장애나 손실
  
### 18.3.1 미러링 된 서버 팜
- 서버 팜은 서로 대신할 수 있고 식별할 수 있게 설정된 웹 서버들의 집합
- 서버 팜의 서버에 있는 콘텐츠들은 한 곳에 문제가 생기면 다른 한 곳에서 대신 전달할 수 있게 미러링 할 수 있음
- 미러링 된 서버는 계층적인 관계에 있음
  - 한 서버는 '콘텐츠의 원본 제작자' 같이 행동 -> 마스터 원 서버
  - 마스터 원 서버로부터 콘텐츠를 받은 미러링 된 서버 -> 복제 원 서버
- 서버 팜에 배포하는 간단한 방법 하나는, 네트워크 스위치를 사용해서 서버에 분산 요청을 보내는 것
- 서버에 호스팅 되고 있는 각 웹사이트의 IP 주소는 스위치의 IP 주소가 됨
- HTTP 리다이렉션: 콘텐츠에 대한 URL은 마스터 서버의 IP를 가리키고, 마스터 서버는 요청을 받는 즉시 복제 서버로 리다이렉트 시킴
- DNS 리다이렉션: 콘텐츠의 URL은 네 개의 IP 주소를 가리킬 수 있고, DNS 서버는 클라이언트에게 전송할 IP 주소를 선택할 수 있음
### 18.3.2 콘텐츠 분산 네트워크
- 콘텐츠 분산 네트워크(CDN)는 특정 콘텐츠의 분산을 목적으로 하는 단순한 네트워크
- 네트워크의 노드는 서버, 대리 서버, 혹은 프락시 서버가 될 수 있음 
### 18.3.3 CDN의 대리 캐시
- 대리 캐시는 복제 원 서버를 대신해 사용될 수 있음
- 리버스 프락시라고도 불리는 대리 서버는 미러링 된 웹 서버처럼 콘텐츠에 대한 요청을 받음
- 대리 서버와 미러링 된 서버의 차이점은, 대리 서버는 보통 수요에 따라서 동작한다는 것 -> 클라이언트가 요청하는 콘텐츠만 저장할 뿐
### 18.3.4 CDN의 프락시 캐시
- 전통적인 프락시 캐시는 어떤 웹 서버 요청이든지 받을 수 있음
- 하지만 대리 서버를 사용하면, 프락시 캐시의 콘텐츠는 요청이 있을 때만 저장될 것이고 원본 서버 콘텐츠를 정확히 복제한다는 보장이 없음





      




