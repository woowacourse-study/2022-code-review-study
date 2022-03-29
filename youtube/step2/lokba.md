# Level1 Youtube Step2(담당 PR 번호들) - 록바

- 분석 담당 코드

  - 록바 [#138](https://github.com/woowacourse/javascript-youtube-classroom/pull/138)
  - 앨버 [#124](https://github.com/woowacourse/javascript-youtube-classroom/pull/124)
  - 태태 [#118](https://github.com/woowacourse/javascript-youtube-classroom/pull/118)
  - 결 [#115](https://github.com/woowacourse/javascript-youtube-classroom/pull/115)
  - 자스민 [#116](https://github.com/woowacourse/javascript-youtube-classroom/pull/116)
  - 동키콩 [#135](https://github.com/woowacourse/javascript-youtube-classroom/pull/135)
  - 도리 [#127](https://github.com/woowacourse/javascript-youtube-classroom/pull/127)
  - 소피아 [#136](https://github.com/woowacourse/javascript-youtube-classroom/pull/136)

<br>

## 피드백 정리

## 대분류(ex: 아키텍처, 함수/클래스, 컨벤션, DOM, 테스트 등)

### 1. 아키텍처

- [#135] [크루원 질문] 구조를 만들 때 어떻게 진행하는게 올바른 방법이라 생각하시는지 구조를 만들 때 중점 사항을 두시는 부분이 있으신지 궁금합니다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/135#pullrequestreview-915496528)

  - [리뷰어님 답변]

    ```
    세상에 완벽한 아키텍처는 존재하지 않습니다.
    또, 처음부터 좋은 아키텍처를 만들수도 없죠.
    첫 시작은 하신 것 처럼 필요로 인해 이렇게 나눠보시는 훈련을 하는거라 생각하시면 되겠습니다.

    그렇기에 "구조를 막 짜보고 부숴보라"고 권해드리는 것이고, 다른 코치님들도 동일한 답변을
    하실 것 같아요~

    그리고 가장 중요한 것은,
    아키텍처는 프로그래밍 개발 지식만으론 만들어지지 않는다는걸 이해하셔야합니다.
    오늘 만드셨던 "나만의 유튜브 강의실"의 경우 지금 만드신 아키텍처가 적합한지 다시 생각해볼 필요가 있어요.

    "나만의 유튜브 강의실"은 어떤 도메인이고, 특성을 갖고있을까요?
    그리고 함께 개발하는 인원은 몇명인가요? 개발 기간은 언제까지일까요?
    이런 것처럼 다양한 환경적 요인과 비즈니스를 성사시키기 위해 아키텍처라는 틀을 만들고
    협업/목표를 이루기 위한 수단을 만드는 거라 이해하시면 되겠습니다.
    ```

---

### 2. 클래스

#### 2.1 내장 메소드

- [#115] classList toggle을 적극 활용해라 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/115#discussion_r830433395)

  ```jsx
  //수정 전 코드
  if (this.willWatchVideoList.children.length !== 0) {
    this.element.classList.add("hide-element");
  } else if (this.willWatchVideoList.children.length === 0) {
    this.element.classList.remove("hide-element");
  }

  //수정 후 코드
  this.element.classList.toggle(
    "hide-element",
    this.willWatchVideoList.children.length !== 0
  );
  ```

<br>

- [#115] 배열을 spread하는 작업은 매우 비싼 작업이다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/115#discussion_r830436124)
  ```jsx
  //해당 코드
  [
    ...ControlVideo.getStorageWillWatchVideoList(),
    ...ControlVideo.getStorageWatchedVideoList(),
  ].length > MAX_SAVE_VIDEO_COUNT;
  ```
  - 배열을 spread하는 것은 배열의 각 요소를 한 번씩 순회하는 비싼 작업입니다.
    여기서는 두 배열의 length를 합하는 것으로 충분해 보이네요.

<br>

#### 2.2 static

- [#115] static 멤버만 존재하는 클래스가 클래스로서의 가치가 있을까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/115#discussion_r830430735)
  - 해당 클래스를 객체로 전환하는 것으로 충분하다.

---

### 3. 클린코드

#### 3-1. 네이밍

- [#116] show vs move 접두사 의미 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/116#discussion_r830470580)
  - css를 통해서 view를 제어하고 있기 때문에 show란 이름이 더 적절하다.
  - move란 이름은 해당페이지로 history.push되거나 link로 이동하는 느낌이다.

<br>

---

### 4. HTML

- [#127] input type="search" 속성 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/127#discussion_r830486205)

  - [[MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/search)] search 속성은 검색어를 입력할 수 있는 텍스트 필드를 정의한다. 검색 필드는 텍스트 필드와 기능적으로 완전히 똑같지만, 브라우저에 의해 다르게 표현될 수 있다.
  - search 필드에서는 반드시 name 속성을 설정해야 하며, name 속성이 설정되어 있지 않으면 서버로 제출되지 않는다. 가장 자주 사용되는 대표적인 name 속성값은 'q'이다.

    ```html
    <form action="/examples/media/action_target.php" method="get">
      검색 <input type="search" name="q" />
      <input type="submit" />
    </form>
    ```

<br>

---

### 5. 테스트

- [#118] cypress config로 baseUrl를 관리할 수 있다. -[바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/118#discussion_r830497307)
  ```jsx
  //cypress.json
  {
    "supportFile": false,
    "baseUrl": "https://nan-noo.github.io/javascript-youtube-classroom"
  }
  ```
  - cypress config 관련 링크 - [여기](https://docs.cypress.io/guides/references/configuration#cypress-json)

---

### 6. 기타

#### 6-1. 나는 생각하지 못한 생각

- [#118] 저장한 비디오를 localStorage에 담고 있는데, 이 경우 사용자에게 오래된 데이터를 보여줄 수 있다. 크루가 생각한 방식은, timestamp를 같이 저장해서 데이터를 가져올 때 일정 기간이 지난 데이터를 삭제하는 방식을 생각했다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/118#issue-1173685271)

  - 좋은 생각이다. 다른 프로그램과 서비스에서는 어떻게 캐시데이터를 관리하는지 찾아봐라.
  - 관련 키워드 - Cache Invalidation

<br>

- [#127] 아이템을 나열하는 리스트 UI에서 자주 사용하는 방식 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/127#discussion_r830480679)
  - 원래는 내부 스크롤 없이 리스트가 페이지에 쭉 나열되고, **스크롤로 내리면 메뉴 바가 상단에 붙는 UI를 많이 쓴다.**

<br>

#### 6-2. 좋은 말

- [#118] 주니어 때 리팩토링 관련 도서 한 권은 필수로 읽자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/118#pullrequestreview-915012855)

<br>

- [#136] 단순 구조 vs 확장성 구조 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/136#pullrequestreview-915020902)
  - 프로토타입이거나 프로덕션이라도 **변경 요청이 별로 없는 부분이라면 확장성을 포기하고 최대한 단순한 구조를 유지**할 것입니다.
  - 프로덕션 레벨이고 **변경이 잦은 부분이라면 확장성을 고려해서 복잡해지더라도 유연한 설계를 취할 것**입니다.

<br>

### 7. 내가 정리한 키워드

#### 7-1. Cache Invalidation

캐시 무효화(Invalidation)는 요청에 대해 캐시 데이터가 사용되어야 할 지 아닐지를 결정하고 항상 최신의 데이터를 반환하도록 하는 일련의 과정입니다.

일반적인 캐시 무효화 방법은 다음과 같습니다.

- **Expiration Time**
  캐시가 생성될 때 지정한 TTL에 의해 캐시의 수명이 결정되고 수명이 다한 캐시는 사용되지 않습니다. 가장 일반적인 캐시 무효화 전략입니다.
  TTL을 보수적으로 정했을 때, 해당 시간이 지나면 최신의 데이터가 반환되기 때문에 안전한 방법입니다. 캐시를 잘 관리할 자신이 없다면 TTL을 짧게 정하는 것이 좋습니다.

<br>

- **Freshness Caching Verification**
  별도의 검증 절차를 통해 캐시가 유효한지 확인하는 방법입니다.
  예를 들어 원본 데이터가 업데이트 된 시간과 캐시가 생성된 시간을 비교하여 캐시의 유효성을 판별합니다. 단점은 캐시가 사용될 때마다 추가적인 확인 절차를 수행하기 때문에 이에 따른 오버헤드가 발생합니다.

<br>

- **Active Application Invalidation**
  데이터가 수정되는 코드가 수행될 때마다 백엔드에서 연관된 캐시를 무력화하는 방법입니다. 캐시를 세밀하게 관리할 수 있기 때문에 이상적으로 설계되었을 때 가장 좋은 효율을 발휘합니다. 1번 방식이 수동적인 방법인데 비해 아주 능동적인 방법입니다. 별도의 코드를 통해 캐시를 컨트롤 하기 때문에 가장 에러가 발생하기 쉽고 관리가 까다롭습니다.

<br>

- 더 자세하게 알고 싶다면 [여기](https://toss.tech/article/smart-web-service-cache)를 참고하세요. - toss tech
