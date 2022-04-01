# 주간 코리스 [👩🏻‍💻나만의 유튜브 강의실 STEP1🧑🏻‍💻]

## 0. 코리스 설명

- [코드리뷰스터디(코리스)](https://github.com/woowacourse-study/2022-code-review-study)는 `함께 자라기 🌱`를 지향해요.

- 이번 호에 정리한 코드리뷰 피드백 주제는 `Level1 Youtube Step1`입니다.
- 스터디원이 아니시더라도 본 자료에 대한 피드백 및 [디스커션](https://github.com/woowacourse-study/2022-code-review-study/discussions) 참여 등을 모두 환영해요.💚

- 자료 : 우아한테크코스 프론트엔드 4기 코드리뷰
  - 각 목록의 앞에 해당 PR번호 기재
- 편집 : 코드리뷰스터디원
  - 마르코(@wonsss) ✨
  - 호프(@moonheekim0118) ✨
  - 무비(@byhhh2) ✨
  - 록바(@lokba) ✨
  - 꼬재(@kkojae91) ✨

<br>

## 1. 아키텍처

### 1-1. 패턴에 대한 생각

- [[#99](https://github.com/woowacourse/javascript-youtube-classroom/pull/99#discussion_r825284197)] 해당 포스팅에서 프론트엔드가 거쳐온 아키텍처를 훑어볼 수 있어 좋아요!
  > [구조에 대한 설계 패턴이 정리된 블로그](https://velog.io/@teo/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%EC%97%90%EC%84%9C-MV-%EC%95%84%ED%82%A4%ED%85%8D%EC%B3%90%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%EC%9A%94)

### 1-2. 도메인과 UI

- [#76] 도메인과 UI를 굳이 왜 나누어야 하는가? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/76#pullrequestreview-907964309)
  - domain과 ui를 왜 나누어야하는지 질문을 주셨는데 domain과 ui가 코드가 같이 있다면 ui를 얼마나 재사용 할 수 있을까요? 또한 지금 작성하신 searchModal view를 유투브 검색이 아니라 다른 플랫폼영상 검색 view로 재사용 가능한지 생각해보면 좋을것 같습니다. 소프트웨어 설계에서 '결합도(coupling)'에 대해서 공부하시면 도움이 될듯합니다.
  - 관련 키워드 : 낮은 결합도와 높은 응집도 ([내가 추천하는 게시글](https://madplay.github.io/post/coupling-and-cohesion-in-software-engineering))

### 1-3. 일관성

- [[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827920206)] 모듈 간의 연결 방식은 통일 해주자! (어떤 모듈은 클래스의 멤버변수로 지정해주고, 어떤 모듈은 import해주는 방식이 혼용되면 일관성이 떨어진다.) \*

<br>
<br>

## 2. 클래스

### 2-1. 클래스의 존재 의미

- [#91] `App` Class정의 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#￼￼discussion_r825456612)
  1. 한번도 사용하지 않을 this.EventHandler는 왜 저장하는건가요?
  2. 그리고 멤버변수도 사용하지않고, 멤버함수도 존재하지 않는 App class 는 어떤 이유에서 필요한가요?
  3. 멤버변수의 이름은 소문자로 시작해야 합니다.

```
index.js
HTML 템플릿 및 JavaScript의 컴포넌트를 조합하여 렌더링하고 실제 표시한다.

App.js
컴포넌트를 정의하는 프로그램이다. 실제로 화면에 표시되는 내용 등은 여기에서 정의된다.
```

### 2-2. 추상클래스

- [#95] 추상클래스 인스턴스화 막기 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/95#￼discussion_r825238162)

```javascript
if (this.constructor === Display) {
  throw new Error('추상 클래스는 인스턴스화 할 수 없습니다.');
}

class Display {
  constructor() {
    if (this.constructor === Display) {
      throw new Error('추상 클래스는 인스턴스화 할 수 없습니다.');
    }
  }
}
class D2 extends Display {}
D2.prototype.constructor = Display;
const d = new D2(); // Error: 추상 클래스는 ...
```

```javascript
 if (new.target.name === Display.name) {
      throw new Error('추상 클래스는 인스턴스화 할 수 없습니다.');
    }
```

- 위에서 D2는 추상클래스가 아님에도, constructor 를 덮어 씌움으로써 같은 에러를 만나게 된다.
- [new.target - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/new.target) 속성을 이용할 수 있다.
- new target 속성은 함수 또는 생성자가 new 연산자를 사용하여 호출되었는지 확인한다.
- 일반함수 호출에서 new.target 은 undefined

- [#95] 추상화에 많은 공을 들이신 것 같아요. 개발하면서 즐거우셨을 것 같습니다. 다만 이번과 같은 규모가 작은 프로젝트에서는 이런 추상화/패턴화가 오히려 가독성/빠른 개발을 해치는 것은 아닐지에 대해서도 한 번쯤 생각해 보시면 좋겠습니다.

- [#95] 전체 코드를 둘러보니, Display의 구현클래스들도 실상은 인스턴스로서의 가치가 없이 오직 초기화 용도로만 클래스를 활용하고 있더군요. 이런 경우에까지 클래스를 사용하는 것이 맞는지 고민해보시면 좋겠어요. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/95#￼discussion_r825242959)

### 2-3. 클래스의 메서드

- [#111] 이벤트핸들러로 등록할 메소드는 `bind(this)` 대신 class fields에 `화살표함수`를 사용할 것을 추천한다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/111/files/23d6739edc86e14986bd35b4bf2b276833475326#r825246994)

  - 왜냐하면 bind(this)를 사용하면 메소드 선언 방식에 의해 prototype에 원본메소드가 남아있는 채로, 실제로 호출되는건 `새로 만들어진 bound 메소드`이다. 사용하지 않을 불필요한 메소드가 남아있어 메모리가 낭비된다고 볼 수 있다.
  - 반면 class field를 이용하면 prototype에는 남아있지 않고, 인스턴스 자신이 사용할 함수만 들고 있을 수 있다.

- [#105] bindEvent를 따로 빼게 되는 경우 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/105#discussion_r826553859)

  - (1) 클래스 인스턴스를 만들게 될때 멤버변수와 멤버함수를 가지고 메모리에 올라가게 됩니다. 최초 실행 이후 더이상 실행되지 않는 멤버함수를 계속 들고 있을 필요가 없다고 판단됩니다.
  - (2) 생성자 호출이후 bindEvent를 stack에 할당하고 호출하는데 이 또한 1번처럼 굳이 한번 더 할 필요없느 불필요한 과정이라고 생각합니다.
  - (3) bindEvent를 private으로 은닉하지 않음으로서 메소드가 노출되고 외부에서 잘못 사용할 여지가 있습니다.(1번과 2번은 지나친 성능 고려이긴 합니다)
  - 한편 여러 줄이여서 길어진다면 주석을 활용하여 구분, 줄바꿈을 통해서 구분, 새로운 메소드를 활용하여 구분할 수 있습니다. 이부분은 개발자마다 다르겠지만 이때는 bindEvent를 메소드를 따로 빼도 괜찮다고 생각합니다.

- [[#101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101#discussion_r825252118)] 생성자에서만 호출하는 함수는 분리해주지 않아도 된다.

- [[#101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101#discussion_r825257497)] 도메인에 한정되어 사용되는 메서드라면 `private`를 고려해보자.

### 2-4. 클래스 사용 시에 주의 사항

- [[#101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101#discussion_r827513622)] 클래스에서 불필요한 getter와 setter는 지양하자. \*
  > 객체의 상태가 변경되는 것은 객체 스스로의 행동에 의해서야 한다. => 느슨한 결합과 유연한 협력 - "객체지향의 사실과 오해"

<br>
<br>

## 3. 객체

### 3-1. 객체 동결

- [#95] Object.freeze - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/95#discussion_r825232776)
  - Object.freeze() 메서드는 객체를 동결합니다. 동결된 객체는 더 이상 변경될 수 없습니다. 즉, 동결된 객체는 새로운 속성을 추가하거나 존재하는 속성을 제거하는 것을 방지하며 존재하는 속성의 불변성, 설정 가능성(configurability), 작성 가능성이 변경되는 것을 방지하고, 존재하는 속성의 값이 변경되는 것도 방지합니다. 또한, 동결 객체는 그 프로토타입이 변경되는것도 방지합니다.
  - [#95] 상수에 Object.freeze를 쓰지 않으면 협업시 다른 사람들이 해당 변수의 내용을 바꾸려는 시도를 하게 될까요?
    콤피님은 나중에 다른 코드상에서 이 변수의 내용을 바꿀 생각이 들 것 같나요?
  - 팀 내 컨벤션이 확고하여 약속을 지키지 않을 가능성이 현저히 적은 경우 -> 굳이 freeze를 할 필요까지는 없을 듯.

<br>
<br>

## 4. 함수

### 4-1. 함수의 분리

- [#97] 첫 비디오 리스트 요청 함수와 이 후 무한스크롤 시 비디오 요청 함수를 분리한 상황 - (이런 기능을 Pagination 이라고한다) 보통은 서버에서 size 와 page 값을 받고 그에 맞는 데이터를 내려주는데요, page 와 size 가 다르다고 해서 함수가 여러개라면 유지보수가 힘들 것이다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/97#￼￼discussion_r825633485)

- [#88] throttle하는 함수를 유틸로 분리해보자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/88#discussion_r825303875)

  ```jsx
  //적용 후
  const throttle = (func, delay) => {
    let timerId;

    return () => {
      if (!timerId) {
        timerId = setTimeout(() => {
          timerId = null;
          func();
        }, delay);
      }
    };
  };
  ```

- [#100] 함수 분리 기준

  - 로직이 길어지는 경우(코드 10줄 초과)
  - 두 번 이상 재사용될 수 있는 경우
  - 그리고 함수가 길다고 느껴진다면 함수 분리 전에, 함수명이 잘못되었을 수도 있으니 함수명과 그 기능을 다시 잘 살펴보자. (MVC에서 controller는 model과 view가 만나는 전쟁터라서 비대해지기 쉽다 😿 )

- [#111] 메소드들을 분리하는 실익이 있나요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/111/files/23d6739edc86e14986bd35b4bf2b276833475326#r825246805)

  - 아래 예제 코드에서 생각해볼만한 의견 : "기능별로 분리한 취지는 이해하지만, 실제로는 '초기화' 프로세스 중 일부에 불과하고 달리 따로 호출될 일이 없는데 말이에요. 불필요한 prototype 메모리만 차지하는 것이 아닐까요? 가독성을 위해 희생할 가치가 있다고 보시나요?"

  - 함수를 분리한 경우

    ```js
    class MyView1 {
      constructor() {
        this.render();
        this.bindElements();
        this.addEvents();
      }

      templateList() {
        return ` <li>A</li> <li>B</li> <li>C</li> <li>D</li> `;
      }

      template() {
        return ` <button class="my-btn">BUTTON</button> <ul class="my-list"> ${templateInner()} </ul> `;
      }

      render() {
        document.querySelector('.container').innerHTML = this.template();
      }

      bindElements() {
        this.$container = document.querySelector('.container');
        this.$button = this.$container.querySelector('.my-btn');
        this.$list = this.$container.querySelector('.my-list');
      }

      addEvents() {
        this.$button.addEventListener('click', this.clickHandler);
        this.$list.addEventListener('scroll', this.scrollHandler);
      }
    }
    ```

  - 초기화 단계에서 전부 처리해주는 경우(위 코드와 비교)

    ```js
    class MyView2 {
      constructor() {
        this.$container = document.querySelector('.container');
        this.$container.innerHTML = this.template();
        this.$button = this.$container.querySelector('.my-btn');
        this.$list = this.$container.querySelector('.my-list');
        this.$button.addEventListener('click', this.clickHandler);
        this.$list.addEventListener('scroll', this.scrollHandler);
      }

      templateList() {
        return ` <li>A</li> <li>B</li> <li>C</li> <li>D</li> ... `;
      }

      template() {
        return ` <button class="my-btn">BUTTON</button> <ul class="my-list"> ${templateInner()} </ul> ... `;
      }
    }
    ```

  - 반드시 코드 길이와 가독성이 연관된다고 보긴 어렵다.
    - 필요에 따라 적절한 구획을 나누어 줄바꿈 처리만 해주어도 가독성은 월등히 높아질 수 있다.
      - 리액트를 예로 들면, 함수형 컴포넌트는 실제로는 '하나의 함수' 이다. return구문의 위쪽에 매우 많은 로직이 들어가게 되는데, 단지 이것만 가지고 가독성이 떨어진다고 하지는 않는다.

### 4-2. 함수의 네이밍

- [#97] isEndOfResult 라는 함수명이 모호하다. 검색결과가 없다라는 것을 나타내야한다.
  그리고 그 것을 굳이 함수로 빼야할까? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/97#￼￼discussion_r825453334)

```javascript
if (this.#isEndOfResult(searchResultArray)) return;
```

### 4-3. 유용하게 쓰일 수 있는 내장 함수

- [#96] reduce로 객체 생성하기 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#discussion_r825314353)

```javascript
const output = Object.entries(origin).reduce(
  (acc, [key, value]) => ({ ...acc, [key]: value.substr(1) }),
  {}
);
```

### 4-4. 함수의 인자

- [[#101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101#discussion_r825260434)] 함수의 인자는 [2개 이하](https://github.com/qkraudghgh/clean-code-javascript-ko#%ED%95%A8%EC%88%98-%EC%9D%B8%EC%9E%90%EB%8A%94-2%EA%B0%9C-%EC%9D%B4%ED%95%98%EA%B0%80-%EC%9D%B4%EC%83%81%EC%A0%81%EC%9E%85%EB%8B%88%EB%8B%A4)가 적절하다.

<br>
<br>

## 5. 네이밍

### 5-1. 클래스의 이름

- [#97] Manager 라는건 어떤 데이터를 관리해주는 주체에게 붙은 이름 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#discussion_r825457653)

- [#89] export default할 class/함수명과 파일명을 일치시키는게 관례이다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/89#discussion_r827201820)

  ```jsx
  //해당코드
  //위치 : src/js/screen/index.js

  export default class ScreenManager{...}
  ```

  - 파일이 많아질 경우 import 된 class/함수명만 보고 파일을 찾아가는 경우가 많다보니 default로 export할 class/함수명과 파일명을 일치시키는게 일반적인 관례이다.

### 5-2. 함수의 이름

- [#97] Intersection Observer 실행 함수명:lastItemOfListObserver - 꼭 lastItemOfList 가 들어가야하나 싶다.
  추후에 기능이 바껴서 마지막줄 전체로 observer 를 옮길 수도 있고, 요점은 어느 누구를 observe 하는것을 나타내는 것이 아니라 intersecting 됐을 때 어떤일을 하는 옵저버인지를 나타내면 좋을 것 같다. 현재 서비스로 말하자면 비디오를 더 불러오는 옵저버라는 것을 나타내면 좋겠다.

- [#102] 보통 이벤트핸들러는 onXXXClick/Change, handleXXXClick/Change
  on/handle + {target} + {eventType} 으로 네이밍한다.- [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/102#discussion_r825408409)

- [#97] `is~~~() / check~~ ()` 는 boolean 값을 리턴예상 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#discussion_r825458109)

- [#80] handleSaveButtonClick -> handleSaveVideos 로 함수명 변경 (이벤트 위임의 의도를 드러내기 위해) 원래의 로직에서 네이밍만 교체하는 것도 좋다.
  마치 이벤트 위임을 위한 DOM 셀렉팅이 함수 네이밍까지 온 느낌인데,
  나중에 UI가 변경되어 셀렉팅이 바뀌게되면 함수명도 변경해야할까? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/80#discussion_r828758902)

- [#93] 메서드 네이밍 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/93#discussion_r824931787)

  - setStorageVideoList 메서드 네이밍과 로직이 매치가 안되는 거 같습니다. (**메서드명만 보면 saveVideoList 상태값을 통으로 바꿔줄 거 같다는 느낌을 받았습니다**)

    ```jsx
    //수정 전 해당코드
    setStorageVideoList(videoId) {
      this.saveVideoList = [videoId, ...this.saveVideoList];
      localStorage.setItem(VIDEO_ID_LIST_KEY, JSON.stringify(this.saveVideoList));
    }

    //수정 후 해당코드
    saveVideoIdToStorage(videoId) {
      this.saveVideoList = [videoId, ...this.saveVideoList];
      localStorage.setItem(VIDEO_ID_LIST_KEY, JSON.stringify(this.saveVideoList));
    }
    ```

### 5-3. 변수의 이름

- [#86] 제 삼자가 변수명만 보고 하고자하는 바를 대충이나마 유추할 수 있도록 하자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/86#pullrequestreview-907390620)

  - `최대 10개`가 아닌, `한 번에 요청하는 개수`가 10개 이기 때문에 수정 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/86#discussion_r824836900)

    ```jsx
    //수정 전
    const MAX_VIDEO_COUNT = 10;

    //수정 후
    const GET_VIDEO_COUNT = 10;
    ```

  - **disabled는 어떤 이유에서건 누를수 없는 모든 경우를 아우르는 표현**이니, 좀더 구체적인 의미를 지니는 단어로 표현하자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/86#discussion_r824837973)

    ```jsx
    //수정 전
    const BUTTON_DISABLED_TEXT = '저장됨';

    //수정 후
    const ALREADY_SAVED_VIDEO = '저장됨';
    ```

<br>
<br>

## 6. 상수화

### 6-1. Selector

- [#96] 사실 현업에서는 DOM 을 Selector 로 조작할 일은 거의 없다.
  물론 상수를 사용함으로써 변경에 용이하겠지만, HTML 에도 상수를 사용하는 게 아니라 개인적으론 가독성이 크게 떨어지는 것 같아서 사용하진 않는다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#discussion_r826001310)

- [#95] “자바스크립트 내에서 사용 중인 선택자들을 다른 개발자에게 알려주어, 템플릿 수정 시 주의를 요하고 싶었습니다.” 이 부분은 선택자를 사용하는 코드가 도처에 널려있을 경우에는 의미가 있다. 당연히 선택자가 수정될 상황이 생긴다면, html, css, js 모두에 수정이 필요하다. 그런 때에 js에서 해당 값을 찾아 변경하는 작업이 필요한 것도 맞다. 그런데 각 선택자가 오직 한군데씩만 쓰이고 있다면, 그 곳을 찾아서 변경하는 것과 상수를 찾아가서 수정하는 것 사이에 얼마나 큰 차이가 있을까? IDE의 일괄검색/바꾸기 기능을 쓰면 constant를 변경하는 것과 아무런 차이가 없지 않은가? 저는 일반론적인 말씀을 드린 것이 아니다. 지금 이 프로젝트에서의 효용성을 논하고 싶었던 것.오버엔지니어링이 아닌지 고민해보자는 취지이다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/95#￼discussion_r825233667)

### 6-2. 상수파일 분리

- [#96] 프로젝트 규모가 작다면 그냥 constants.js 에 모두 정의할 때도 있고, 점점 커져 역할에 나눌 필요가 있다면 분리할 때도 있다. 개인적으론 작을 땐 constants.js 에 정의하고 커짐에 따라 개선하는 걸 선호합니다! [YAGNI](https://martinfowler.com/bliki/Yagni.html) 원칙을 좋아합니다 그리고 범용성있게 사용되지 않는 상수는 보통 그 상수를 사용하는 파일 내에 정의할 때가 많다! - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#discussion_r826005848)

### 6-3. 상수화에 대한 고민

- [[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#pullrequestreview-911446808)] threshold나 snackbar의 노출 시간 등 이후에 충분히 옵션값으로 자주 수정할 수도 있을 부분들, 의미있게 사용되는 부분들에 대해서는 중복으로 사용되지 않더라도 의미 있는 이름을 지어준다는 취지에서 상수화를 고려해보자.

- [[#108](https://github.com/woowacourse/javascript-youtube-classroom/pull/108#discussion_r825376456)] LocalStorage에 사용하는 Key는 상수로 관리하자. 물론 쓰이는 Key가 하나라면 과도하게 분리하진 않아도 된다. 관리가 힘들어질때 분리해보자.

<br>
<br>

## 7. 이벤트

### 7-1. 스크롤 이벤트

- [#91] 보통 스크롤 값들로 무한스크롤을 구현할 때는 정확히 하단에 도착했을 때가 아닌 offset 을 준다.
  - `scrollTop + clientHeight >= scrollHeight - ${SCROLL_OFFSET}` - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#discussion_r825458566)

### 7-2. 이벤트 처리 관련

- [#96] addEvent 함수에서 preventDefault 를 매개변수로 받아줘야할까? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#discussion_r826011710)
  - 그리고 저는 preventDefault 는 사용하는 핸들러에서 명시적으로 사용하는 게 더 나을 것 같다 ! 보통 event.preventDefault 는 핸들러 내부에서 호출하는데, 그 코드가 한 depth 더 위에 있기 때문에 코드 파악하는 데 어려움이 있을 수도 있다!

```javascript
addEvent({ eventType, selector, handler, isPreventedDefault = false }) {
    const children = [...this.container.querySelectorAll(selector)];
    const isTarget = target => children.includes(target) || target.closest(selector);

    this.container.addEventListener(eventType, event => {
      if (isPreventedDefault) event.preventDefault();
      if (!isTarget(event.target)) return false;
      handler(event);
    });
  }
```

- [[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827895129)] keyCode는 현재 [deprecated](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/keyCode)! 대신 [key](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key)를 사용해보자. \*

- [[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827904507)] 도메인에 event객체를 모두 넘겨주지 말고 필요한 데이터만 넘겨주자. event 객체라는 건 UI 영역에서만 알면 되는 정보이기 때문. \*

### 7-3. 이벤트 영역

- [#89] 이벤트를 매핑하는 영역 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/89#discussion_r825416052)

  ```jsx
  //해당 코드
  document.addEventListener('click', this.handleCloseModal.bind(this));
  ```

  - 전역에서 사용되는 document에 이벤트 리스너를 추가하게 되면, 불필요한 이벤트 감지가 자주 발생하여 추천하지 않는다.
  - 전역에 매핑된 이벤트 리스너는 의도치 않은 동작을 발생시킬 수 있다.
  - 이벤트를 매핑하는 영역은 내가 다루고 있는 컴포넌트 영역으로 한정하는 것이 좋다.

- [[#79](https://github.com/woowacourse/javascript-youtube-classroom/pull/79#discussion_r825301847)] window에 eventListener를 붙일 수가 있는데 window는 browser 내에서 모두가 공유하는 최상단 객체인데 이곳에 eventListener를 붙이는건 성능적인 면에서, 또 안정성 면에서 좋지 않다. 본인이 의도한 영역으로 최소화 하자.

### 7-4. 이벤트 위임

- [#111] 아이템 생성시마다 매번 listener를 등록해주는 것보다는, 최초 한 번 상위 element에 등록해두고 event delegation을 활용하는 편이 훨씬 좋다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/111/files/23d6739edc86e14986bd35b4bf2b276833475326#r825247408)

  - 예제코드 해석
    - $videoList에 videoListTemplate을 만들어서 넣을 것이다.
    - videoListTemplate에 있는 각각의 요소들마다 이벤트리스너를 등록하는 것이 아니라, 최초 한 번만 상위 element인 $videoList에 이벤트를 등록함으로써 $videoList 하위로 들어가는 요소들에도 이벤트가 위임되도록 하였다.

  ```js
  // 예제코드
  class SearchModal {
    init() {
      this.$videoList = $('.video-list', this.$searchResult);
      this.addEvent();
    }

    // 상위 요소인 $videoList에 이벤트리스너 한 번 등록
    addEvent() {
      this.$videoList.addEventListener('click', event => {
        this.handleClickVideoList(event);
      });
    }

    // $videoList에 videoListTemplate을 만들어서 넣을 것이다.
    renderVideoItems(videos) {
      const videoListTemplate = videos
        .map(video => {
          return `<li class="video-item" data-video-id="${video.id}">
          // 생략
        </li>`;
        })
        .join('');

      this.$videoList.insertAdjacentHTML('beforeend', videoListTemplate);
    }

    // 인수로 event를 받아 조건을 분기하여 위임을 처리
    handleClickVideoList(event) {
      const { target } = event;
      if (target.classList.contains('video-item__save-button')) {
        this.handleClickSaveButton(event);
      }
    }
  }
  ```

- [[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827900878)] 이벤트 위임을 할 때 이벤트 타겟이 특정 class를 가지고 있냐에 따라 이벤트를 적용해줄 수 있다. \*
  ```js
  if (e.target.classList.contains('video-item__save-button')) {
    video.save(e);
  }
  ```

### 7-5. Custom Event

- [#85] 리뷰어님이 생각하는 보통 custom event를 사용하는 경우 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/85#discussion_r826997741)
  - 보통은 A 요소와 B 요소가 별도로 역할이 분리되어 있는데 이벤트를 발생시키는 A요소에 있는 값들을 해당 이벤트를 수신하는 상위 컨테이너 B 요소에서 그 전달받은 값 들로 새로운 값으로 가공하거나 변경해서 다른 C 요소에게 전달해야할 때, 각 요소에 대한 의존성을 분리시키고 이벤트에 의존하게 하면서 좀 더 유연하게 처리할 수 있는 장점이 있는것 같아요.

<br>
<br>

## 8. HTML

### 8-1. 렌더링 관련

- [[#87](https://github.com/woowacourse/javascript-youtube-classroom/pull/87#discussion_r825440742)] 사용자가 검색할 검색결과가 존재하지 않을지 / 존재할지 를 모르는 상태에서 미리 dom 을 그려놓으면, not_found.png 파일이 100MB 일 때 어떤 부작용이 있을지 생각해보면 좋겠네요! 타 html 태그와는 달리 img 는 기본적으로 추가해놓을 때 한 번쯤은 고민해볼 주제인 것 같네요!

  - lazy loading, 필요한 시점에 dom element를 생성해 추가하는 방식 두가지중 선택하면 좋다.
  - <details>
    <summary>Lazy loading</summary>

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

    </details>

- [[#107](https://github.com/woowacourse/javascript-youtube-classroom/pull/107#discussion_r825312544)] 웹 성능 최적화를 위해 현재 화면에 보여지지 않는 이미지를 추후에 로딩하여 초기 로딩시간을 줄이는 [lazy loading기법을 간단히 태그의 loading 속성을 통해 적용해줄 수 있다.](https://web.dev/i18n/ko/browser-level-image-lazy-loading/) \*
  ```html
  <img ... loading="lazy" />
  ```

### 8-2. 시맨틱 태그

- [#81] modal에 해당하는 semantic tag는 없을까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/81#discussion_r825378003)
  - [동키콩 답변] 공식문서를 확인 해보니, `<dialog>`태그가 존재한다.
  - [[MDN](https://developer.mozilla.org/ko/docs/Web/HTML/Element/dialog)] HTML `<dialog>` 요소는 닫을 수 있는 경고, 검사기, 창 등 대화 상자 및 기타 다른 상호작용 가능한 컴포넌트를 나타냅니다.

### 8-3. 태그의 특성

- [#76] button의 default type은 무엇일까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/76#discussion_r825261769)

  - button의 기본 default type은 submit이다.

- [#105] 브라우저 별로 button의 default type은 다를 수 있으니 반드시 명시해줘야 한다.

  - IE에서의 default type은 'button', 다른 브라우저에서는 'submit'이다.
  - 따라서 submit 타입으로 사용할 목적이 아니라면, 다른 타입으로 명시해줘야 한다.(그냥 버튼타입을 사용하려면 `type="button"`)

- [[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827932240)] `input`태그의 값이 필수적이라면 [required](http://www.tcpschool.com/html-tag-attrs/input-required)를 지정해주는 것을 고려해보자. \*

  ```html
  <input required />
  <!-- 사용자가 input태그를 작성하지 않았을 경우 form이 제출되지 않습니다. -->
  ```

### 8-4. 웹 접근성

- [[#108](https://github.com/woowacourse/javascript-youtube-classroom/pull/108#discussion_r825376270)] 태그의 속성 중 [`role`](https://happycording.tistory.com/entry/HTML-Role-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC%EB%A7%8C-%ED%95%98%EB%8A%94%EA%B0%80)은 element에게 명확한 의미를 부여해준다. (웹 접근성을 위한 속성) \*

### 8-5. Custom Element

- [[#104](https://github.com/woowacourse/javascript-youtube-classroom/pull/104#pullrequestreview-908015752)] custom element를 사용했을 경우 모든 element에 대한 `import`를 `index.js`에서 해주는 것은 구조가 커져 custom element가 많아졌을 경우 적절하지 못하다. 사용하는 곳에서 `import`를 해주자.

<br>
<br>

## 9. CSS

### 9-1. 방법론

- [#100] BEM은 궁극적으로 유니크한 클래스를 보장하기 때문에, BEM을 class name으로 사용한다면 굳이 id를 설정할 필요 없이, 유니크함이 보장되는 class name을 사용하는 것이 낫다. 또한, BEM을 class name으로 제공하셨기에 id과 혼동할 여지를 주지 않는게 중요하다.

### 9-2. 변수 활용

- [#88] 고정된 background, color 값들은 변수로 빼주세요. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/88#discussion_r825301665)

### 9-3. display

- [#88] NO_RESULT(결과 없음을 나타내는 페이지)는 항상 동일한 화면을 보여주기에 html에 있고, 상태에 따라 show, hide하는 방식을 이용하는게 어떨까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/88#discussion_r825304921)
  ```jsx
  //해당 코드
  this.searchResult.insertAdjacentHTML('beforeend', NO_RESULT_TEMPLATE);
  ```

<br>
<br>

## 10. Dom Element 조작

### 10-1. 추가, 삭제 관련

- [[#106](https://github.com/woowacourse/javascript-youtube-classroom/pull/106#discussion_r825445785)] replaceChildren() 도 넘기는 인수 없이 호출하면 하위 노드 요소를 비워줄 수 있어요. 추천 👍

  - <details>
    <summary>replaceChildren()</summary>

    element의 기존 자식 요소를 replaceChildren()의 인자값으로 변경해준다.

    - 인자 값은 DOMString이나 Node 객체가 올 수 있다.
    - 인자의 값이 비어있는 경우 하위 요소들을 모두 지워준다.

    Syntax

    ```js
    element.replaceChildren(...DOMStringOrNodeObject);
    ```

    리뷰어님 말씀 그대로!

    ```js
    element.innerHTML = '';

    element.replaceChildren();
    ```

    innerHTML -> replaceChildren()을 사용할 수 있다.

    > [MDN - replaceChilren()](https://developer.mozilla.org/en-US/docs/Web/API/Element/replaceChildren)

    </details>

- [#86] 내용을 지울때 replaceChildren()을 쓰자.

  ```jsx
  //수정 전
  this.videoListContainer.innerHTML = '';

  //수정 후
  this.videoListContainer.replaceChildren();
  ```

  - **헷갈림 주의!!** [Node.replaceChild()](https://developer.mozilla.org/ko/docs/Web/API/Node/replaceChild) vs [Element.replaceChildren()](https://developer.mozilla.org/en-US/docs/Web/API/Element/replaceChildren)
  - MDN에 따르면, **replaceChildren()모든 자식 노드를 비우기 위한 매우 편리한 메커니즘을 제공합니다.**

- [#86] innerHTML 보다는 insertAdjacentHTML 사용해 주세요. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/86#discussion_r824851095)

  - 리뷰어님이 첨부해준 [참고링크](https://developer.mozilla.org/ko/docs/Web/API/Element/insertAdjacentHTML)

- [#111] element를 빈번하게 show/hide해야 하는 경우, 해당 element를 삭제했다가 다시 삽입하는 방식보다는 보여줄 요소를 처음부터 맨 뒤에 만들어두고, css로 show/hide만 하는 방식이 성능상 더 좋을 수 있다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/111/files/23d6739edc86e14986bd35b4bf2b276833475326#r825247862)

- [#77] 리스트에 처음부터 skeleton item을 배치해두고 숨김처리한뒤 검색했을 때 skeleton 을 보이게 한 뒤 skeleton 맨 첫요소 앞에 검색 결과 리스트를 insert 해보세요. 그럼 굉장히 심플해져요. [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/77#discussion_r825409052), [커밋](https://github.com/woowacourse/javascript-youtube-classroom/pull/77/commits/9a2b69ff2adf55cdd3b2e1c4a55d360221077a14)

### 10-2. 로드 관련

- [[#90](https://github.com/woowacourse/javascript-youtube-classroom/pull/90#discussion_r825565265)] 추가로, DOMContentLoaded 이벤트는 DOM 트리 구축이 완료된 후 호출되는 이벤트라 하고, 해당 이벤트가 호출된 후 app이 실행되는 게 안전하다고 판단하여 사용했습니다. 혹시 제 판단에 잘못된 부분이 있다면 편하게 말씀해주세요😆

  - <details>
    <summary>DOMContentLoaded</summary>

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

    </details>

### 10-3. 탐색 관련

- [#89] document.querySelector vs element.querySelector - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/89#discussion_r827182868)

  ```jsx
  //해당 코드
  $('.video-item__thumbnail', element).src = thumbnails;
  ```

  - [리뷰어님 답변] 성능을 위한 이런 소소한 개선 좋습니다 👍
  - document.querySelector로 시작한다면 DOM 트리의 최상단에서부터 시작해서 찾게 되고 element.querySelector로 찾게 되면 해당 element부터 시작해서 찾게 됩니다
    물론 요즘이야 워낙 하드웨어가 좋아서 이런 정도는 인지할 수 없을 정도의 미세한 차이지만 **이런 작은 개선들이 조금씩 쌓여서 좋은 프로그램이 될 수도, 반대로 안좋은 미세한 차이들이 쌓여서 나중에는 거대한 똥덩어리가 되기도 하는걸 많이 봤습니다.**
  - 추가적으로 작성해주신 방식이 의도하지 않은 element가 select 되는 것을 막을수 있습니다. 예를 들어서 $('.video-itemthumbnail')이 document부터 시작한다면 DOM tree에서 class name이 video-item\_\_thumbnail인 첫 번째 element를 찾게 되서 엉뚱한 element의 src에 thumbnails가 대입되게 됩니다

### 10-4. Element 클래스 관련

- [#86] classList.toggle 상세 기능 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/86#discussion_r825563206)

  ```jsx
  //수정 전
  event === 'hide'
    ? skeletonUi.classList.add('hide-skeleton')
    : skeletonUi.classList.remove('hide-skeleton');

  //수정 후
  classList.toggle('hide-skeleton', event === 'hide');
  ```

  - [[MDN](https://developer.mozilla.org/ko/docs/Web/API/Element/classList)] toggle에 두번째 인수가 있을 때 : 두번째 인수가 `true`로 평가되면 지정한 클래스 값을 추가하고 `false`로 평가되면 제거한다.

<br>
<br>

## 11. 자바스크립트 특징

### 11-1. 비동기

- [#91] 먼저, javascript는 동기적 언어. 싱글 스레드로 한 번에 하나의 작업을 수행한다. 하지만 이런 방식은 웹 페이지에 치명적이다. 동기적인 방식으로 하면 하나의 동작이 끝날 때까지 다음 동작을 수행하지 못하게 된다! (엄청 버벅 거리거나 로딩이 오래 걸린다) 그래서 비동기를 통해 요청을 끝날 때까지 기다리지 않고 바로 다음 동작이 실행될 수 있도록 한다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#issuecomment-1067049781)

- [[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827909437)] Promise체인 내에 async/await가 혼용되면 어색할 수 있다.

### 11-2. 타입

- [[#99](https://github.com/woowacourse/javascript-youtube-classroom/pull/99#discussion_r826038350)] `Date 타입` : date를 파싱해서 그대로 사용해주셨는데 api에서 넘어오는 시간은 국제 표준시라 그대로 사용하게되면 일자가 안맞을 가능성이 있어보여요. date에 이해하는데 도움될만한 아티클이 있어 읽어보시길 추천드릴게요 😄
  - 다양한 지역에 대한 타임존을 제대로 지원하고 싶다면, 직접 구현하려는 욕심을 버리고 **Moment Timezone과 같은 라이브러리**를 활용하는 것이 나을 것이다.
    > 아티클 : [자바스크립트에서 타임존 다루기1](https://meetup.toast.com/posts/125), [자바스크립트에서 타임존 다루기2](https://meetup.toast.com/posts/130)

### 11-3. 유용한 문법

- [[#101](https://github.com/woowacourse/javascript-youtube-classroom/pull/101#discussion_r825256475)] 삼항 연산자의 조건과 리턴값이 동일하다면 병합 연산자를 사용해보자 \*

  ```js
  // 삼항 연산자
  return localStorage.getItem('videos')
    ? JSON.parse(localStorage.getItem('videos'))
    : [];

  // 병합 연산자
  return JSON.parse(localStorage.getItem('videos') ?? '[]');
  ```

- [[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r825287822)] export를 묶어서 해줄 수도 있다. \*

  ```js
  const MAX_SAVE_COUNT = 100;

  const STORAGE_KEY = 'videoId';

  export { MAX_SAVE_COUNT, STORAGE_KEY };
  ```

<br>
<br>

## 12. 예외 처리

### 12-1. 로컬스토리지 관련 예외 처리

- [#97] localStorage 는 기본적으로 item 이 존재하지 않을 수 있다는 가정이 있다. 그래서 예외처리가 필요하다.
  예를 들어 저장된 아이템이 없는 상태에서 getSavedVideos()[2].videoId 를 하면 런타임에러가 발생해 서비스 장애상황이 생긴다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/97#discussion_r825452143)

```javascript
const savedVideos = storage.getSavedVideos() || {};
```

- [#76] JSON.parse 할 수 없는 값이 반환된다면 어떻게 될까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/76#discussion_r825298848)

  ```jsx
  //해당 코드
  const getLocalStorage = key => JSON.parse(localStorage.getItem(key)) ?? [];
  ```

  - [[MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)] 변환할 문자열이 유효한 JSON이 아닐 경우에 syntaxError가 발생한다.

    ```jsx
    //수정한 코드
    const getLocalStorage = key => {
      try {
        return JSON.parse(localStorage.getItem(key)) ?? [];
      } catch {
        alert(ERROR_MESSAGE.WRONG_INPUT);
      }
    };
    ```

### 12-2. 안전한 예외처리

- [#95] `if (saveItemsCount === CLASS_ROOM_SETTING.MAX_SAVE_NUMBER)` 이런 코드보다 `if (saveItemsCount >= CLASS_ROOM_SETTING.MAX_SAVE_NUMBER)` 이게 더 안전하다.

  - 이유는 ? (🍺의 생각) 사용자가 버튼을 빠르게 눌러서 두번 저장될 수도 있는데 그러면 101은 예외사항으로 처리하지 못하기 때문이다. [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/95#discussion_r825242511)

- [#105] JSON.parse()의 인자로 parse하지 못하는 값이 넘겨서 온다면?
  - JSON.parse() 는 문자열을 JSON으로 파싱한다. 이 문자열은 유효한 JSON 형태의 문자열이어야 하며, 유효하지 않을 경우 에러가 발생한다.
    - `\r, \n, \t, \f `가 포함되면 안된다.
    - 여분의 콤마도 허용하지 않는다.
    - 프로퍼티 이름은 반드시 쌍따옴표로 표현해야 한다.
    - 01 처럼 리딩제로를 사용할 수 없고, 십진 소수점 뒤에는 최소한 하나의 숫자는 등장해야 한다.
      > [MDN-JSON bad parse](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Errors/JSON_bad_parse)

<br>
<br>

## 13. API

### 13-1. API 요청에 대한 예외처리

- [#96] 일정 시간후 fetch 요청 취소하기 - timeout - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#issuecomment-1068810922)
  - AbortController 웹 API 를 사용하여 구현 가능
  - signal 객체를 fetch option으로 넣어준다.
  - 사용자가 지정한 timeout 시간 이후 응답이 없으면 fetch 요청 취소

```javascript
const controller = new AbortController();
const { signal } = controller;
const timeout = 8000;

const request = async (uri, options) => {
  const timer = setTimeout(() => controller.abort(), timeout);
  const response = await fetch(uri, { ...options, signal });

  if (!response.ok) throw new Error('서버 오류');
  const data = await response.json();
  clearTimeout(timer);
  return data;
};
```

### 13-2. 효율적인 API 요청

- [[#104](https://github.com/woowacourse/javascript-youtube-classroom/pull/104#pullrequestreview-908015752)] 너무 잦은 호출에 대한 문제

  1. 무한 스크롤로 구현했을 시 스크롤 이벤트가 발생했을 경우 바로 API가 호출된다. -> 유저가 스크롤을 한번에 훅 넘겼을 때, 그만큼의 요청이 서버에 전달된다.
  2. 스크롤의 접점에서 여러 번의 스크롤 이벤트가 발생할 수 있다. (바운싱 현상)

  이 모든 문제는 [debounce와 throttle](https://techcourse.woowahan.com/s/dSWvXWYI/ls/Is8GTQl7)를 통해 해결가능하다.

- [[#94](https://github.com/woowacourse/javascript-youtube-classroom/pull/94#discussion_r827880419)] API요청 URL을 작성할 때 파라미터가 많다면 URL이 복잡해지고, 파라미터를 변경해야할 때 어려움이 있다. 이럴 때 [URLSearchParams](https://developer.mozilla.org/ko/docs/Web/API/URLSearchParams)와 같은 API를 적용해보자. \*

<br>
<br>

## 14. 테스트

### 14-1. TDD

- [#89] TDD는 어디까지나 더 나은 코드를 작성하기 위해 도움을 줄 수 있는 하나의 방법론일 뿐이지 이 자체가 목적이 되어서는 안된다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/89#pullrequestreview-908108852)

### 14-2. Mocking

- [#97] localStorage는 직접 구현하신게 아닌 브라우저 기능인데, 굳이 테스트해야할까?
  e2e 테스트였다면 비디오 저장 후 새로고침했을 때 저장된 비디오가 있는지, 그리고 2단계에서는 비디오가 잘 노출되는지를 테스트 할 듯하다.

  - jest 에서 localStorage를 테스트하려면 localStorage 를 mocking 해야하는데, 사실상 개발자가 mocking한 localStorage를 테스트 하는게 의미가 있는가? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/97#discussion_r825449991)
  - (의견) 테스트 한다면 localStorage 앞 뒤에 붙어있는 데이터 가공 및 요청 함수를 테스트하는게 맞다고 생각한다.

- [#96] 웹스토리지 자체를 테스트할 필요는 없다고 생각해요. 다만 웹스토리지에서 _가져와서 데이터를 조작하는 비즈니스 로직 부는 테스트해야합니당._ 만약 비즈니스 로직이 웹스토리지가 필요하다면 디펜던시를 줄여서 모킹할 수 있는 구조로 변경 해야한다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#￼issuecomment-1066917848)

- [[#99](https://github.com/woowacourse/javascript-youtube-classroom/pull/99#discussion_r825261609)] API를 모킹하는 대표적인 라이브러리 MSW

  > [msw](https://blog.mathpresso.com/msw%EB%A1%9C-api-%EB%AA%A8%ED%82%B9%ED%95%98%EA%B8%B0-2d8a803c3d5c)

- [[#79](https://github.com/woowacourse/javascript-youtube-classroom/pull/79#discussion_r825298920)] 테스트를 위해 직접 mock객체를 만드는 경우가 있다. 하지만 이 경우에 테스트하는 객체와 mock객체의 규격이 동일하지 않다면 실제 코드에서는 테스트가 실패할 수 있어 테스트 코드가 무의미해진다. \*

### 14-3. 테스트 파일 구조

- [#102] 테스트 파일 구조 - 테스트 파일이 분리가 되면 좋겠다는 생각을 했다.
  **test** 에 모아둔다면 테스트 파일들을 모으는 것 외에는 별다른 장점이 없다. 좋은 코드 중 하나를 비슷한 역할을 하는 코드들을 모아 보기 좋게/ 유지 보수에 좋게 만드는 것이라고 생각하는데, 테스트 코드도 마찬가지로 **test** 에 모으는 게 아니라 validator.js 와 동일한 뎁스에 validator.test.js 를 위치시키는게 좋다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/102#discussion_r825956918)

### 14-4. 올바른 테스트 내용

- [#91] 테스트 제목 ‘입력 없이 버튼을 눌렀다면 error를 throw한다.’,버튼을 눌렀다 가 함축한 내용이 많다. 검색어가 무엇일 때 버튼을 눌렀고 어떤 Error 가 throw 되는지 구체적으로 적는게 좋다.
  그리고 입력버튼을 눌렀다 는 e2e 관점에 가깝다, 검색어가 비었을 때 검색하면 ~~~에러가 발생한다 와 같이 기능 관련된 테스트 명으로 수정하는게 좋다 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#discussion_r825455932)

- [#96] isSameKeyword 같이 단순 문자열을 비교하는 함수를 테스트해줘야할까?
  테스트코드도 비용이다. e2e 테스트나 통합테스트로 작성한 다음 다른 사이드이펙트 방지를 테스트하는 건 의미가 있을 순 있겠으나 === 만 수행하는 테스트를 해야하나? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#discussion_r825312522)

- [#97] api가 올바른 형태의 응답값을 리턴하는지 확인하는 테스트에 관하여

  - 특별한 로직이 있는게 아니라 바이패스인 수준이기 때문에 (예를 들어 2 \* 5 = 10 처럼 2, 5를 넣으면 10이 반환되는 함수인지를 검증하는 로직같은) 굳이 테스트하지 않을 것 같다. api 응답값을 잘 가공하는지 보다, 응답값 중 localstorage에 저장되어있는 비디오라면 isSaved 가 true 일테니 localstorage 에 있는 video 갯수와 isSaved 가 true 인 video 의 갯수가 같은지 정도 테스트해볼 수 있을 것 같다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/97#discussion_r825450961)

- [#96] 널리 쓰이는 유틸성 함수같은 경우, 현업에서도 있는 라이브러리를 사용할 때도 있고, 재미상 직접 만들어 쓰는 경우도 (사실 더 많음) 있다. 그럴 때 비슷한 유틸 라이브러리를 찾아서 테스트 코드를 보면서 엣지 케이스나 고려할 점들을 참고하면서 테스트 코드를 짠다.
  그게 아니라 프로덕트 내에 특정 상황을 다루는 유틸 함수라면 기획이나 엣지 케이스를 직접 만들어서 최대한 범위를 맞춰야한다.
  테스트 코드는 어디까지나 우리 개발자가 작성하는 거기 때문에 완벽할 순 없고, 개발자 역량과 경험에 따라 그 촘촘함도 달라진다. 이 부분은 다른 테스트코드를 많이 보면서 경험을 쌓는 것 밖에 없다고 답을 드릴 수 밖에.. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#￼issuecomment-1066917848)

- [[#99](https://github.com/woowacourse/javascript-youtube-classroom/pull/99#discussion_r825272614)] 테스트의 케이스가 많은건 좋다고 생각하는데 의미있는 테스트인지는 생각해볼 필요가 있어보여요. 유틸리티가 많이 변경될 여지가 없다고 생각되기도 하구요. 테스트를 위한 테스트가 된 느낌입니다 😅

- [[#98](https://github.com/woowacourse/javascript-youtube-classroom/pull/98#discussion_r825415865)] API를 테스트 할 것인지..? API 응답을 처리하는 로직을 테스트할 것인지..?

  - 리뷰어님 : API를 테스트하는 것은 BE의 몫이라고 생각한다. 그렇게 때문에 API를 테스트 할 것이 아니라면 실제 API를 호출할 필요는 없다고 생각한다.
  - 내 생각 : 즉, API를 직접 호출하여 테스트 하는 것 보다는 API호출을 인터셉트하여 모킹된 데이터로 갈아끼워주는 작업을 해줘야 한다.

- [#89] **테스트의 목적** - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/89#discussion_r827155936)

  - 테스트의 목적은 단순히 기능을 검증하는 데에만 있지 않다.
  - 테스트 코드는 추후에 다른 개발자 혹은 현재의 flow를 망각한 자신에게 테스트 기능에 대한 어떤 flow가 필요한지 보여주는 설명서가 된다.
  - 또 다른 목적은 변경의 감지이다. 코드가 변경됐을 때 그 코드에 의존하고 있던 다른 코드가 변경된 내역을 반영하지 않고 있는 경우를 테스트 코드를 통해 발견할 수 있습니다.

- [#93] 일관성 있는 테스트 결과를 내려고 하자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/93#discussion_r824926586)

  ```jsx
  //해당코드
  try {
    await searchVideo.handleSearchVideo('playlist');
    expect(searchVideo.searchResults.length).toEqual(MAX_VIDEO_COUNT);
  } catch (error) {
    expect(error.message).toEqual(ERROR_MESSAGE.CANNOT_GET_YOUTUBE_VIDEO);
  }
  ```

  - 에러가 무조건 나는 케이스와 절대 에러 나지 않는 케이스로 각각 나눠서 테스트하면 좋을 것 같습니다.

- [#81] [동키콩 질문] 어디까지 테스트를 해야하는지 감이 안온다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/81#pullrequestreview-911456941)

  - **테스트 코드는, 직접 "이게 맞나..? 이렇게 디테일 해야해..?" 까지 짜보시는걸 추천드릴께요!** 항상 이야기하면 감이 잘 안오는데, 최대한 할 수 있는데로 다 나눠보시는걸 추천드릴께요!

- [#100] 오작동하는 케이스를 중심을 테스트하자
  - 로직의 정상 작동 결과는 하나뿐이지만, 오작동하는 케이스는 수십가지가 넘는다.
  - 따라서 유저가 어떤 행동을 했을 때 오작동을 하면 유저에게 그에 대한 피드백이 필요하다. 이러한 오작동 케이스에 대한 수많은 문제를 정상적으로 사용할 수 있도록 유도하는 코드가 필요하다.
  - 테스트코드는 오작동 문제 상황에 대한 코드 보완 여부를 협업하는 사람들과 공유하는 성격도 갖고 있다.
    - 따라서 에러 상황에 대한 테스트코드를 많이 고민하고 작성수록, 이 테스트코드를 코드단위로 이행시켜서 효율성을 증대시킬 수 있다.
  - 결론: 유저 관점에서 유저가 일으킬 수 있는 수많은 오작동 케이스를 커버할 수 있는 테스트코드를 작성하자. 그리고 테스트코드는 에러 상황 커버리지를 코드단위로 이행시키며 다양한 사람들에게 선한 영향력을 줄 수 있음을 염두하고 정성껏 작성하자!
    > 문제가 있는 경우에 대한 테스트 작성 예시 : "입력된 검색어가 없거나, 공백으로 입력된 경우 검색이 안되게한다."

### 14-5. 테스트 종류

- [#100] 유닛테스트와 통합(E2E)테스트의 차이를 생각하자 => 보통 더미 데이터로 많이 사용하지만 실제 api를 테스트해서 하는 것만큼 통합테스트에 적합한 것이 없다. 여기서 중요한 점은 "유닛 테스트" 와 "통합 테스트"의 차이를 집중해야 한다는 점이다.

  - 비디오의 데이터를 활용해 다양한 메소드나 함수들이 정상동작하는지 여부를 파악하고 싶다면 더미데이터를 이용해서 유닛테스트를 진행하면 될 것 같다.
  - 반면, api가 정상적으로 동작되는가에 대한 통합 테스트는 jest의 mock 기능 등을 이용해 진행하는 것이 적합할 것 같다.

- [#103] `API를 통한 테스트`는 `통합테스트`을 할 때 진행하면 될 것 같고, `유닛테스트`에서는 `더미데이터`를 사용하는 것이 적절할 것 같다.

- [#112] 더미데이터를 활용하여 checkSearchResult를 테스트하는 것은 의미있는 테스트라 판단합니다. checkSearchResult는 데이터를 가공하는 일을 하고 '단위 테스트'는 그 가공하는 일이 제대로 이루어지는지 테스트하면 됩니다. 유투브같은 외부 API의 데이터 형식이 바뀌는경우는 적으며, API를 업데이트하더라도 하위호환을 고려해서 대응합니다. 한편 실제 전체 프로그램의 작동하는것까지 고려하는것은 E2E테스트를 통해서 확인하는게 맞다고 생각합니다.

- [#100] GWT(given, when, then) 및 AAA 테스팅
  - Arrange : 테스트에 필요한 데이터를 생성한다.
  - Act : 테스트하려는 코드를 실행한다.
  - Assert : 예상하는 결과를 확인한다.

### 14-6. E2E 테스트 도구 Cypress

- [[#98](https://github.com/woowacourse/javascript-youtube-classroom/pull/98#discussion_r825416213)] Cypress에서는 fixture, intercept를 활용해서 API를 모킹할 수 있다.
  > [cypress-fixture](https://docs.cypress.io/api/commands/fixture), [cypress-intercept](https://docs.cypress.io/api/commands/intercept#Stubbing-a-response)

### 14-7. UNIT 테스트 도구 Jest

- [[#113](https://github.com/woowacourse/javascript-youtube-classroom/pull/113#discussion_r826976214)] 테스트를 진행할 떄 CLI만 사용해서는 코드레벨에서의 에러를 확인하기 어려운 경우가 많다. jest plug-in과 같은 도구를 사용해보자. \*

<br>
<br>

## 15. 클린 코드

### 15-1. 임시변수 활용

- [#86] 변수를 할당해서 명확히 하자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/86#discussion_r824860673)

  - searchResults는 제대로 값이 들어있을지를 의심하게 만드네요.

        ```jsx
        //수정 전
        await this.searchVideo.handleSearchVideo(this.searchInput.value.trim());
        this.renderSearchVideo(this.searchVideo.searchResults);

        //수정 후
        const searchResults = await this.searchVideo.handleSearchVideo(
          this.searchInput.value.trim()
        );
        this.renderSearchVideo(searchResults);
        ```

<br>
<br>

## 16. Project Setting

### 16-1. Package

- [#81] --no-cache로 설정한 이유는 있을까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/81#discussion_r825378085)

  ```jsx
  //해당코드
  "scripts": {
    "test": "jest --watch --no-cache",
  ```

  - [동키콩 답변] 공식문서를 다시 살펴보니 --no-cache를 사용할 경우 jest가 최소 두배이상 느려져 캐싱문제가 생길경우에만 --no-cache를 사용하라고 추천하고 있네요

- [#81] 버전을 1.0.0으로 하신 이유가 있을까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/81#discussion_r825378158)
  ```jsx
  //해당코드
  {
    "name": "javascript-youtube-classroom",
    "version": "1.0.0",
  ```
  - package.json 처럼 쓰이는 버전 양식을 [semver](https://semver.org/lang/ko/) 라고 합니다. (**Sementic Versioning의 약자**) 제가 보기엔, 완성된게 아니므로 메이저 버전이 1이 될 수 없을 것 같아요! 그렇기에 0.x.x 요렇게 표기되어야 하지 않을까 싶네요~

### 16-2. Prettier

- [#89] default 값에 의존하는 것보다는 tab size 같은 최소한의 정의는 명시적으로 표현하는게 좋을 것 같습니다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/89#discussion_r827184610)

- [#81] `printWidth : 100`을 한 이유가 있을까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/81#discussion_r825377862)

  - prettier 공식 문서에 따르면, **For readability we recommend against using more than 80 characters:**

- [#84] `trailingComma: all` 의 장점은?

  - trailingComma를 하게되면 코드리뷰 시점에 이전 코드 부분에 `, `가 추가가 안되므로 더 편하게 수정된 부분만 확인할수도 있는 등, 다양한 이점이 있어요!

- [[#113](https://github.com/woowacourse/javascript-youtube-classroom/pull/113#discussion_r825396404)] prettier에서는 기본 설정이 존재하고, 이 기본 설정을 그대로 따르고 싶을 수 있다. 하지만 기본 설정을 모두 외우고 있는 개발자는 드물고 기본 설정값을 바뀔 수 있기 때문에, 기본 설정이라도 명시해주는 것이 좋다. \*

### 16-3. ESlint

- [#100] 불필요한 eslint 설정 제거

  - globals 프로퍼티는 선언되지 않은 전역변수를 사용하는 경우 ESLint 경고가 발생하지 않도록 사용자 전역 변수를 추가하는 곳이다. ESLint는 V8 엔진에 있는 Atomics와 SharedArrayBuffer를 인식하지 못하기 때문에, 이러한 전역 객체를 사용하는 경우 이와 관련한 ESLint 설정을 추가해주는 것 같다. 이와 관련된 기능을 사용하지 않는다면, eslint 설정에 둘 이유가 없다.

    > 참고
    > [Eslint what is meaning of Atomics Read only and Shared Array Buffer Readonly](https://www.onooks.com/tag-globals-atomics-readonly/) > [MDN-Atomics](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Atomics)

    ```js
      globals: {
        Atomics: 'readonly',
        SharedArrayBuffer: 'readonly',
      },
    ```

<br>
<br>
