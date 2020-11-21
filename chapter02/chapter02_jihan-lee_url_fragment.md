# URL 프래그먼트 / HASH

>HTTP 완벽가이드 2장 2.2.7 프래그먼트를 공부하며 정리

## 프래그먼트

### 정의

```
http://www.google.com/index.html#image
```

프래그먼트는 리소스 자체의 다른 부분을 가리키는 앵커이다.

앵커는 리소스내에서 **북마크**의 한 종류를 나타내며 브라우저에게 "북마크된" 지점에 위치한 컨텐츠를

보여주기 위한 방법을 나타낸다. HTML 문서 상에서 브라우저는 **앵커가 정의된 지점으로 스크롤 된다.**

### 실습

![image0](https://user-images.githubusercontent.com/39042837/99150986-6c346b00-26db-11eb-871d-a30173070d3b.png)
위의 프래그먼트 설명이 되어있는 MDN 사이트 URL로 접속한 상태이다.

URL에 **#프래그먼트** 를 추가하여 하단의 프래그먼트 설명 위치로 바로 스크롤 할 수 있다.

 

```
https://developer.mozilla.org/ko/docs/.../Identifying_resources_on_the_Web#프래그먼트
```

![image1](https://user-images.githubusercontent.com/39042837/99150987-6dfe2e80-26db-11eb-927c-61ba093b089b.png)

프래그먼트 설명 위치로 스크롤 된 것을 확인할 수 있다.

**#프래그먼트** 위치는 어디서 알 수 있을까?

![image2](https://user-images.githubusercontent.com/39042837/99150963-3e4f2680-26db-11eb-81a0-2a863bfbde50.png)

웹 페이지의 HTML 문서를 확인하면 활성화된 코드 라인에 **id="프래그먼트"** 값이 있다.

![image3](https://user-images.githubusercontent.com/39042837/99150943-0fd14b80-26db-11eb-84ae-36922d04fbed.png)

**#포트** 프래그먼트를 추가하면 포트 리소스의 위치로 스크롤 된다.

또한, window.location.hash 를 사용하여 프래그먼트 값을 변경할 수 있다.

```javascript
window.location.hash = '쿼리'
```

![image4](https://user-images.githubusercontent.com/39042837/99150900-e1ec0700-26da-11eb-832e-67caf6773212.png)

프래그먼트 **#쿼리로** 변경되었다.

---

## HASH



#### 👉 **프래그먼트를 어디서 사용할까? 사용해 본적이 없는데?** ~~응 아니야 ✋~~
   
   

현재 Vue.js 프론트엔드 프레임워크를 사용하여 웹 개발을 하고 있다.

Vue router에는 두 가지 모드가 있는데 **hash** 와 **history** 이며, **기본값은 hash 모드이다.**

(router는 URL 네비게이션 역할을 합니다.)

Vue 애플리케이션을 띄우면 아래와 같이 기본 URL 뒤에 /#/ 해시가 붙는다.

```jsx
http://localhost:8080/#/
```
   
   

해시태그니까 해시모드 인가보지 ? →  끝 🛌

프래그먼트를 한번도 본적이 없다! 라고 생각한 저는 반성합니다...

늘 보던 hash 모드의 **#** 이 바로 프래그먼트를 사용한 것 이다.

---
   
#### 👉 **왜 hash 모드를 사용하게 된 것 일까?**

라우팅 방식에 차이가 있다.

- **AJAX 방식**

    동일한 URL 내에서 페이지 갱신(새로고침) 없이 원하는 데이터를 가져오는 장점이 있지만

    **URL을 변경시키지 않으므로**, 브라우저의 뒤로가기 앞으로가기 등 **history 관리가 동작**하지 않는다.



- **hash 방식**

    URL이 동일한 상태에서 **hash가 변경되면 브라우저는 서버에 어떠한 요청도 하지 않는다.**

    따라서, 페이지가 갱신(새로고침)되지 않는다.

    hash 방식은 서버에 새로운 요청을 보내지 않지만, hash 값을 **브라우저 history에 항목을 추가하므로**

    **history 관리가 가능**하다.

```html
<a href="#/notice">...</a>
<a href="#/mail">...</a>
<a href="#/reservation">...</a>
```

Vue router hash 모드는 위의 네비게이션 링크를 클릭했을 때,

```javascript
http://localhost:8080/#/notice
```

동일한 URL 내에 hash가 변경되어서, 서버에 요청을 하지 않는다. (페이지 갱신 X)

브라우저의 **hashchange** 이벤트가 **변경된 hash를 감지**해서 **필요한 데이터 요청**만 일어난다.

---

**HTML5 History API인 pushState 와 popState** 방식이 지원되면서, hash(#)를 쓰지 않고도 history 관리를 할 수 있다.

![image5](https://user-images.githubusercontent.com/39042837/99150828-90437c80-26da-11eb-9825-1a218077661e.png)

Vue router 공식 문서에서도 history 모드는 history.pushState API를 활용한 방식이라고 소개한다.

---

## 참고 URL

[https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/Identifying_resources_on_the_Web](https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/Identifying_resources_on_the_Web)

[https://poiemaweb.com/js-spa#1-spa-single-page-application](https://poiemaweb.com/js-spa#1-spa-single-page-application)