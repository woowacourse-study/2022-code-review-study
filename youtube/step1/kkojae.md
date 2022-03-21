# STEP1. 나만의 유튜브 강의실 정리

---

## 담당 크루

---

- 준찌 [#83](https://github.com/woowacourse/javascript-youtube-classroom/pull/83)
- 빅터 [#90](https://github.com/woowacourse/javascript-youtube-classroom/pull/90)
- 꼬재 [#98](https://github.com/woowacourse/javascript-youtube-classroom/pull/98)
- 우연 [#109](https://github.com/woowacourse/javascript-youtube-classroom/pull/109)
- 유세지 [#99](https://github.com/woowacourse/javascript-youtube-classroom/pull/99)
- 온스타 [#87](https://github.com/woowacourse/javascript-youtube-classroom/pull/87)
- 아놀드 [#106](https://github.com/woowacourse/javascript-youtube-classroom/pull/106)

## 아키텍처 분석

---

준찌, 빅터

---

커스텀 이벤트를 통해 Components와 business의 의존관계를 끊음

- AppComponent: SearchForm, SearchModal, SkeletonList, Video, VideoContainer로 분리하여 component를 관리
- business: 비즈니스 로직 관리

클로저 활용 - state, components, notify를 은닉화한 코드

```js
export const { subscribe, setState, getState } = (function () {
  const state = {
    [STATE_STORE_KEY.IS_MODAL_SHOW]: false,
    [STATE_STORE_KEY.SEARCH_RESULT]: {
      videoList: [],
      keyword: null,
      prevVideoListLength: 0,
      nextPageToken: null,
    },
    [STATE_STORE_KEY.IS_WAITING_RESPONSE]: false,
    [STATE_STORE_KEY.SAVED_VIDEO]:
      webStore.getData(WEB_STORE_KEY.SAVED_VIDEO_LIST_KEY) ?? [],
  };

  const components = {
    [STATE_STORE_KEY.IS_MODAL_SHOW]: new Set(),
    [STATE_STORE_KEY.SEARCH_RESULT]: new Set(),
    [STATE_STORE_KEY.IS_WAITING_RESPONSE]: new Set(),
    [STATE_STORE_KEY.SAVED_VIDEO]: new Set(),
  };

  function notify(stateKey) {
    const subscribedComponents = components[stateKey];
    subscribedComponents.forEach((component) => component.render(stateKey));
  }

  return {
    subscribe: (key, component) => {
      components[key].add(component);
      return state[key];
    },
    setState(key, valueOrFunction) {
      if (typeof valueOrFunction === "function") {
        state[key] = valueOrFunction(state[key]);
        notify(key);
      }
      if (typeof valueOrFunction !== "function") {
        state[key] = valueOrFunction;
        notify(key);
      }
    },
    getState(key) {
      return state[key];
    },
  };
})();
```

## 피드백 정리

---

### HTML

---

[#87](https://github.com/woowacourse/javascript-youtube-classroom/pull/87#discussion_r825440742) 사용자가 검색할 검색결과가 존재하지 않을지 / 존재할지 를 모르는 상태에서 미리 dom 을 그려놓으면, not_found.png 파일이 100MB 일 때 어떤 부작용이 있을지 생각해보면 좋겠네요! 타 html 태그와는 달리 img 는 기본적으로 추가해놓을 때 한 번쯤은 고민해볼 주제인 것 같네요!

- lazy loading, 필요한 시점에 dom element를 생성해 추가하는 방식 두가지중 선택하면 좋다.

### Lazy loading

Lazy loading이란 무엇인가?

페이지를 로드할 때, 모든 이미지를 로드하는 것이 아니라 중요하지 않은 자원 또는 당장 필요 없는 자원의 경우 서버에 요청을 미루고 필요한 경우 해당 자원을 요청 받는 방법을 말한다.

Lazy loading이 필요한 이유

1. 데이터의 낭비를 막을 수 있다.

서버로부터 모든 자원을 요청하는 것은 잠재적으로 사용자들이 사용하지 않거나 볼 가능성이 적은 모든 자원들까지 요청받는 것이다.
따라서 서버로부터 필요한 자원만 요청 받고, 필요할 때만 해당 자원을 요청 받는 것이 효율적이다.

2. 브라우저의 랜더링 시간을 줄여준다.

브라우저는 서버로부터 자원을 요청받고 난 뒤에 화면에 랜더링을 한다. 따라서 불필요한 자원의 다운로드를 막는 것만으로도 프로세스 시간이 단축될 수 있다.

loading 속성 값은 다음 3가지와 같다.

- auto : 디폴트 값으로, 속성값을 지정하지 않은 것과 동일하다.
- lazy : 뷰포트 상에서 해당 이미지의 위치를 계산하여 이미지 자원을 요청함
- eager : 어느 위치에 있든지 이미지 자원을 바로 요청받음

사용법

```html
<img src="kkojae.png" loading="lazy" />
```

주의 사항

- 해당 속성을 사용할 경우 되도록 해당 이미지 영역의 크기를 지정하는 것이 권장된다.
  - 영역의 크기에 대한 정보가 없으면, 브라우저는 해당 영역의 크기를 알 수 없어 해당 영역을 0x0으로 처음에 인식하게 된다.
  - 만약 해당 이미지 영역으로 스크롤 할 경우 이미지가 로드 되면서 layout shift가 일어날 수 있기 때문에 되도록 해당 img 태그에 명시적으로 높이/너비 값을 지정해야 함.
- 페이지의 첫 시작부터 보이는 이미지에 대해서는 lazy-loading을 사용하지 말아야 한다.
- background-image에서는 적용 안 된다.

> [우테코 LMS lazy-loading](https://techcourse.woowahan.com/s/dSWvXWYI/ls/MlRcHlFG), [웹 성능을 위한 이미지 최적화](https://velog.io/@hustle-dev/%EC%9B%B9-%EC%84%B1%EB%8A%A5%EC%9D%84-%EC%9C%84%ED%95%9C-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%B5%9C%EC%A0%81%ED%99%94)

### JS

---

[#99](https://github.com/woowacourse/javascript-youtube-classroom/pull/99#discussion_r826038350) date를 파싱해서 그대로 사용해주셨는데 api에서 넘어오는 시간은 국제 표준시라 그대로 사용하게되면 일자가 안맞을 가능성이 있어보여요. date에 이해하는데 도움될만한 아티클이 있어 읽어보시길 추천드릴게요 😄

- 다양한 지역에 대한 타임존을 제대로 지원하고 싶다면, 직접 구현하려는 욕심을 버리고 **Moment Timezone과 같은 라이브러리**를 활용하는 것이 나을 것이다.

> 아티클 : [자바스크립트에서 타임존 다루기1](https://meetup.toast.com/posts/125), [자바스크립트에서 타임존 다루기2](https://meetup.toast.com/posts/130)

[#106](https://github.com/woowacourse/javascript-youtube-classroom/pull/106#discussion_r825445785) replaceChildren() 도 넘기는 인수 없이 호출하면 하위 노드 요소를 비워줄 수 있어요. 추천 👍

### replaceChildren

element의 기존 자식 요소를 replaceChildren()의 인자값으로 변경해준다.

- 인자 값은 DOMString이나 Node 객체가 올 수 있다.
- 인자의 값이 비어있는 경우 하위 요소들을 모두 지워준다.

Syntax

```js
element.replaceChildren(...DOMStringOrNodeObject);
```

리뷰어님 말씀 그대로!

```js
element.innerHTML = "";

element.replaceChildren();
```

innerHTML -> replaceChildren()을 사용할 수 있다.

> [MDN - replaceChilren()](https://developer.mozilla.org/en-US/docs/Web/API/Element/replaceChildren)

[#90](https://github.com/woowacourse/javascript-youtube-classroom/pull/90#discussion_r825565265) 추가로, DOMContentLoaded 이벤트는 DOM 트리 구축이 완료된 후 호출되는 이벤트라 하고, 해당 이벤트가 호출된 후 app이 실행되는 게 안전하다고 판단하여 사용했습니다. 혹시 제 판단에 잘못된 부분이 있다면 편하게 말씀해주세요😆

DOMContentLoaded

- DOMContentLoaded 이벤트는 초기 HTML 문서를 완전히 불러오고 분석했을 때 발생합니다. 스타일 시트, 이미지, 하위 프레임의 로딩은 기다리지 않습니다.

> [MDN - DOMContentloaded](https://developer.mozilla.org/ko/docs/Web/API/Window/DOMContentLoaded_event)

load

- load는 모든 리소스(css, image, font)등등 이 불러드려지고 나서 호출된다.

> [MDN - load](https://developer.mozilla.org/ko/docs/Web/API/Window/load_event)

beforeunload

- beforeunload 이벤트는 문서와 그 리소스가 언로드 되기 직전에 window에서 발생합니다. 이벤트 발생 시점엔 문서를 아직 볼 수 있으며 이벤트도 취소 가능합니다.
- 사용자가 화면을 종료할 때! 발생하는 이벤트
- beforeunload 이벤트를 사용하면 사용자가 페이지를 떠날 때 정말로 떠날 것인지 묻는 확인 대화 상자를 표시할 수 있습니다. 사용자가 확인을 누를 경우 브라우저는 새로운 페이지로 탐색하고, 취소할 경우 탐색을 취소하고 현재 페이지에 머무릅니다.

> [MDN - beforeunload](https://developer.mozilla.org/ko/docs/Web/API/Window/beforeunload_event)

unload

- 사용자가 화면을 종료하고, 모든 리소스들이 unloading 중에 발생하는 이벤트
- 이미지, IFrame 등 모든 리소스는 여전히 존재합니다.
- 최종 사용자는 아무것도 볼 수 없습니다.
- UI 상호작용은 아무 효과도 없습니다. (window.open(), window.alert(), window.confirm(), 등등)
- 오류가 발생해도 언로딩 절차는 중단되지 않습니다.

> [MDN - unload](https://developer.mozilla.org/ko/docs/Web/API/Window/unload_event)

[#90](https://github.com/woowacourse/javascript-youtube-classroom/pull/90#discussion_r825306933) notify 할 때, 따로 state 를 전달해줘야할까요? 👀

기존 코드

```js
function notify(stateKey) {
  const subscribedComponents = components[stateKey];
  subscribedComponents.forEach((component) =>
    component.wakeUp(state[stateKey], stateKey)
  );
}
```

변경된 코드 +getState 메서드 추가 후 분리

```js
function notify(stateKey) {
  const subscribedComponents = components[stateKey];
  subscribedComponents.forEach((component) => component.render(stateKey));
}
```

### test

---

[#99](https://github.com/woowacourse/javascript-youtube-classroom/pull/99#discussion_r825272614) 테스트의 케이스가 많은건 좋다고 생각하는데 의미있는 테스트인지는 생각해볼 필요가 있어보여요. 유틸리티가 많이 변경될 여지가 없다고 생각되기도 하구요. 테스트를 위한 테스트가 된 느낌입니다 😅

[#99](https://github.com/woowacourse/javascript-youtube-classroom/pull/99#discussion_r825261609) API를 모킹하는 대표적인 라이브러리 MSW

> [msw](https://blog.mathpresso.com/msw%EB%A1%9C-api-%EB%AA%A8%ED%82%B9%ED%95%98%EA%B8%B0-2d8a803c3d5c)

[#98](https://github.com/woowacourse/javascript-youtube-classroom/pull/98#discussion_r825415865) API를 테스트 할 것인지..? API 응답을 처리하는 로직을 테스트할 것인지..?

리뷰어님 : API를 테스트하는 것은 BE의 몫이라고 생각한다. 그렇게 때문에 API를 테스트 할 것이 아니라면 실제 API를 호출할 필요는 없다고 생각한다.
내 생각 : 즉, API를 직접 호출하여 테스트 하는 것 보다는 API호출을 인터셉트하여 모킹된 데이터로 갈아끼워주는 작업을 해줘야 한다.

[#98](https://github.com/woowacourse/javascript-youtube-classroom/pull/98#discussion_r825416213) Cypress에서는 fixture, intercept를 활용해서 API를 모킹할 수 있다.

> [cypress-fixture](https://docs.cypress.io/api/commands/fixture), [cypress-intercept](https://docs.cypress.io/api/commands/intercept#Stubbing-a-response)

### 구조, 설계

---

[#99](https://github.com/woowacourse/javascript-youtube-classroom/pull/99#discussion_r825284197) 해당 포스팅에서 프론트엔드가 거쳐온 아키텍처를 훑어볼 수 있어 좋아요!

> [구조에 대한 설계 패턴이 정리된 블로그](https://velog.io/@teo/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%EC%97%90%EC%84%9C-MV-%EC%95%84%ED%82%A4%ED%85%8D%EC%B3%90%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%EC%9A%94)
