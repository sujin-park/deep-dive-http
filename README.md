# deep-dive-http (HTTP 완벽가이드 책 스터디 모임)

## Introduce

Http 완벽가이드 책을 마스터하는 스터디 모임

매주 동일한 챕터를 각자 정리하여 Pull Request 방식으로 공유 및 피드백 주고 받기

매주 일요일 오전 10시에 그 주의 공유자가 해당 챕터와 관련된 내용을 발표

**2020.11.08 시작!**

## How to

### Pull Request 기반으로 공유하기

1. GitHub ID 로 브랜치를 생성합니다.

```jsx
git switch -c [깃헙아이디]
```

2. 해당 챕터 내에 markdown 형식의 파일을 추가합니다.

```jsx
chapter01
└── chapter01_sujin-park.md
chapter02
└── chapter02_sujin-park.md
```

3. 해당 내용을 commit 및 push 를 하고 PR 을 올립니다. (main 으로 rebase 하여 PR 에는 해당 차수의 commit 만 올라오게끔 작업합니다.)
```
git rebase main
```

4. PR 올릴 때는 ```chapter``` 라벨을 추가합니다.

5. 서로 매주 일요일 스터디 모임 이전에 피드백을 주고 받으면서 승인 및 merge를 하여도 되고, 별도의 피드백을 주고 받지 않았다면 매주 일요일 스터디가 끝난 후에 일괄 merge를 합니다.

### 저장소를 Fork 하여 작업했을 때 Pull Request 이후 최신화 하기

원격 저장소를 upstream 이란 이름으로 최초 1회 등록합니다.
```
git remote add -t [깃헙아이디] upstream https://github.com/sujin-park/deep-dive-http.git
```

잘 등록되었는지 확인해봅니다.
```
git remote -v

origin	https://github.com/[깃헙아이디]/deep-dive-http.git (fetch)
origin	https://github.com/[깃헙아이디]/deep-dive-http.git (push)
upstream	https://github.com/sujin-park/deep-dive-http.git (fetch)
upstream	https://github.com/sujin-park/deep-dive-http.git (push)
````

Fork 한 저장소의 내용을 원격 저장소의 내용으로 최신화 해줍니다.
```
git fetch upstream main
git rebase upstream/main
```

### 이미지 파일을 md 파일에 포함하여 공유하고 싶을 때

1. 우선 Issues 탭에서 New Issue 를 클릭하여 텍스트 영역에 공유하고 싶은 파일을 드래그 합니다.
2. GItHub 서버에 업로드 된 파일 경로를 복사하여 사용합니다.

```jsx
![2020-10-20 17 00 20](https://user-images.githubusercontent.com/29244798/98436762-5d6d1780-2121-11eb-8f2f-202f03247b58.gif)
```

---
2020.11.22 업데이트됨
