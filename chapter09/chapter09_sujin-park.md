# 9장 웹 호스팅

웹 로봇은 `사람과의 상호작용 없이 연속된 웹 트랜잭션들을 자동으로 수행하는 소프트웨어 프로그램`입니다.

많은 로봇이 웹 사이트를 떠돌아다니면서, 콘텐츠를 가져오고, 하이퍼링크를 따라가고,
그들이 발견한 데이터를 처리합니다. 이러한 종류의 로봇들은 크롤러, 스파이더, 웜, 봇 등으로 불립니다.

## 9.1 크롤러와 크롤링

`웹 크롤러`란

웹 페이지를 한 개 가져오고, 그 다음 그 페이지가 가리키는 모든 웹 페이지를 가져오고, 다시 그 페이지들이 가리키는 `모든 웹 페이지들을 가져오는 일을 재귀적으로 반복하는 방식으로 웹을 순회하는 로봇`입니다.

인터넷 검색엔진은 웹을 돌아다니면서 그들이 만나는 모든 문서를 끌어오기 위해 크롤러를 사용합니다.

### 9.1.1 어디에서 시작하는가: '루트 집합'

`루트 집합`

크롤러가 방문을 시작하는 URL들의 초기 집합. 루트 집합을 고를 때, 모든 링크를 크롤링하면 결과적으로 관심 있는 웹페이지들의 대부분을 가져오게 될 수 있도록 충분히 다른 장소에서 URL을 선택해야 합니다.

`좋은 루트 집합`

크고 인기 있는 웹 사이트, 새로 생성된 페이지의 목록, 자주 링크되지 않는 잘 알려져 있지 않은 페이지들의 목록으로 구성되어 있습니다. 사용자들이 루트 집합에 새 페이지나 잘 알려지지 않은 페이지들을 추가하는 기능을 제공하기도 합니다.

### 9.1.2 링크 추출과 상대 링크 정상화

링크 추출
- 크롤러는 검색한 각 페이지 안에 들어있는 URL 링크들을 파싱해서 크롤링할 페이지들의 목록에 추가합니다.

상대 링크 정상화
- 크롤러들은 간단한 HTML 파싱을 해서 이들 링크들을 추출하고 상대 링크를 절대 링크로 변환할 필요가 있습니다.

### 9.1.3 순환 피하기

로봇이 웹을 크롤링 할 때, 루프나 순환에 빠지지 않도록 매우 조심해야 합니다.

로봇들은 순환을 피하기 위해 어디를 방문했는지 알아야 합니다. 순환은 로봇을 함정에 빠뜨려서 멈추게 하거나 진행을 느려지게 합니다.

### 9.1.4 루프와 중복

**순환이 크롤러에게 해로운 이유**

- 순환은 크롤러를 루프에 빠뜨려서 같은 페이지를 반복해서 가져오는데 모든 시간을 허비할 수 있고, 네트워크 대역폭을 다 차지해서 어떤 페이지도 가져올 수 없게 되어버릴 수 있습니다.
- 웹 서버에 부담을 줄 수 있습니다.
- 크롤러는 많은 수의 중복된 페이지들을 가져오게 됩니다. 크롤러의 애플리케이션은 자신을 쓸모없게 만드는 중복된 콘텐츠로 넘쳐나게 될 것입니다.

### 9.1.5 빵 부스러기의 흔적

대규모 웹 크롤러가 그들이 방문한 곳을 관리하기 위해 사용하는 유용한 기법

`트리와 해시 테이블`

방문한 URL을 추적하기 위해 검색 트리나 해시 테이블을 사용했을 수도 있습니다.

`느슨한 존재 비트맵`

존재 비트맵같은 느슨한 자료 구조를 사용하여 만약 존재 비트가 이미 존재한다면, 크롤러는 그 URL을 이미 크롤링 되었다고 간주합니다.

`체크포인트`

