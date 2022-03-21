# LEVEL 1 나만의 유튜브 강의실 STEP 1 - 무비😎

## 분석 담당 코드

- 무비 [PR 104](https://github.com/woowacourse/javascript-youtube-classroom/pull/104) X 티거 [PR 101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101)
- 우디 [PR 107](https://github.com/woowacourse/javascript-youtube-classroom/pull/107) X 시지프 [PR 113](https://github.com/woowacourse/javascript-youtube-classroom/pull/113)
- 하리 [PR 79](https://github.com/woowacourse/javascript-youtube-classroom/pull/79) X 민초 [PR 94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94)
- 비녀 [PR 108](https://github.com/woowacourse/javascript-youtube-classroom/pull/108) X 코이 [PR 92](https://github.com/woowacourse/javascript-youtube-classroom/pull/92)

<br />
<br />

## 아키텍처 분석

### [[#104](https://github.com/woowacourse/javascript-youtube-classroom/pull/104) X [#101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101)] 무비 X 티거

- UI를 위해 custom element 사용
- HTMLElement -> 상속 -> CustomElement -> 상속
- UI에서 발생한 이벤트 처리를 위해 custom event사용
- 데이터 관리를 위한 store가 존재하는데, store는 데이터 변경이 있으면 element들에게 알림을 준다.
- 로직에 대한 코드는 `domains`에 존재한다.

<br />

- **store**
  - 비디오에 변경사항이 있으면 구독자들(elements)에게 알린다.
  - 비디오에 대한 정보를 저장하고 관리한다.
- **domain**
  - element에게 발생한 이벤트를 구독한다.
  - 이벤트 발생을 처리하고 store에게 dispatch 요청을 보낸다.
- **element**
  - 이벤트 발생을 domain에게 알린다.
  - store에서 notify를 주면 UI를 렌더링한다.

<br />

#### store

- 어디서나 접근할 수 있는 싱글톤 패턴 사용

```js
    static _instance = null;

    static get instance() {
      if (!VideoStore._instance) {
        VideoStore._instance = new VideoStore();
      }
      return VideoStore._instance;
    }
```

- store를 element들이 구독하게 하여 데이터 변경이 일어나면 구독자들에게 알림을 준다.

```js
  #videos = [];

  #subscribers = []; // elements

  subscribe(element) {
    this.#subscribers.push(element);
    // element들이 store를 구독한다.
  }

  dispatch(action, data) {
    const newVideos = [...this.getVideos(), ...data];

    this.#setVideos(newVideos);
    // 데이터 변경이 있으면

    this.#subscribers.forEach((subscriber) => {
      subscriber.notify(action, data);
      // 구독자들에게 알린다.
    });
  }
```

#### custom element

- custom element 활용을 위한 추상 클래스인 `CustomElement`가 존재하고 이를 상속 받아 여러 element들을 만들었다.

```js
// abstract class
// parent class
class CustomElement extends HTMLElement {}

// child class
class MyClassroom extends CustomElement {}
class SearchResult extends CustomElement {}
```

<br />

### [[#107](https://github.com/woowacourse/javascript-youtube-classroom/pull/107) X [PR 113](https://github.com/woowacourse/javascript-youtube-classroom/pull/113)] 우디 X 시지프

> 기존에 MVC 패턴으로 코드를 짜왔는데, 이벤트가 발생할 때 데이터를 바꾸어주고, UI를 바꾸어 주는 작업이 조금 번거롭다고 생각했습니다. 그래서 React처럼 상태 정의하고, 상태가 변하면 UI가 바뀌게 짜기로 했습니다

- 리액트처럼 짜려고 한 코드!
- UI를 컴포넌트화
  - `component`라는 부모 클래스(core)가 존재하고 만들어지는 component들이 이를 상속받는 구조
  - component의 생성자에는 target(component의 element)와 props를 넘겨준다.
    - component에서 써야하는 함수 등을 `props`로 넘겨준다.
  - component는 리액트와 비슷하게 `beforeMounted()`, `afterMounted()` 등의 함수가 존재한다.
- state
  - component만의 지역 상태 존재
  - 전역 state는 rootStore에서 관리하고 state가 변경되었을 때 구독한 컴포넌트를 재 렌더링한다.

<br />
<br />

## 피드백 정리

😛 링크를 누르면 해당 discussion으로 이동! 😛

<br />

### 구조

[[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827920206)] 모듈 간의 연결 방식은 통일 해주자! (어떤 모듈은 클래스의 멤버변수로 지정해주고, 어떤 모듈은 import해주는 방식이 혼용되면 일관성이 떨어진다.) \*

### 함수

[[#101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101#discussion_r825252118)] 생성자에서만 호출하는 함수는 분리해주지 않아도 된다.

[[#101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101#discussion_r825260434)] 함수의 인자는 [2개 이하](https://github.com/qkraudghgh/clean-code-javascript-ko#%ED%95%A8%EC%88%98-%EC%9D%B8%EC%9E%90%EB%8A%94-2%EA%B0%9C-%EC%9D%B4%ED%95%98%EA%B0%80-%EC%9D%B4%EC%83%81%EC%A0%81%EC%9E%85%EB%8B%88%EB%8B%A4)가 적절하다.

<br />

### 클래스

[[#101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101#discussion_r827513622)] 클래스에서 불필요한 getter와 setter는 지양하자. \*

> 객체의 상태가 변경되는 것은 객체 스스로의 행동에 의해서야 한다. => 느슨한 결합과 유연한 협력 - "객체지향의 사실과 오해"

<br />

### 상수화

[[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#pullrequestreview-911446808)] threshold나 snackbar의 노출 시간 등 이후에 충분히 옵션값으로 자주 수정할 수도 있을 부분들, 의미있게 사용되는 부분들에 대해서는 중복으로 사용되지 않더라도 의미 있는 이름을 지어준다는 취지에서 상수화를 고려해보자.

[[#108](https://github.com/woowacourse/javascript-youtube-classroom/pull/108#discussion_r825376456)] LocalStorage에 사용하는 Key는 상수로 관리하자. 물론 쓰이는 Key가 하나라면 과도하게 분리하진 않아도 된다. 관리가 힘들어질때 분리해보자.

<br />

### OOP

[[#101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101#discussion_r825257497)] 도메인에 한정되어 사용되는 메서드라면 `private`를 고려해보자.

<br />

### HTML

[[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827932240)] `input`태그의 값이 필수적이라면 [required](http://www.tcpschool.com/html-tag-attrs/input-required)를 지정해주는 것을 고려해보자. \*

```html
<input required />
<!-- 사용자가 input태그를 작성하지 않았을 경우 form이 제출되지 않습니다. -->
```

[[#108](https://github.com/woowacourse/javascript-youtube-classroom/pull/108#discussion_r825376270)] 태그의 속성 중 [`role`](https://happycording.tistory.com/entry/HTML-Role-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC%EB%A7%8C-%ED%95%98%EB%8A%94%EA%B0%80)은 element에게 명확한 의미를 부여해준다. (웹 접근성을 위한 속성) \*

<br />

### 이벤트

[[#79](https://github.com/woowacourse/javascript-youtube-classroom/pull/79#discussion_r825301847)] window에 eventListener를 붙일 수가 있는데 window는 browser 내에서 모두가 공유하는 최상단 객체인데 이곳에 eventListener를 붙이는건 성능적인 면에서, 또 안정성 면에서 좋지 않다. 본인이 의도한 영역으로 최소화 하자.

[[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827895129)] keyCode는 현재 [deprecated](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/keyCode)! 대신 [key](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key)를 사용해보자. \*

[[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827900878)] 이벤트 위임을 할 때 이벤트 타겟이 특정 class를 가지고 있냐에 따라 이벤트를 적용해줄 수 있다. \*

```js
if (e.target.classList.contains('video-item__save-button')) {
  video.save(e);
}
```

[[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827904507)] 도메인에 event객체를 모두 넘겨주지 말고 필요한 데이터만 넘겨주자. event 객체라는 건 UI 영역에서만 알면 되는 정보이기 때문. \*

<br />

### 유용한 정보

[[#101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101#discussion_r825256475)] 삼항 연산자의 조건과 리턴값이 동일하다면 병합 연산자를 사용해보자 \*

```js
// 삼항 연산자
return localStorage.getItem('videos')
  ? JSON.parse(localStorage.getItem('videos'))
  : [];

// 병합 연산자
return JSON.parse(localStorage.getItem('videos') ?? '[]');
```

[[#113](https://github.com/woowacourse/javascript-youtube-classroom/pull/113#discussion_r826976214)] 테스트를 진행할 떄 CLI만 사용해서는 코드레벨에서의 에러를 확인하기 어려운 경우가 많다. jest plug-in과 같은 도구를 사용해보자. \*

[[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r825287822)] export를 묶어서 해줄 수도 있다. \*

```js
const MAX_SAVE_COUNT = 100;

const STORAGE_KEY = 'videoId';

export { MAX_SAVE_COUNT, STORAGE_KEY };
```

<br />

### API 통신

[[#104](https://github.com/woowacourse/javascript-youtube-classroom/pull/104#pullrequestreview-908015752)] 너무 잦은 호출에 대한 문제

1. 무한 스크롤로 구현했을 시 스크롤 이벤트가 발생했을 경우 바로 API가 호출된다. -> 유저가 스크롤을 한번에 훅 넘겼을 때, 그만큼의 요청이 서버에 전달된다.
2. 스크롤의 접점에서 여러 번의 스크롤 이벤트가 발생할 수 있다. (바운싱 현상)

이 모든 문제는 [debounce와 throttle](https://techcourse.woowahan.com/s/dSWvXWYI/ls/Is8GTQl7)를 통해 해결가능하다.

[[#107](https://github.com/woowacourse/javascript-youtube-classroom/pull/107#discussion_r825312544)] 웹 성능 최적화를 위해 현재 화면에 보여지지 않는 이미지를 추후에 로딩하여 초기 로딩시간을 줄이는 [lazy loading기법을 간단히 태그의 loading 속성을 통해 적용해줄 수 있다.](https://web.dev/i18n/ko/browser-level-image-lazy-loading/) \*

```html
<img ... loading="lazy" />
```

[[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827880419)] API요청 URL을 작성할 때 파라미터가 많다면 URL이 복잡해지고, 파라미터를 변경해야할 때 어려움이 있다. 이럴 때 [URLSearchParams](https://developer.mozilla.org/ko/docs/Web/API/URLSearchParams)와 같은 API를 적용해보자. \*

[[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827909437)] Promise체인 내에 async/await가 혼용되면 어색할 수 있다.

<br />

### 테스트

[[#79](https://github.com/woowacourse/javascript-youtube-classroom/pull/79#discussion_r825298920)] 테스트를 위해 직접 mock객체를 만드는 경우가 있다. 하지만 이 경우에 테스트하는 객체와 mock객체의 규격이 동일하지 않다면 실제 코드에서는 테스트가 실패할 수 있어 테스트 코드가 무의미해진다. \*

<br />

### Custom Element

[[#104](https://github.com/woowacourse/javascript-youtube-classroom/pull/104#pullrequestreview-908015752)] custom element를 사용했을 경우 모든 element에 대한 `import`를 `index.js`에서 해주는 것은 구조가 커져 custom element가 많아졌을 경우 적절하지 못하다. 사용하는 곳에서 `import`를 해주자.

<br />

### Prettier

[[#113](https://github.com/woowacourse/javascript-youtube-classroom/pull/113#discussion_r825396404)] prettier에서는 기본 설정이 존재하고, 이 기본 설정을 그대로 따르고 싶을 수 있다. 하지만 기본 설정을 모두 외우고 있는 개발자는 드물고 기본 설정값을 바뀔 수 있기 때문에, 기본 설정이라도 명시해주는 것이 좋다. \*
