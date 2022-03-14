# Level1 Lotto Step2(담당 PR 번호들) - 록바

- 분석 담당 코드
  - 돔하디 [#126](https://github.com/woowacourse/javascript-lotto/pull/126)
  - 민초 [#155](https://github.com/woowacourse/javascript-lotto/pull/155)
  - 블링 [#129](https://github.com/woowacourse/javascript-lotto/pull/129)
  - 태태 [#125](https://github.com/woowacourse/javascript-lotto/pull/125)
  - 하리 [#122](https://github.com/woowacourse/javascript-lotto/pull/122)
  - 록바 [#148](https://github.com/woowacourse/javascript-lotto/pull/148)
  - 코카콜라 [#137](https://github.com/woowacourse/javascript-lotto/pull/137)
  - 안 [#153](https://github.com/woowacourse/javascript-lotto/pull/153)

<br>

## 아키텍처 분석(desc: 담당한 소프트웨어의 아키텍처를 간단히 분석하고 설명합니다)

<br>

### [#122] 하리

#### 인상 깊은 점

- 자주 쓰이는 color를 변수로 관리한 것

  ```javascript
  :root {
    --main-bg-color: #e5e5e5;
    --app-bg-color: #ffffff;
    --popup-bg-color: #00000080;
    ...
  }
  ```

<br>

- [MDN](https://developer.mozilla.org/ko/docs/Web/CSS/Using_CSS_custom_properties) 내용 - 흔히 보이는 패턴은 :root 의사 클래스에 선언해서 여러분의 HTML 문서 어디에서나 사용자 지정 속성에 접근할 수 있도록 구성하는 것입니다.

### [#129] 블링

#### 인상 깊은 점

- fieldset과 legend 사용

  ```html
  <form class="winner-number-input-form">
    <fieldset class="winner-number-input-container">
      <legend>당첨 번호</legend>
      <input
        type="number"
        class="winner-number-input"
        min="1"
        max="45"
        maxlength="2"
        placeholder="1"
        required
      />
      ...
    </fieldset>
  </form>
  ```

  - [MDN] `<fieldset>` 요소는 HTML 양식 속에서 그룹을 만들 수 있으며 `<legend>` 요소로 그룹의 설명(제목)을 제공할 수 있습니다. fieldset 태그는 주로 form 내부에 사용한다.

<br>

- 기존의 요소를 직접 생성한 방식을 선언적으로 구현

  ```javascript
  // 구현
  const createCustomElement = ({ tag, className, id, children }) => {
    const element = document.createElement(tag);
    Object.assign(element, className && { className }, id && { id });
    if (Array.isArray(children)) {
      element.append(...children);
      return element;
    }
    element.append(children);
    return element;
  };

  export const div = function createCustomDiv({ className, id, children }) {
    return createCustomElement({ tag: 'div', className, id, children });
  };

  export const p = function createCustomP({ className, id, children }) {
    return createCustomElement({ tag: 'p', className, id, children });
  };

  // 사용
  #generateLottoElement(lottoNumberSet) {
    return div({
      className: 'lotto',
      children: [
        p({ className: 'lotto-image', children: '🎟️' }),
        p({
          className: 'lotto-numbers',
          children: Array.from(lottoNumberSet).join(', '),
        }),
      ],
    });
  }
  ```

  - 방식이 신선하다. react의 jsx를 모티브 삼아 구현했다고 한다.
  - 리뷰어분이 추천해준 react 관련 아티클 [링크](https://pomb.us/build-your-own-react/)
  - 만약, depth가 깊은 요소를 만들어야 할 경우 함수 호출이 빈번히 일어나기에 비효율적이라 판단됨.
    <br>

---

## 피드백 정리

### 대분류(ex: 아키텍처, 함수/클래스, 컨벤션, DOM, 테스트 등)

#### 함수/클래스

- [#148] 이벤트 위임 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/148/files/397534106e2a8f87c1e4f76ef04723e4f54dd529#r820122291)

  - 수정 전 - (피드백 :각 input 하나하나에 event handler를 등록하지 않고, 이벤트 위임을 활용하자.)

    ```javascript
    this.winningNumberInputs.forEach((inputElement, index) => {
      inputElement.addEventListener("input", () =>
        this.handleWinningNumberInputFocus(inputElement, index)
      );
    });
    ```

  - 수정 후 - 최종 통과

    ```javascript
    this.winningNumberForm.addEventListener("submit", this.handleWinningNumberFormSubmit);

    handleWinningNumberFormInputFocus(e) {
      if (e.target.classList.contains('winning-number-input')) {
        ...
      }
    }

    ```

<br>

- [#125] 모델의 데이터 초기화 방식 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/125#pullrequestreview-899701248)

  - 초기화하는 메소드를 호출해서 초기화 vs 새로운 인스턴스 생성해서 덮어씌우기

    ```javascript
    //1번
    this.model.init();

    //2번
    this.model = new Model();
    ```

    - [리뷰어] 상황에 따라 적절한 방법을 택하면 됩니다. (1번)은 초기화할 대상이 많지 않아 간단히 처리할 수 있는 경우에 적합하고, (2번)는 초기화를 위한 비용이 새로 생성하는 것보다 많이 들 때 적합하겠죠. (2번)의 경우에도 버려진 기존 데이터는 G.C. 대상이 되니 원칙적으로는 걱정할 것이 없구요. (모델 외부 또는 내부 프로세스 중에 해당 데이터의 참조카운트를 증가시키는 사례가 있다면 G.C되지 않으니 주의가 필요하긴 합니다)

<br>

- [#125] 함수 분리 vs 함수 통합 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/125#discussion_r819189317)
  - **기능별로 잘게 쪼개놓고 보니 활용하는 곳이 하나뿐인 함수는 다시 하나로 통합하는 리팩터링을 거치는 편이 좋습니다.**

<br>

### 컨벤션

- [#148] customEvent 관련 유틸 함수 네이밍 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/148/files/397534106e2a8f87c1e4f76ef04723e4f54dd529#r820119930)

  - 수정 전 - (피드백 : 함수명이 짧다)

    ```javascript
    export const on = (target, eventName, handler) => {
      target.addEventListener(eventName, handler);
    };

    export const emit = (target, eventName, detail) => {
      const event = new CustomEvent(eventName, { detail });
      target.dispatchEvent(event);
    };
    ```

  - 1단계 수정 후 - (피드백 : event라는 변수명이 브라우저에서 흔히 쓰이는 event객체의 인스턴스로 오인할 가능성이 있어, 협업시에 문제 여지 존재)
    ```javascript
    const event = {
      on: (target, eventName, handler) => {
        target.addEventListener(eventName, handler);
      },
      emit: (target, eventName, detail) => {
        const event = new CustomEvent(eventName, { detail });
        target.dispatchEvent(event);
      },
    };
    ```
  - 2단계 수정 - 최종 통과
    ```javascript
    const eventManager = {
      on: (target, eventName, handler) => {
        target.addEventListener(eventName, handler);
      },
      emit: (target, eventName, detail) => {
        const event = new CustomEvent(eventName, { detail });
        target.dispatchEvent(event);
      },
    };
    ```

<br>

- [#148] 변수명은 요구사항이 변경되어도 수정되지 않도록 네이밍 해야한다. - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/148/files/027ff954b472b18deb84a8365466096f4b947fee#r820450287)

  - 수정 전 (피드백 : isNotDividedIntoUnit로 바꾸는게 어떠냐?)
    ```javascript
     export const isNotThousandUnit = value => {...}
    ```

<br>

- [#122] private과 public의 메소드 순서 정리 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/122/files/0148a8ca0b693ee52474deb5dd8dc8eac2a285eb#r819318713)

  ```javascript
  class PopupView{
    #initDom() {...}
    #visible() {...}
    #closeHandler() {...}
    #addCloseEvent() {...}
    addRestartEvent(resetEvent) {...}
    render(result, rewardRate) {...}
  }
  ```

<br>

- [#155] 이것것도 상수로 하자! - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/155#discussion_r820599544)

  ```javascript
  //수정 전
  movePreviousInput(e) {
    if (e.keyCode === 8) {...}
    ...
  }


  //수정 후
  movePreviousInput(e) {
    if (e.keyCode === BACK_SPACE_KEY_CODE) {...}
    ...
  }
  ```

<br>

### CSS

- [#148] on/off 상태에 따른 클래스 명 나누기 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/148/files/397534106e2a8f87c1e4f76ef04723e4f54dd529#r820123190)
  - 수정 전 - (피드백 : off인 상태를 기본으로 인지하고 별도로 on이 붙은 경우에만 관리하면 편리해요. on이 안붙어있다면 off일 것이라는 추정도 어렵지 않구요. **관리해야 할 값을 줄이는 것도 읽기 좋은 코드를 만드는 기법 중 하나**입니다.)
    ```javascript
    classList.contains("switch-off")
      ? classList.replace("switch-off", "switch-on")
      : classList.replace("switch-on", "switch-off");
    ```
  - 수정 후 - 최종 통과
    ```javascript
    this.purchasedLottoList.classList.toggle("switch-on");
    ```

<br>

### 기타

- [#122] 수학적 계산이 들어간 부분은 주석을 달면 좋다. - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/122/files/0148a8ca0b693ee52474deb5dd8dc8eac2a285eb#r819291894)

  ```javascript
  // 수익률 = (총 상금 / 구매 금액)을 퍼센트 환산
  // 즉, 원금 상환 === 수익률 100%
  const rewardRate = totalReward / this.lottos.length / 10;
  ```

<br>

- [#125] modal은 보통 body 태그의 맨 마지막에 둔다. **z-index를 굳이 설정하지 않아도 자연히 맨 위로 올라갈 수 있기 때문이다.**

<br>

### Test

- [#125] #으로 은닉한 메소드의 경우에는 유닛 테스트를 할 수 없다. - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/125#pullrequestreview-899701248)
  - 리뷰어 답변 : **원래부터 private method는 테스트할 수 없도록 하는게 객체지향에서의 본래 취지**입니다. "객체 내부 로직에 대해서는 궁금하지 않고 오직 밖으로 드러난 것만 잘 보이면 된다"는 차원이거든요. '단위테스트'라고 할 때의 '단위'가 함수 내지 메소드단위일 경우도 있지만, '객체'를 하나의 단위로 인식하는게 일반적입니다. 이 객체(인스턴스)에 대한 테스트는 오직 public method를 통해서만 이뤄지도록 하면 되는 것이에요. **public method만으로 기존 테스트를 정상적으로 수행할 수 없다면, 테스트 케이스를 수정하셔야 합니다.**

<br>

### 새로운 접근

- [#148] validation 로직에서 소개해준 리팩토링 기법 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/148/files/397534106e2a8f87c1e4f76ef04723e4f54dd529#r820123720)

  - 수정 전

    ```javascript
    export const validatePurchaseMoney = (value) => {
      if (isZero(value)) {
        throw new Error(ERROR_MESSAGE.ZERO_PURCHASE_MONEY);
      }

      if (isNotNumber(value)) {
        throw new Error(ERROR_MESSAGE.INVALID_PURCHASE_MONEY_TYPE);
      }
      ...
    };
    ```

  - 추천해준 방식
    ```javascript
    const purchasedMoneyValidator = [
      { test: isZero, errorMsg: ERROR_MESSAGE.ZERO_PURCHASE_MONEY },
      { test: isNotNumber, errorMsg: ERROR_MESSAGE.INVALID_PURCHASE_MONEY_TYPE },
      ...
    ]
    const validatePurchaseMoney = value => purchasedMoneyValidator.every(({ test, errorMsg }) => {
      if (test(value)) throw new Error(errorMsg);
      return true;
    })
    ```

<br>

### 좋은 이야기

- [#126] 사용자 편의성에 대한 좋은 조언 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/126#pullrequestreview-900288536)
  - **먼저 사용자가 무엇을 해야할지 직관적으로 바로 알 수 없다면 개발자의 잘못**이라고 생각합니다
    직관적으로 알 수 있는 UI가 먼저이고 그럴 수 없는 경우라면 어떻게 해야할지 구체적이고 상세하게 안내해야 된다고 생각합니다 그리고 **악의적인 초등학생이 깨트리려고 해도 절대 깨지지 않는 앱을 만들어야 된다고 생각합니다** (이건 Bsidesoft의 맹대표님 강의 중에 들은 교훈입니다)
    그래서 **`성공하는 일부 케이스만 테스트하는게 아니라 에러를 더 많이 발생시키고 그에 대한 예외처리를 상세히 기술해서 사용성이 깨지는 경우를 방지하려고 노력합니다`**