로봇 프로그램이 갑작스럽게 중단될 경우를 대비해, 방문한 URL의 목록이 디스크에 저장되었는지 확인합니다.

`파티셔닝`

한 대의 컴퓨터에서 하나의 로봇이 크롤링을 완수하는 것은 불가능해졌습니다. 몇몇 대규모 웹 로봇은, 분리되어 서로 도와 웹을 크롤링합니다.

### 9.1.6 별칭과 로봇 순환

URL이 별칭을 가지면 어떤 페이지를 이전에 방문했었는지 아는게 어려울 수도 있습니다. URL이 다르더라도 같은 리소스를 가리키고 있을 수도 있습니다.

| 첫 번째 URL | 두 번째 URL | 같은 URL을 가리키는 이유 |
|---|:---:|:---:|
| `http://www.foo.com/bar.html` | http://www.foo.com:80/bar.html | 기본 포트가 80번일 때 
| `http://www.foo.com/~fred` | http://www.foo.com/%7Ffred | %7F이 ~과 같을 때
| `http://www.foo.com/x.html#early` | http://www.foo.com/x.html#middle | 태그에 따라 페이지가 바뀌지 않을 때
| `http://www.foo.com/readme.htm` | http://www.foo.com/README.HTM | 서버가 대소문자를 구분하지 않을 때
| `http://www.foo.com/` | http://www.foo.com/index.html | 기본 페이지가 index.html 일 때
| `http://www.foo.com/index.html` | http://209.231.87.45/index.html | www.foo.com 이 아이피 주소를 가질 때

### 9.1.7 URL 정규화하기

로봇은 다음과 같은 방식으로 모든 URL을 정규화된 형식으로 변환할 수 있습니다.

1. 포트 번호가 명시되지 않았다면, 호스트 명에 ':80"을 추가
2. 모든 %xx 이스케이핑된 문자들을 대응되는 문자로 변환
3. '#' 태그들을 제거

그러나 위의 방법으로는 9.1.6에 있는 표 아래 3개의 항목에 대해서는 중복을 피할 수 없습니다.

### 9.1.8 파일 시스템 링크 순환

파일 시스템의 심벌릭 링크는 아무것도 존재하지 않으면서도 끝없이 깊어지는 디렉터리 계층을 만들 수 있습니다.


![Untitled-11658264-21c1-405c-8331-5177fb56ab3b](https://user-images.githubusercontent.com/29244798/108579748-b9277d80-736b-11eb-9436-68ddd84600a4.png)


**9-3a 의 파일시스템**

- GET http://www.foo.com/index.html
    - /index.html 을 가져와서, subdir/index.html 로 이어지는 링크 발견
- GET http://www.foo.com/subdir/index.html
    - subdir/index.html을 가져와서, subdir/logo.gif로 이어지는 링크 발견
- GET http://www.foo.com/subdir/logo.gif
    - subdir/logo.gif를 가져오고, 더 이상 링크가 없으므로 여기서 완료

**9-3b 의 파일시스템**

- GET http://www.foo.com/index.html
    - /index.html 을 가져와서, subdir/index.html 로 이어지는 링크 발견
- GET http://www.foo.com/subdir/index.html
    - subdir/index.html을 가져왔지만 같은 index.html 로 되돌아감
- GET http://www.foo.com/subdir/subdir/index.html
    - subdir/subdir/index.html을 가져옴
- GET http://www.foo.com/subdir/subdir/subdir/index.html
    - subdir/subdir/subdir/index.html을 가져옴

subdir/이 /로 링크되어 있어서 순환되지만 URL이 달라보이기 때문에 로봇은 URL만으로는 문서가 같다는 것을 모를 수 있습니다.



### 9.1.9 동적 가상 웹 공간

악의적으로 로봇들을 함정으로 빠뜨리기 위해 의도적으로 복합한 크롤러 루프를 만드는 일이 있습니다.

더 흔하게 일어날 수 있는 일은, 나쁜 뜻이 없음에도 자신도 모르게 심벌릭 링크나 동적 콘텐츠를 통한 크롤러 함정을 만들 수 있습니다.

### 9.1.10 루프와 중복 피하기

웹에서 로봇이 순환을 피하면서 올바르게 동작하기 위해 사용하는 기법은 아래와 같습니다.

`URL 정규화`

URL을 표준 형태로 변환함으로써, 같은 리소스를 가리키는 중복된 URL이 생기는 것을 일부 회피합니다.

`너비 우선 크롤링`

방문할 URL들을 웹 사이트들 전체에 걸쳐 너비 우선으로 스케줄링하면, 순환의 영향을 최소화 할 수 있습니다.

`스로틀링`

로봇이 웹 사이트에서 일정 시간 동안 가져올 수 있는 페이지의 숫자를 제한합니다.

`URL 크기 제한`

일정 길이를 넘는 URL의 크롤링은 거부할 수 있습니다. 순환으로 인해 URL이 계속해서 길어진다면, 길이 제한으로 인해 순환이 중단 될 것입니다.

**주의해야 할 점**

URL을 사용자 상태를 관리하기 위해 사용하는 요즘 이 기법을 적용하면 가져오지 못하는 콘텐츠들도 틀림없이 있습니다.

`URL/사이트 블랙리스트`

로봇 순환을 만들어 내거나 함정으로 알려진 사이트와 URL의 목록을 만들어 관리하고 피합니다.

`패턴 발견`

파일 시스템의 심벌릭 링크를 통한 순환과 그와 비슷한 오설정들은 일정 패턴을 따릅니다. 해당 패턴을 보고 반복된 구성요소를 갖고 있는 URL을 크롤링하는 것을 거절할 수 있습니다.

`콘텐츠 지문`

콘텐츠 지문을 사용하는 로봇들은 페이지의 콘텐츠에서 바이트를 얻어 체크섬을 계산합니다.

`사람의 모니터링`

로봇은 사람이 쉽게 로봇의 진행 상황을 모니터링해서 특이한 일이 발생하면 즉각 인지할 수 있게끔 진단과 로깅을 포함하도록 설계되어 있습니다.

## 9.2 로봇의 HTTP

### 9.2.1 요청 헤더 식별하기

로봇들이 HTTP를 최소한도로만 지원하려고 함에도 불구하고, 그들 대부분은 약간의 신원 식별 헤더를 구현하고 전송합니다.
로봇 구현자들은 로봇의 능력, 신원, 출신을 알려주는 기본적인 몇 가지 헤더를 사이트에게 보내주는 것이 좋습니다.

**권장되는 기본적인 신원 식별 헤더**

`User-Agent`

서버에게 요청을 만든 로봇의 이름

`From`

로봇의 사용자/관리자의 이메일 주소를 제공

`Accept`

서버에게 어떤 미디어 타입을 보내도 되는지 말해줌, 헤더는 로봇이 관심 있는 유형의 콘텐츠만 받게 될 것임을 확신하는데 도움을 제공

`Referer`

현재의 요청 URL을 포함한 문서의 URL을 제공

### 9.2.2 가상 호스팅

로봇 구현자들은 Host 헤더를 지원할 필요가 있습니다. 요청에 Host 헤더를 포함하지 않으면 로봇이 어떤 URL에 대해 잘못된 콘텐츠를 찾게 만듭니다.

**요청에 Host 헤더가 없어서 문제가 되는 경우**

- 두 개의 사이트를 운영하는 서버에 Host 헤더가 없이 요청 전송
- 크롤러는 다른 사이트의 콘텐츠를 요청하였을 때, 서버가 기본적으로 운영하고 있는 사이트의 콘텐츠를 응답으로 줄 수 있음
- 더욱 나쁜 것은, 크롤러는 자신이 요청한 사이트에서 온 콘텐츠라고 생각할 것이라는 점

### 9.2.3 조건부 요청

- 검색엔진 로봇과 같은 경우, 오직 변경되었을 때만 콘텐츠를 가져오도록 하는 것은 의미가 있습니다.
- 시간이나 엔터티 태그를 비교함으로써 그들이 받아간 마지막 버전 이후에 업데이트 된 것이 있는지 알아보는 조건부 HTTP 요청을 구현합니다.

### 9.2.4 응답 다루기

HTTP의 특정 몇몇 기능을 사용하는 로봇들이나, 웹 탐색이나 서버와의 상호작용을 더 잘 해보려고 하는 로봇들은 여러 종류의 HTTP 응답을 다룰 줄 알 필요가 있습니다.

`상태 코드`

모든 서버가 언제나 항상 적절한 에러 코드를 반환하지 않는다는 걸 알아두는게 중요하고, 최소한 일반적인 상태코드를 다룰 수 있어야합니다.

`엔터티`

HTTP 헤더에 임베딩된 정보를 따라 로봇들은 엔터티 자체의 정보를 찾을 수 있습니다.
`메타 http-equiv 태그와 같은 메타 HTML 태그는 리소스에 대해 콘텐츠 저자가 포함시킨 정보`입니다.

### 9.2.5 User-Agent 타겟팅

많은 웹 사이트들은 로봇들의 여러 기능을 지원할 수 있도록 브라우저의 종류를 감지하여 그에 맞게 콘텐츠를 최적화해야합니다.

풍부한 기능을 갖추지 못한 브라우저나 로봇 등 다양한 클라이언트에 잘 대응하는 유연한 페이지를 개발할 수 있습니다. 사이트 관리자들은 최소한 로봇이 그들의 사이트에 방문했다가 콘텐츠를 얻을 수 없어 당황하는 일이 없도록 대비해야 합니다.

## 9.3 부적절하게 동작하는 로봇들

`폭주하는 로봇`

만약 로봇에 에러가 있거나 순환에 빠진다면 웹 서버에 극심한 부하를 안겨줄 수 있으며, 서버에 과부하를 유발하여 다른 누구에게도 서비스를 못하게 만드는 일도 있을 수 있습니다.

`오래된 URL`

오래된 URL 을 방문할 수 있고, 만약에 웹 사이트가 이미 콘텐츠가 바뀌어있다면 로봇들은 존재하지 않는 URL에 대한 요청을 많이 보낼 수 있습니다.

`길고 잘못된 URL`

순환, 프로그래밍상의 오류로 인해 로봇은 웹 사이트에게 크고 의미 없는 URL을 요청할 수 있습니다.

`호기심이 지나친 로봇`

사적인 데이터에 대한 URL을 얻어 그 데이터를 인터넷 검색엔진이나 기타 애플리케이션을 통해 쉽게 접근할 수 있도록 만들 수도 있습니다. 
로봇이 명시적으로 하이퍼링크가 존재하지도 않는 문서들을 디렉터리의 콘텐츠를 가져오는 등 방법으로 긁어 올 때 가끔씩 일어날 수 있습니다.

**민감한 데이터를 무시하는 (검색 색인이나 아카이브에서 제거하는) 메커니즘**은 중요합니다.

`동적 게이트웨이 접근`

로봇은 게이트웨이 애플리케이션의 콘텐츠에 대한 URL로 요청을 할 수도 있습니다. 얻은 데이터는 아마도 특수 목적을 위한 것이고 처리 비용이 많이 들 것입니다.

## 9.4 로봇 차단하기

로봇의 동작을 제어할 수 있는 메커니즘을 제공하는 단순하고 자발적인 기법은 로봇의 접근을 제어하는 정보를 저장하는 파일의 이름을 따서 `robots.txt` 라고 불립니다.

`robots.txt`

- 서버의 문서 루트에 robots.txt 라고 이름 붙은 선택적인 파일을 제공
- 서버의 어떤 부분에 접근할 수 있는지에 대한 정보 존재
- 웹 사이트의 어떤 리소스에 접근하기 전에 우선 사이트의 robots.txt 를 요청

### 9.4.1 로봇 차단 표준

료봇 차단 표준은 세 가지 버전이 존재합니다.

| 버전 | 이름과 설명 | 날짜 |
|---|:---:|:---:|
| `0.0` | 로봇 배제 표준-Disallow 지시자를 지원하는 마틴 코스터의 오리지널 robots.txt 메커니즘 | 1994년 6월
| `1.0` | 웹 로봇 제어 방법-Allow 지시자의 지원이 추가된 마틴 코스터의 IETF 초안 | 1996년 11월
| `2.0` | 로봇 차단을 위한 확장 표준-정규식과 타이밍 정보를 포함한 숀 코너의 확장. 널리 지원되지 않음 | 1996년 11월

### 9.4.2 웹 사이트와 robots.txt 파일들

- 사이트 전체에서 robots.txt 파일은 단 하나만 존재
- 반드시 파일 시스템에 존재해야하는 이유는 없고, 게이트웨이 애플리케이션이 robots.txt를 동적으로 생성 가능
- 서브디렉터리에 설치할 수 있는 방법은 존재하지 않음

`robots.txt 가져오기`

로봇은 `HTTP GET 메서드`를 이용해 robots.txt 리소스를 가져오고, `존재한다면 text/plain 본문으로 반환`하고 `404 Not Found 상태 코드로 응답한다면 로봇의 접근을 제한하지 않는 것`으로 간주하고 아무 파일이나 요청하게 됩니다.

사이트 관리자가 로봇의 접근을 추적할 수 있도록 From 또는 User-Agent 헤더를 통해 정보를 넘깁니다. 아래는 웹 로봇이 보낼 수 있는 HTTP 크롤러 요청의 한 가지 예입니다.

```
GET /robots.txt HTTP/1.0
Host: www.joes-hardware.com
User-Agent: Slurp/2.0
Date: Wed Oct 3 20:22:48 EST 2001
```

`응답 코드`

로봇은 robots.txt 의 검색 결과에 따라 다르게 동작합니다.

- 서버가 성공으로 응답하면 로봇은 그 응답의 콘텐츠를 파싱하여 차단 규칙을 얻고, 사이트에서 콘텐츠를 얻을 때 규칙에 따라야 함
- 만약 존재하지 않는다면 차단 규칙이 존재하지 않는다고 가정하고 제약 없이 사이트에 접근
- 서버가 접근 제한(HTTP 401 or 403)으로 응답한다면 로봇은 그 사이트로의 접근이 완전히 제한되어 있다고 가정
- 요청 시도가 일시적으로 실패했다면(HTTP 503) 로봇은 사이트의 검색을 뒤로 미룸
- 서버 응답이 리다이렉션을 의미한다면 (HTTP 3XX) 로봇은 리소스가 발견될 때까지 리다이렉트를 따라감

### 9.4.3 robots.txt 파일 포맷

robots.txt 파일은 단순한 줄 기반 문법을 갖습니다. robots.txt 파일의 각 줄은 빈 줄, 주석 줄, 규칙 줄의 세 가지 종류가 있습니다.

규칙 줄은 HTTP 헤더처럼 생겼고 `(<필드>:<값>)` 패턴 매칭을 위해 사용됩니다.
```
User-Agent: slurp
User-Agent: webcrawler
Disallow: /private

User-Agent: *
Disallow:
```
- 위의 예는 robots.txt 파일이 Slurp 과 Webcrawler 로봇들이 하위 디렉터리 안에 있는 것을 제외한 어떤 파일이든 접근할 수 있도록 허용합니다.
- 다른 로봇들이 이 사이트의 그 무엇에도 접근할 수 없도록 막습니다.


robots.txt 이 줄들은 레코드로 구분됩니다. 차단 규칙의 집합을 기술하고 로봇별로 각각 다른 차단 규칙을 적용할 수 있습니다.

- 레코드는 규칙 줄들의 집합으로 되어있고, 빈 줄이나 파일 끝(end-of-file) 문자로 끝남
- User-Agent 줄로 시작하며 뒤이어 로봇들이 접근할 수 있는 URL들을 알려주는 Disallow, Allow 줄 작성

`User-Agent 줄`

```
User-Agent: <robot-name> 또는 User-Agent: *
```

- 로봇의 이름은 로봇의 HTTP GET 요청 안의 User-Agent 헤더를 통해 보내집니다.
- 로봇 이름은 대소문자를 구분하지 않는 부분 문자열과 맞춰보므로, `User-Agent: bot`은 Bot, Robot, Bottom-Feeder 에 매치됩니다.

`Disallow와 Allow 줄들`

- Disallow, Allow 줄은 User-Agent 줄들 바로 다음에 작성
- 특정 로봇에 어떤 URL 경로가 명시적으로 금지되어 있고 명시적으로 허용되는지 기술
- 반드시 요청하려고 하는 URL을 차단 레코드의 모든 Disallow와 Allow 규칙에 순서대로 맞게 보아야 함
    - 첫 번째로 맞은 것이 사용되며 만약 어떤 것도 맞지 않으면, URL은 허용됨

`Disallow와 Allow 접두 매칭`

- Disallow 나 Allow 규칙이 어떤 경로에 적용되려면, 그 경로의 시작부터 규칙 경로의 길이만큼 문자열이 규칙 경로와 같아야 함 (대소문자의 차이도 없이)
- User-Agent 줄과 달리 별표(*)는 특별한 의미를 갖지 않지만, 대신 빈 문자열을 이용해 모든 문자열에 매치되도록 할 수 있음
- 규칙 경로나 URL 경로의 임의의 `이스케이핑된` 문자들(%XX)은 비교 전에 원래대로 복원
    - 빗금(/)을 의미하는 %2F는 예외로, 반드시 그대로 매치되어야 함
- 만약 어떤 규칙 경로가 빈 문자열이라면, 그 규칙은 모든 URL 경로와 매치 됨

| 규칙 경로 | URL 경로 | 매치되는가? | 부연 설명
|---|:---:|:---:|:---:|
| `/tmp` | /tmp | O | 규칙경로 === URL 경로
| `/tmp` | /tmpfile.html | O | 규칙 경로가 URL 경로의 접두어
| `/tmp` | /tmp/a.html | O |규칙 경로가 URL 경로의 접두어
| `/tmp/` | /tmp | X |/tmp는 /tmp의 접두어가 아님
|  | README.TXT | O |빈 문자열은 모든 것에 매치 됨
| `/~fred/hi.html` | %7Efred/hi.html | O | %7E은 ~과 같은 것으로 취급됨
| `/%7Efred/hi.html` | /~fred/hi.html | O | %7E은 ~과 같은 것으로 취급됨
| `%7efred/hi.html` | /%7Efred/hi.html | O |이스케이핑된 문자는 대소문자를 구분하지 않음
| `/~fred/hi.html` | ~fred%2Fhi.html | X | %2F는 빗금(/)이 맞긴 하지만, 빗금은 특별히 정확하게 매치되어야 함


### 9.4.4 그 외에 알아둘 점

robots.txt 파일을 파싱할 때 지켜야 할 추가 규칙이 있습니다.

- robots.txt 파일은 명세가 발전하면서 User-Agent, Disallow, Allow 외의 다른 필드를 포함할 수 있음. 자신이 이해하지 못하는 필드는 무시
- 하위 호환성을 위해, 한 줄을 여러 줄로 나누어 적는 것은 허용되지 않음
- 주석은 파일 어디에서든 허용. 주석은 선택적인 공백 문자와 뒤이은 주석 문자(#)로 시작해서, 그 뒤에 줄바꿈 문자가 나오기 전까지 주석 내용
- 로봇 차단 표준 버전 0.0은 Allow 줄을 지원하지 않았고, 몇몇 로봇은 오직 0.0 버전의 명세만을 구현하여 Allow 줄을 무시함. 허용되는 URL도 탐색하지 않을 수 있음

### 9.4.5 robots.txt 캐싱과 만료

로봇이 매 파일 접근할 때마다 robots.txt 파일을 새로 가져온다면 서버의 부하를 줄 수 있습니다. 그래서 로봇은 주기적으로 robots.txt 를 가져와서 그 결과를 캐시해야 합니다.

robots.txt 파일의 캐싱을 제어하기 위해 HTTP 캐시 제어 메커니즘은 원 서버와 로봇 양쪽 모두에 의해 사용되어야 합니다.

### 9.4.7 HTML 로봇 제어 META 태그

robots.txt 파일은 사이트 관리자가 로봇들을 웹 사이트의 일부 혹은 전체에 접근할 수 없게 합니다. robots.txt 파일의 단점은 파일을 웹 사이트 관리자가 소유한다는 것입니다.

로봇 제어 HTML 태그에 따르는 로봇들은 문서를 가져올 수는 있지만, 로봇 차단 태그가 존재한다면 그들은 그 문서를 무시할 것이다.

`로봇 META 지시자`

로봇 META 지시자는 시간이 지나면서 검색엔진과 기능 집합을 확장함에 따라 새 지시자가 추가될 가능성이 높습니다.

가장 널리 쓰이는 로봇 META 지시자 두 가지는 아래와 같습니다.

`NOINDEX`

로봇에게 이 페이지를 처리하지 말고 무시하라고 함. (예: 이 페이지의 콘텐츠를 색인이나 데이터베이스에 포함시키지 말 것)
```
<META NAME="ROBOTS" CONTENT="NOINDEX">
```

`NOFOLLOW`

로봇에게 이 페이지가 링크한 페이지를 크롤링하지 말라고 함
```
<META NAME="ROBOTS" CONTENT="NOFOLLOW">
```

의미가 반대인 지시자와 다른 지시자들은 아래와 같습니다.

`INDEX`

로봇에게 이 페이지의 콘텐츠를 인덱싱해도 된다고 말해줌

`FOLLOW`

로봇에게 이 페이지가 링크한 페이지를 크롤링해도 된다고 말해줌

`NOARCHIVE`

로봇에게 이 페이지의 캐시를 위한 로컬 사본을 만들어서는 안 된다고 말해줌

`ALL`

INDEX, FOLLOW와 동일

`NONE`

NOINDEX, NOFOLLOW와 동일

- 로봇 META 태그는 다른 모든 HTML META 태그와 마찬가지로 반드시 HTML 페이지의 HEAD 섹션에 존재
- name, content 는 대소문자를 구분하지 않음

```
<html>
<head>
    <meta name="robots" content="noindex,nofollow">
    <title>...</title>
</head>
<body>
    ...
</body>
</html>
```

`검색엔진 META 태그`

많은 META 태그 중 DESCRIPTION, KEYWORDS META 태그는 콘텐츠의 색인을 만드는 검색엔진 로봇들에 대해 유용합니다.

| name= | content= | 설명 |
|---|:---:|:---:|
| `DESCRIPTION` | <텍스트> | 웹페이지의 짧은 요약을 정의. 많은 검색엔진이 그들의 웹페이지에 대해 기술하는 짧고 적절한 요약을 지정할 수 있도록 함
| `KEYWORDS` | <쉼표 목록> | 키워드 검색을 돕기 위한, 웹페이지를 기술하는 단어들의 쉼표로 구분되는 목록 `<meta name="keywords" content="antiques,mary,furniture,restoration"`
| `REVISIT-AFTER` | <숫자 days> | 이 페이지는 쉽게 변경될 것이기 때문에 지정된 만큼의 날짜가 지난 이후 다시 방문해야 한다고 지시 `<meta name="revisit-after" content="10 days">`

## 9.5 로봇 에티켓

웹 로봇 커뮤니티의 개척자인 마틴 코스터는 웹 로봇을 만드는 사람들을 위해 가이드라인 목록을 작성했습니다.
[로봇 제작자들을 위한 가이드라인](https://www.robotstxt.org/)

## 9.6 검색엔진

웹 로봇을 가장 광범위하게 사용하는 것은 `인터넷 검색엔진`입니다.

### 9.6.1 넓게 생각하라

웹이 초장기였을 때, 검색엔진들은 문서의 위치를 알아내는 것을 돕는 상대적으로 단순한 데이터베이스였습니다.
오늘날, 검색엔진은 인터넷 사용자들의 정보 찾기를 도와주는 검색엔진들이 필수가 되었고 수십억 개의 웹페이지들을 검색하기 위해 복잡한 크롤러를 사용해야 합니다.

### 9.6.2 현대적인 검색엔진의 아키텍처

오늘날 검색엔진들은 전 세계의 웹페이지들에 대해 `풀 텍스트 색인(full-text indexes)`라고 하는 복잡한 로컬 데이터베이스를 생성합니다.

- 검색엔진 크롤러들이 웹페이지들을 수집하여 풀 텍스트 색인에 추가
- 동시에 웹 검색 게이트웨이를 통해 풀 텍스트 색인에 대한 질의를 전송
- 크롤링을 한 번 하는데 걸리는 시간이 상당한 데 비해 웹 페이지들은 변화하기 때문에, 풀 텍스트 색인은 웹의 특정 순간에 대한 스냅샷에 불과함

### 9.6.3 풀 텍스트 색인

**풀 텍스트 색인**은 단어 하나를 입력받아 그 단어를 포함하고 있는 문서를 즉각 알려줄 수 있는 데이터베이스입니다. 색인이 생성된 후에는 검색할 필요가 없습니다.

### 9.6.4 질의 보내기

`사용자가 질의를 웹 검색엔진 게이트웨이로 보내는 방법은, HTML 폼을 사용자가 채워 넣고 브라우저가 폼을 HTTP GET, POST 요청을 이용해서` 게이트웨이로 보냅니다.

웹 서버는 질의를 받아서 검색 게이트웨이 애플리케이션에게 넘겨주면, 게이트웨이는 웹 서버에게 문서의 목록을 결과로 돌려주고, 웹 서버는 이 결과를 사용자를 위한 HTML 페이지로 변환합니다.

### 9.6.5 검색 결과를 정렬하고 보여주기

질의의 결과를 확인하기 위해 검색엔진이 색인을 한번 사용했다면, 게이트웨이 애플리케이션은 결과를 이용해 최종 사용자를 위한 결과 페이지를 즉석에서 만들어냅니다.

`관련도 랭킹`이란 검색 결과의 목록에 점수를 매기고 정렬하는 과정입니다. 이 과정을 더 잘 지원하기 위해, 많은 검색엔진이 웹을 크롤링하는 과정에서 수집된 통계 데이터를 실제로 사용합니다.

### 9.6.6 스푸핑

웹 사이트를 찾을 때 검색 결과의 순서는 굉장히 중요합니다. 검색 결과에서 더 높은 순위를 차지하고자 검색엔진과 자신의 사이트를 눈에 띄게 하려고 합니다.

많은 웹 마스터들은 수많은 키워드들을 나열한 가짜 페이지를 만들거나, 더 나아가서는 `검색 엔진의 관련도 알고리즘을 더 잘 속일 수 있는 특정 단어에 대한 가짜 페이지를 생성하는 게이트웨이 애플리케이션을 만들어 사용`합니다.