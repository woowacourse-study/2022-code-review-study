# 주간 코리스 [🎱행운의 로또 2호]

## 0. 코리스 설명

- [코드리뷰스터디(코리스)](https://github.com/woowacourse-study/2022-code-review-study)는 `함께 자라기 🌱`를 지향해요.

- 이번 호에 정리한 코드리뷰 피드백 주제는 `Level1 LottoMission Step2`입니다.
- 스터디원이 아니시더라도 본 자료에 대한 피드백 및 [디스커션](https://github.com/woowacourse-study/2022-code-review-study/discussions) 참여 등을 모두 환영해요.💚

- 자료 : 우아한테크코스 프론트엔드 4기 코드리뷰
  - 각 목록의 앞에 해당 PR번호 기재
- 편집 : 코드리뷰스터디원
  - 마르코(@wonsss)
  - 호프(@moonheekim0118)
  - 무비(@byhhh2)
  - 록바(@lokba)
  - 꼬재(@kkojae91)

## 1. 함수

### 1-1. every 메서드를 활용한 validation

- [#148] every 메서드를 활용한 validation 방법 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/148/files/397534106e2a8f87c1e4f76ef04723e4f54dd529#r820123720)

  - every 뿐만 아니라 some, includes 등도 상황에 따라 활용할 수 있다.

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

### 1-2. try-catch

- `try-catch`의 장점은 오류가 있을 경우 즉시 실행을 멈춘다는 점과 에러 핸들링을 할 수 있다는 점 등이다. - [PR 136](https://github.com/woowacourse/javascript-lotto/pull/136#discussion_r820396206)

### 1-3. reduce

- [[#PR 142]](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820230362) 객체의 프로퍼티를 이용하여 계산할 때 [`reduce`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)를 사용해보자.

  - `entries()`의 사용

    ```js
    //PR 142
    //before
    const winningMoney =
      this.#winningCounts.fifthPlace * LOTTO.FIFTH_PRIZE +
      this.#winningCounts.fourthPlace * LOTTO.FOURTH_PRIZE +
      this.#winningCounts.thirdPlace * LOTTO.THIRD_PRIZE +
      this.#winningCounts.secondPlace * LOTTO.SECOND_PRIZE +
      this.#winningCounts.firstPlace * LOTTO.FIRST_PRIZE;

    //after
    const PRIZE_BY_PLACE = {
      fifthPlace: LOTTO.FIFTH_PRIZE,
      fourthPlace: LOTTO.FOURTH_PRIZE,
      thirdPlace: LOTTO.THIRD_PRIZE,
      secondPlace: LOTTO.SECOND_PRIZE,
      firstPlace: LOTTO.FIRST_PRIZE,
    };

    //entries()는 key-value쌍 배열을 반환
    const winningMoney = Object.entries(this.#winningCounts).reduce(
      (previous, [key, value]) => {
        return previous + value * PRIZE_BY_PLACE[key];
      },
      0
    );
    ```

  - `keys()`의 사용

    ```js
    //PR 140
    //before
    const lastValue =
      this.prizeCount.first * PRIZE_MONEY.FIRST +
      this.prizeCount.second * PRIZE_MONEY.SECOND +
      this.prizeCount.third * PRIZE_MONEY.THIRD +
      this.prizeCount.fourth * PRIZE_MONEY.FOURTH +
      this.prizeCount.fifth * PRIZE_MONEY.FIFTH;

    //after
    const lastValue = Object.keys(this.prizeCount).reduce(
      (money, rankKey) =>
        this.prizeCount[rankKey] * PRIZE_MONEY[rankKey.toUpperCase()] + money,
      0
    );
    ```

### 1-4. fill

- [#144] 배열을 fill을 이용해 초기화할 수도 있다.

  ```js
  const output = Array.from(
    { length: LOTTO_SETTING.RACKING_START_NUMBER },
    () => 0
  );
  const output = Array(LOTTO_SETTING.RACKING_START_NUMBER).fill(0);
  ```

### 1-5. 함수분리

- [#141] 그저 함수가 긴 것 같아서 불필요하게 분리된 짧은 함수는 가독성을 떨어트린다.

  - "확인해보니 함수내 '줄수 제한'은 요구사항이 아닙니다. 애초에 모든 함수가 15줄 이내여야 한다는 건 너무 가혹하기도 하고, 도리어 불필요한 짧은 함수를 남발하여 가독성을 떨어뜨리는 결과를 초래할 수도 있어요."

- [#131] 함수 분리를 "같은 로직이 반복되어 중복이 발생할 경우에 한정"한다. 긴밀하게 연결된 메서드라면 분리를 위한 분리는 할 필요가 없다.

- [#145] 의미있는 단위로 줄 간격을 줘볼까요?

  - 코드 라인수에 제한을 두는 것 보다, 의미있는 단위로 줄 간격을 나눠주는 것이 가독성을 높이는데 더 효율적이다.

- [#125] 함수 분리 vs 함수 통합 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/125#discussion_r819189317)

  - **기능별로 잘게 쪼개놓고 보니 활용하는 곳이 하나뿐인 함수는 다시 하나로 통합하는 리팩터링을 거치는 편이 좋습니다.**

### 1-6. Array.from

- [#147] 배열에 push하는 for문을 Array.from()을 이용하여 리팩토링

  - 참고: [MDN- Array.from()과 화살표 함수 사용하기](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/from#array.from%EA%B3%BC_%ED%99%94%EC%82%B4%ED%91%9C_%ED%95%A8%EC%88%98_%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)
  - 리팩토링 예시

    ```jsx
    // before
    const lottos = [];
    for (let i = 0; i < this.getLottoCount(); i += 1) {
      lottos.push(this.generateLottoNumbers());
    }
    return lottos;
    ```

    ```jsx
    // after
    return Array.from({ length: this.getLottoCount() }, v =>
      this.generateLottoNumbers()
    );
    ```

- [#132] from을 사용하면 fill,map 두가지 메소드를 사용하지 않고 하나의 메소드 구현할 수 있다. 더불어 현재 map에서 첫번째 파라미터 `_`를 사용하지 않아도 된다.

  ```js
  setLottoList(count) {
    this.#lottoList = Array(count)
      .fill(0)
      .map((_, index, list) => (list[index] = this.createLottoList(count)));
  }
  ```

  ```js
  setLottoList(count) {
    this.#lottoList = Array.from({length: count}, () => this.createLottoList(count));
  }
  ```

- [[#136]](https://github.com/woowacourse/javascript-lotto/pull/136#discussion_r820091000) `Array.from`을 통해 `map`의 작업도 동시에 할 수 있다.

```js
console.log(Array.from([1, 2, 3], x => x + x));
// expected output: Array [2, 4, 6]
```

### 1-8. 표준 내장 객체

- [#135] `Number.isNaN`과 `전역 window.isNaN`의 차이

  - `전역의 isNaN`은 인수가 Number형이 아닌 경우, 그 값을 먼저 숫자로 강제하고, 그 다음 NaN인지 판단을 한다. `"37"`은 NaN이 아닌 숫자 `37`로 변환되므로 `false`가 반환된다. 그리고 헷갈리게도 `undefined`, `{}`, `'ok'`도 `true(=NaN)`가 반환된다.
    - [MDN-혼란스러운 특별 케이스 행동](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/isNaN#%ED%98%BC%EB%9E%80%EC%8A%A4%EB%9F%AC%EC%9A%B4_%ED%8A%B9%EB%B3%84_%EC%BC%80%EC%9D%B4%EC%8A%A4_%ED%96%89%EB%8F%99)
  - 하지만 `Number.isNaN`은 오직 숫자형이면서 NaN인 값만을 true를 반환한다. 예를 들어, `undefined`, `{}`, `'ok'` 같은 것들은 `false(=not NaN)`가 반환된다. 또한, `37`, `"37"`, `""`, `"true"`, `"null"`도 모두 `false`가 반환된다.
  - 따라서 보다 엄격하게 `NaN`(`Number.NaN`, `0/0`)만을 판별하고 싶다면 `Number.isNaN`을 사용하는게 좋을 것 같다.

- [#130] Math.floor는 소수점 내림, parseInt는 소수점을 버림 / 따라서 양수에는 동일하게 작용하나 음수에는 다르게 작용
  - parseInt는 정확하게 string 형을 number로 만들어준다.
  - string -> number 변환시 박싱 / 언박싱이 일어난다.
  - 즉, 메모리 계층에서 기존 메모리 타입인 string을 갖고 있다가, number 형태의 메모리 블록을 만들고 그 후 string을 number로 옮기며 메모리의 비효율이 일어난다.
  - Math.floor는 타입캐스팅하지 않고 오롯이 계산만 하기 때문에 박싱 / 언박싱에서 자유로움

### 1-9. 순수 함수

- [#127] 순수하지 않은 함수는 그 작동 과정을 확인하기 위해 다른 함수를 살펴야 해서 좋지 않을 수 있다.

### 1-10. 객체 깊은 복사

- [[#156]](https://github.com/woowacourse/javascript-lotto/pull/156#discussion_r820578491) 객체를 깊은 복사하는 방법에는 `JSON.parse(JSON.stringify(object))`가 있다. 주의할 점은 객체안의 함수는 복사되지 않는다.

## 2. 클래스와 메서드

### 2-1. constructor

- [[#146]](https://github.com/woowacourse/javascript-lotto/pull/146#issuecomment-1060542912) 생성자에서 해줘도 될 일을 굳이 함수로 빼줄 필요는 없다.

### 2-2. 재사용되지 않는 인스턴스

- [#144] 재사용되지 않는 View 들은 굳이 selector 들을 외부에서 주입해줘야할까? 라는 생각이 들긴 하는군요 🤔
  - 재사용되지 않는 인스턴스는 필요한 곳에서 생성해서 사용하는 것이 좋다. 맴버 변수로 관리할 필요가 없기 때문

### 2-3. 은닉화

- [#121] 외부에서 접근해서는 안 되는 메소드에 대해서 은닉화한다. 그 접근 가능 여부의 기준에는 "외부에서 접근했을 때 객체의 상태를 직접적으로 변경시키는가?" 또는 "외부에서 써도 안전한 메소드인가?" 등이 있다.

### 2-4. 상속

- [#145] WinningLotto 가 Lotto 를 상속해보면 어떨까요?

  - 위에 코멘트 남긴대로 pushNumberIntoPickedNumbers 가 필요없으니 pushPickNumbers 같은 함수가 생길테니 재사용가능하고, generate 는 재정의하고, pickedNumbers 를 활용하면 bonusNumber 관련 로직만 추가하면 될 것 같은데요! 어떤가요?

    ```js
    export default class Lotto {
      // _는 protected 입니다.
      _lottoNumbers = [];

      generate() {
        const { MAX_RANDOM_NUMBER: MAX, MIN_RANDOM_NUMBER: MIN } =
          LOTTO_SETTING;
        const shuffledList = shuffle(
          [...Array(MAX - MIN + 1)].map((_, idx) => idx + MIN)
        );
        this._lottoNumbers = shuffledList.slice(
          0,
          LOTTO_SETTING.LOTTO_NUMBER_LENGTH
        );

        return this;
      }
    }
    ```

  ```js
  export default class WinningLotto extends Lotto {
    generate(winningNumbers, bonusNumber) {
      try {
        checkValidWinningNumberInput(winningNumbers, bonusNumber);

        this._lottoNumbers = [...winningNumbers];
        this.#bonusNumber = bonusNumber;

        return this;
      } catch (error) {
        throw new Error(error);
      }
    }
  }
  ```

- [#145] 상속을 언제 사용하면 좋은가?
  - 상속은 항상 장단점이 있죠! 한 함수만을 추상화한다해도 여러 자식 클래스에서 상속할 부모 클래스라면 그럴 가치는 있죠!
  - 다만 프론트는 서버에 비해 변경 가능성이 크기 때문에 (예를 들어 숫자 인풋에서 문자 인풋으로 바뀔 수도 있죠?) 뷰 단까지 상속 구조를 써야 하나..? 내가 실무에서 뷰 단을 상속한 적이 있었나..? 라고 생각했을 땐 케이스가 별로 없네요 😅
  - 아마 나중에 api 단을 구현하실 때 상속할 일이 좀 나올거에요! 그 때 더 고민하면서 적용해보시죠!

### 2-5. 인스턴스 초기화

- [#125] 모델의 데이터 초기화 방식 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/125#pullrequestreview-899701248)

  - 초기화하는 메소드를 호출해서 초기화 vs 새로운 인스턴스 생성해서 덮어씌우기

    ```javascript
    //1번
    this.model.init();

    //2번
    this.model = new Model();
    ```

    - [리뷰어] 상황에 따라 적절한 방법을 택하면 됩니다. (1번)은 초기화할 대상이 많지 않아 간단히 처리할 수 있는 경우에 적합하고, (2번)는 초기화를 위한 비용이 새로 생성하는 것보다 많이 들 때 적합하겠죠. (2번)의 경우에도 버려진 기존 데이터는 G.C. 대상이 되니 원칙적으로는 걱정할 것이 없구요. (모델 외부 또는 내부 프로세스 중에 해당 데이터의 참조카운트를 증가시키는 사례가 있다면 G.C되지 않으니 주의가 필요하긴 합니다)

## 3. DOM

### 3-1. react의 jsx를 모티브로 한, 선언적인 요소 생성 방식

- [#129] 기존의 요소를 직접 생성한 방식을 선언적으로 구현

  - 방식이 신선하다. react의 jsx를 모티브 삼아 구현했다고 한다.
  - 리뷰어분이 추천해준 react 관련 아티클 [링크](https://pomb.us/build-your-own-react/)
  - 하지만, 만약 depth가 깊은 요소를 만들어야 할 경우 함수 호출이 빈번히 일어나기에 비효율적이라 판단됨.

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

### 3-2. 자식노드 초기화 방법

- [#110] 부모에서 firstChild가 없을 때 까지 자식노드를 삭제한다 (자식 엘리먼트에서 remove를 사용하거나 부모 엘리먼트에서 removeChild를 활용) => 반복적으로 호출이 필요한 부분이지만 브라우저와 하위 노드 수에 따라 차이가 있겠지만 하위 노드의 수가 1000개를 기준으로 벤치마크에서 innerHTML보다 좋은 성능

  - 또는, textContent나 innerHTML을 “”로 변경
  - 또는 ,엘리먼트를 createElement로 생성했다면 replaceChildren을 사용하여 생성한 노드로 교체할 수 있다.

### 3-3. css의 class를 수정하기

- [#141, #143] [setAttribute](https://developer.mozilla.org/en-US/docs/Web/API/Element/setAttribute) vs [classList](https://developer.mozilla.org/ko/docs/Web/API/Element/classList), style 처리를 위한 경우 setAttribute() 메서드보다는 classList 프로퍼티의 메서드(add, remove, toggle, contains, replace)를 사용하는 것이 성능상 더 낫고 편리한 것 같다. 또한, `this.elem.style.display = 'flex'`처럼 js에서 직접 css를 고치기보다는 css의 class를 활용하는 것이 낫다.

- [#147] dom 요소 탐색 시, root부터 탐색하지 말고 parent부터 탐색하는 방식이 성능상 더 좋다. [리팩토링한 해당 커밋](https://github.com/woowacourse/javascript-lotto/pull/147/commits/88122adf486354c6864f7f881c5584f930788444)

### 3-4. 렌더링

- [[#146]](https://github.com/woowacourse/javascript-lotto/pull/146#discussion_r820173527) template가 반복될 때 `map`을 사용해주자.

## 4. 이벤트

### 4-1. 일회용 이벤트

- [#144] 이벤트를 한 번만 걸어주고 자동으로 지워주는 옵션

  ```js
  $element.addEventListener(eventType, callback, {
    once: true,
  });
  ```

### 4-2. 키보드 이벤트

- [#151] input은 값이 들어온 직후 발생되는 이벤트 / keyup은 keyboard에서 떨어질때 발생하는 이벤트

- [#135] 한글의 경우 keypress 이벤트에서 감지하지 못하는 문제가 있다.

### 4-3. 이벤트 위임

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

- [#135] 이벤트를 걸려는 element들의 상위 element에 eventListener를 달고, 이벤트 발생에 대한 콜백함수에 이벤트가 발생한 target의 선택자를 확인하는 분기를 만들고 해당 로직을 실행시킨다.

  - 예시 코드

    ```html
    <div id="app">
      <div class="main-container">
        <h1>🎱 행운의 로또</h1>
        <form id="lotto-price-form">
          <label for="lotto-price-input">구입할 금액을 입력해주세요.</label>
          <input type="number" placeholder="금액" id="lotto-price-input" />
          <input
            type="number"
            placeholder="금액"
            id="lotto-price-input"
            autofocus
          />
          <button id="lotto-price-button" class="btn">구입</button>
        </form>
        <div class="input-error-message" id="lotto-price-input-message"></div>
        <div id="result"></div>
      </div>
    </div>
    ```

    ```jsx
    export default class InputView {
      constructor() {
        this.$app = $("#app");
        // 생략
      }

      bindEvent() {
        this.$app.addEventListener("input", this.detectInputState.bind(this));
      }

      detectInputState(e) {
        if (e.target.id === "lotto-price-input") {
          this.detectMoneyInputState();
        }
        if (e.target.classList.contains("winning-number-input")) {
          this.detectWinningInputState();
        }
      }
    }
    ```

- [[#146]](https://github.com/woowacourse/javascript-lotto/pull/146#discussion_r820577052) 비슷한 방식으로 여러 요소의 이벤트를 처리해야할 때 [이벤트 위임](https://ko.javascript.info/event-delegation)을 사용해보자. -

## 5. 클린코드

### 5-1. Boolean을 return할 경우

- [[#142]](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820095323) Boolean을 `return`할 경우 조건을 `return`할 수 있는지 확인해주자.

### 5-2. 인자

- [[#140]](https://github.com/woowacourse/javascript-lotto/pull/140#discussion_r820068616) 두개 이상의 인자를 받을 경우 객체 형태로 넣어주세요. [인자는 2개 이하가 이상적입니다.](https://github.com/qkraudghgh/clean-code-javascript-ko#%ED%95%A8%EC%88%98functions)
  - 매개변수가 많으면 가독성 측면에서 좋지 않고, 유지보수할 때 실수를 유발할 수 있다. - [PR 136](https://github.com/woowacourse/javascript-lotto/pull/136#discussion_r820088741)

### 5-3. 네이밍

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
    ```js
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
    ```js
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

- [#150] 집합 개념을 사용하고 있을 때에는 1,2 보다 A, B가 들어간 이름이 더 적합하다.

  ```js
  intersection(arrayA, arrayB);
  ```

- [[#156]](https://github.com/woowacourse/javascript-lotto/pull/156#discussion_r820520699) `init`은 가장 처음을 위한 초기화에 가깝다. (reset이 아님)

- [[#120]](https://github.com/woowacourse/javascript-lotto/pull/120#discussion_r817495192) `submit`은 `form`에 있는 데이터를 제출한다는 의미로 많이 사용된다. toggle 같은 함수에서 붙일 필요가 없다. (예시 - `submitLottoToggle()`라는 이름의 함수)

- [#148] 변수명은 요구사항이 변경되어도 수정되지 않도록 네이밍 해야한다. - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/148/files/027ff954b472b18deb84a8365466096f4b947fee#r820450287)

  - 수정 전 (피드백 : isNotDividedIntoUnit로 바꾸는게 어떠냐?)
    ```javascript
     export const isNotThousandUnit = value => {...}
    ```

### 5-4. 상수화

- [#141] 여러 곳에서 자주 쓰이는 값들에 대해서만 고민하여 선택적으로 상수화를 하자.
- [#144] SELECTOR를 상수화 할 경우 class선택자와 id 선택자를 분리하여 작성해주는게 좋다.

  - 물론, 당연히 SELECTOR, DOM_NAME 의 쓰임새에 대한 '약속'을 했지만, 휴먼에러는 언제든 발생할 수 있으니까요.
    ID SELECTOR 인데 CLASS 로 생각해서 iterator 문법을 사용할 수도 있고요 ;ㅁ;

- [#132] MONEY를 객체로 만들 필요가 있을까요?ㅎ 혹시 제가 생각못한 확장성 부분이 있을까요

  - 현재 프로젝트에서는 모두 객체로 사용하지만 실제 상수는 enum, array, 값 등 다양하게 사용됩니다. 그렇기때문에 통일성을 맞추기보다 그 역할에 맞게 작성하시는게 좋을 것 같습니다 ㅎ

    ```js
    export const MONEY = {
      STANDARD: 1000,
    };
    ```

    ```js
    export const MONEY_STANDARD = 1000;
    ```

### 5-6. 긍정 조건문

- [#152] 조건에 `!`을 되도록 쓰지 않는다(다만 early return을 사용할때는 부정 조건문을 자주 작성할 수도 있다.). 그리고 식별자명 자체에 부정문이 함축되어 있는 경우도(ex, isNotPositive), 부정문 대신에 부정을 함축한 반의어 단어(ex, negative)를 활용하는 것이 가독성에 더 좋고 실수를 줄일 수 있다.

  - `없는 게 아닐 때`보단 `있을 때`가 좀 덜 헷갈린다.
    - `!isEmpty` -> `has~`

  ```plain
  !isEnough -> isInsufficientFare
  !isValidRangeNumber -> isOverRangeLottoNumber
  !isValidRangeNumbers -> isOverRangeLottoNumbers
  isNotOverlapped -> isOverlapped
  !isValidCount -> isInvalidCount
  isNotIncludedWinningNumbers -> isIncludedWinningNumbers
  ```

### 5-7. 공통으로 관리하는 변수는 배열과 객체로 묶기

- [[#136]](https://github.com/woowacourse/javascript-lotto/pull/136#discussion_r820089422)
  변수가 많으면 관리포인트가 많아진다. 공통으로 관리할 수 있는 부분은 배열과 객체를 이용하자.

## 6. CSS, HTML

### 6-1. css 클래스명

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

### 6-2. aria label

- [#130] aria label은 해당 section이 어떤 것을 의미하는지 알려주고 웹에 더 정확한 정보를 전달하기 위해 사용한다.

### 6-3. `<label>`

- label이 가르키는 input과 그 label은 가까이 두자. - [PR 156](https://github.com/woowacourse/javascript-lotto/pull/156#commitcomment-68209662)

### 6-4. `<fieldset>` `<legend>`

- [[#146]](https://github.com/woowacourse/javascript-lotto/pull/146#discussion_r820173289) [`fieldset`](https://developer.mozilla.org/ko/docs/Web/HTML/Element/fieldset)을 사용하면 여러 `input`과 하나의 `label`을 묶어줄 수 있습니다. 이는 하나의 `form`안에 두는 것이 일반적입니다.

- [#129] fieldset과 legend 활용 예시

  - [MDN] `<fieldset>` 요소는 HTML 양식 속에서 그룹을 만들 수 있으며 `<legend>` 요소로 그룹의 설명(제목)을 제공할 수 있습니다. fieldset 태그는 주로 form 내부에 사용한다.

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

### 6-5. z-index

- [#130] position 속성이 지정된 엘리먼트보다 position 속성이 지정되지 않은 엘리먼트가 먼저 렌더링 되어서 따로 z-index 설정을 할 필요가 없다.

  - 따라서 position이 absolute, fixed, sticky 등은 position이 지정되지 않은 엘리먼트보다 후에 렌더링한다.
  - 쌓임맥락으로 인해 z index 설정이 필요 없고 쌓임맥락 기준으로 그래픽 레이어가 렌더링되기 때문
  - 기본적인 position 설정을 안해주면 static이 되는데, static은 쌓임 맥락이 없으므로 가장 바닥에 쌓이고, 쌓임 맥락은 그 위로 쌓이기 때문에 그래픽 레이어 생성시 앞서 보이기 때문이다. [관련 링크](https://developer.mozilla.org/ko/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context)

- [#164] 불필요한 z-index를 사용하지 않는다.

  - z-index를 1로 둔 이유가 있을까요?
    - 만약 1로 두지 않았다면 어떻게 되었을까요?
    - 쌓임의 맥락에 대해 한 번쯤 살펴볼 필요가 있다.

- [#125] modal은 보통 body 태그의 맨 마지막에 둔다. **z-index를 굳이 설정하지 않아도 자연히 맨 위로 올라갈 수 있기 때문이다.**

### 6-6. !important 지양

- [#145] !important는 지양
  - CSS 케스케이딩에 대해 명확하게 확인해볼 필요가 있음.

### 6-7. 단위

- [#145] vw, vh 와 em, rem 의 차이를 알아볼까요?

- 현재 실행중인 스크린 크기에 맞춰 상대적 크기를 반환하겠다는 의미의 vw, vh

  - vw
    - viewport width
    - 1vw는 viewport의 넓이의 1%와 같다
  - vh
    - viewport height
    - 1vh는 viewport의 높이의 1%와 같다

- em과 rem은 font-size 속성 값에 비례해서 결정되는 상대단위
  - em
    - 해당하는 단위가 사용되고 있는 요소의 font-size 속성 값이 기준이된다.
  - rem
    - 최상위 요소의 font-size 속성 값이 기준이된다.

### 6-8. css variable

- [#164] color 값은 css 변수로 관리해보자!

  ```css
  :root {
    --color-primary: #00bcd4;
    --color-light-gray: #b4b4b4;
    --color-white: #ffffff;
    --color-table-border: #c8c8c8;
  }
  ```

- [[#142]](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820071137) 앱에서 중복적으로 사용되는 CSS는 변수로 [분리](https://developer.mozilla.org/ko/docs/Web/CSS/:root)해주자

- [[#120]](https://github.com/woowacourse/javascript-lotto/pull/120#discussion_r820346138)색상 표기는 통일성이 있게 해보자.

### 6-9. cursor: not-allowed

- [[#142]](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820072764) 버튼이 disable 상태일 때 `cursor: not-allowed;`를 적용해줘야할까?
  - `cursor: not-allowed;`을 적용했을 시 디자인적인 문제가 있다.
  - 대부분의 서비스에서 찾아볼 수 없다.
  - bootstrap에서 위 같은 문제로 삭제된 [전례](https://github.com/twbs/bootstrap/issues/22222)가 있다.

### 6-10. CSS 방법론

- [[#136]](https://github.com/woowacourse/javascript-lotto/pull/136#issuecomment-1060243153) CSS에도 [방법론](https://whales.tistory.com/33)이 존재한다.
  - BEM 등

### 6-11. 0px 등은 `0`으로

- [[#142]](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820072820)
  길이에서 `0px`을 표기하고 싶을 때 `0`을 사용하자. 이는 선택이지만 [권장사항](https://www.w3.org/TR/css-values-3/#lengths) -

### 6-12. 부드러운 스크롤

- `scroll-behavior: smooth;` 딱 한 줄만으로 [부드러운 스크롤](https://gomakethings.com/smooth-scrolling-links-with-only-css/)을 구현할 수 있습니다.

## 7. 테스트

### 7-1. 사용자의 입장에서 테스트 코드 작성

- [#126] 사용자 편의성에 대한 좋은 조언 - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/126#pullrequestreview-900288536)
  - **먼저 사용자가 무엇을 해야할지 직관적으로 바로 알 수 없다면 개발자의 잘못**이라고 생각합니다
    직관적으로 알 수 있는 UI가 먼저이고 그럴 수 없는 경우라면 어떻게 해야할지 구체적이고 상세하게 안내해야 된다고 생각합니다 그리고 **악의적인 초등학생이 깨트리려고 해도 절대 깨지지 않는 앱을 만들어야 된다고 생각합니다** (이건 Bsidesoft의 맹대표님 강의 중에 들은 교훈입니다)
    그래서 **`성공하는 일부 케이스만 테스트하는게 아니라 에러를 더 많이 발생시키고 그에 대한 예외처리를 상세히 기술해서 사용성이 깨지는 경우를 방지하려고 노력합니다`**

### 7-2. 중복 정리

- [#132] 모든 테스트 케이스마다 cy.visit('./index.html');가 있는데요 이는 before으로 따로 빼도 좋을 것 같습니다. [before/beforeEach](https://docs.cypress.io/guides/core-concepts/writing-and-organizing-tests#Hooks)

  - before : 같은 describe 안에서 테스트가 많더라도 단 한 번만 실행되는 경우 사용
    cy.visit()의 경우 한 번 방문한 후 계속해서 동작을 이어나갈 수 있다.

  - beforeEach : 같은 describe 안에서 테스트별로 각각 한 번씩 사용되어야할 경우 사용

### 7-3. 테스트가 불필요한 경우

- [#149] 테스트를 위한 분리는 지양하기. 하지만 함수가 하나의 행동만 해야된다는 원칙에 위배된다면 분리해는게 좋은 방향성이라고 생각하는데 단일 행동을 하게 작성하다보면 테스트하기 좋은 구조가 나온다.
- [#110] 변경으로 인해 사이드 이펙트가 없고 해당 로직이 변경되지 않는다는 확신이 있다면 테스트 할 필요성이 떨어진다.

- [#125] #으로 은닉한 메소드의 경우에는 유닛 테스트를 할 수 없다. - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/125#pullrequestreview-899701248)

  - 리뷰어 답변 : **원래부터 private method는 테스트할 수 없도록 하는게 객체지향에서의 본래 취지**입니다. "객체 내부 로직에 대해서는 궁금하지 않고 오직 밖으로 드러난 것만 잘 보이면 된다"는 차원이거든요. '단위테스트'라고 할 때의 '단위'가 함수 내지 메소드단위일 경우도 있지만, '객체'를 하나의 단위로 인식하는게 일반적입니다. 이 객체(인스턴스)에 대한 테스트는 오직 public method를 통해서만 이뤄지도록 하면 되는 것이에요. **public method만으로 기존 테스트를 정상적으로 수행할 수 없다면, 테스트 케이스를 수정하셔야 합니다.**

- [#145] 의미 없는 테스트 코드는 지워준다.
  - 유효성 체크를 처음부터 모두 짜놓고 코드를 구현하겠지만, 코드 구현 후 테스트 코드로 돌아왔을 때 막상 필요없는 테스트라면 지우는게 맞지 않을까 싶습니다!

### 7-4. 단위테스트다운 것

- [#150] [단위테스트vs인수테스트vs통합테스트](https://tecoble.techcourse.co.kr/post/2021-05-25-unit-test-vs-integration-test-vs-acceptance-test/), 하나의 테스트를 위해 여러가지 함수를 불러와서 실행시킨 후, 결과를 테스트한다면 단위테스트가 아니다.

### 7-5. 여러 경우의 수

- [#141] 테스트코드에는 경계값 및 여러 경우의 수를 다 넣고 설명도 상세히 쓰자.
  - "테스트코드만 봐도 1001, 1000.5, 1500 같은 수들이 다 괜찮을지 어떨지 알 수 있게 해주시면 좋겠어요."

### 7-6. mocking

- [[#140]](https://github.com/woowacourse/javascript-lotto/pull/140#issuecomment-1060418153) 랜덤을 위한 테스트에는 `mock`을 사용해보자.

- [#149] jest의 spyOn 메서드를 통해 특정 object의 key에 해당하는 함수를 mocking하기

```javascript
import * as Utils from "../utils/util";

jest.spyOn(Utils, "generateRandomInRange");
// ReturnValue chaining하여 정의
```

- [#151, #135] jest는 함수의 반환값을 mocking할 수 있다. 예를 들어, lotto 번호를 생성하는 메서드를 `spyOn`이라는 jest함수를 활용하여 mocking할 수 있다.

  - 또한, 당첨 결과 확인 테스트케이스를 커버하고 싶다면, `mockReturnValueOnce`를 체이닝하여 사용하면 호출 시마다 원하는 로또 번호를 입력할 수 있다.

    - [리팩토링한 해당 커밋](https://github.com/woowacourse/javascript-lotto/pull/151/commits/1c301f1fef6ac1133298f3c384ee5fda89ae7268)

    ```js
    // model의 메서드
    export default class Model {
      #cash = 0;
      #lottoList = [];
      #winningLottoQuantity = {
        "3개": 0,
        "4개": 0,
        "5개": 0,
        "5개+보너스볼": 0,
        "6개": 0,
      };

      buyLotto(cash) {
        this.#lottoList = [];
        for (let i = 0; i < cash / LOTTO_PRICE; i++) {
          this.#lottoList.push(this.makeLottoNumbers());
        }
      }

      makeLottoNumbers() {
        return shuffle(
          makeAllLottoNumbers(LOTTO_RULE.MIN_NUMBER, LOTTO_RULE.MAX_NUMBER)
        ).slice(0, LOTTO_RULE.NUMBERS_COUNT);
      }
    }
    ```

    ```js
    // 테스트코드
    describe("당첨 결과 확인 테스트", () => {
      test("결과 확인하기 버튼을 누르면, 당첨 갯수와 수익률이 정확히 계산된다", () => {
        const pickedNumber = [4, 5, 6, 7, 8, 9, 10];

        const model = new Model();
        // 해당 메서드를 mocking하여 랜덤 테스트를 조작한다.
        model.makeLottoNumbers = jest
          .fn()
          .mockReturnValueOnce([1, 2, 3, 4, 5, 6])
          .mockReturnValueOnce([4, 5, 6, 7, 8, 9])
          .mockReturnValueOnce([4, 5, 6, 7, 8, 10])
          .mockReturnValueOnce([7, 8, 9, 10, 11, 12])
          .mockReturnValueOnce([13, 14, 15, 16, 17, 18]);

        model.setCash(lottoQuantity * LOTTO_PRICE);
        model.buyLotto(lottoQuantity);
        model.setWinningLottoQuantity(pickedNumber);

        const winningLottoQuantity = model.getWinningLottoQuantity();
        expect(winningLottoQuantity["3개"]).toBe(2);
        expect(winningLottoQuantity["4개"]).toBe(0);
        expect(winningLottoQuantity["5개"]).toBe(0);
        expect(winningLottoQuantity["5개+보너스볼"]).toBe(1);
        expect(winningLottoQuantity["6개"]).toBe(1);
        expect(model.calculateProfitRatio()).toBe(40600200);
      });
    });
    ```

## 8. 설계

### 8-1. 커스텀 이벤트

- [#121] 커스텀 이벤트를 활용하여 View 이벤트를 바인딩한 MVC 패턴의 경우, 커스텀 이벤트명으로 이벤트 발생을 개발자가 '의도한 시점에서 자유롭게' 이벤트를 일으킬 수 있다.

### 8-2. 클로저 패턴

- [#121] 클로저 패턴(또는 커링 함수)은 네트워크 요청 데이터 캐싱과 같이 자유변수의 참조값을 캐싱하고자 할 때 많이 쓰고, 스크롤이벤트에 디바운스를 걸 때 활용하기도 한다.

### 8-3. 객체들의 의존 관계

- [#143] 객체들의 의존 관계를 그림으로 그려보고, 예를 들어 "A객체가 B객체를 꼭 알아야 할까? A객체의 역할은 a인데, b,c 역할에서 수행할 법한 기능을 포함해서 역할이 모호해지네?"등과 같은 고민을 거치면서, 구조를 개선해보자.

### 8-4. MVC 패턴

- [[#142]](https://github.com/woowacourse/javascript-lotto/pull/142#issuecomment-1059778168) (_데이터 변경이 없는 이벤트에 대한 처리는 View에서 해야할까요? Controller에서 해야할까요?_)
  - Controller를 통해 이벤트를 처리할 필요가 없다면 View가 적절할 수 있다.

### 8-5. 역할

- [[#123]](https://github.com/woowacourse/javascript-lotto/pull/123#pullrequestreview-900065374) 구조가 맡는 역할이 모호해지면, 코드가 길어지고 가독성이 떨어진다.

### 8-6. 디렉토리

- [[#136]](https://github.com/woowacourse/javascript-lotto/pull/136#discussion_r820087835) 하나의 `index.js`에 많은 `import`를 한다면 가독성이 떨어질 수 있다. 만약 여러개의 View를 하나의 `index.js`에서 `import`하고 있다면 View 디렉토리에 새로운 `index.js`를 만들고 이를 `import`해주는 방식을 고려해보자.

## 9. 기타

### 9-1. 수학적 계산에 대한 설명을 주석으로

- [#122] 수학적 계산이 들어간 부분은 주석을 달면 좋다. - [바로가기](https://github.com/woowacourse/javascript-lotto/pull/122/files/0148a8ca0b693ee52474deb5dd8dc8eac2a285eb#r819291894)

  ```javascript
  // 수익률 = (총 상금 / 구매 금액)을 퍼센트 환산
  // 즉, 원금 상환 === 수익률 100%
  const rewardRate = totalReward / this.lottos.length / 10;
  ```

### 9-2. 좋은 커밋 메시지

- [[#123]](https://github.com/woowacourse/javascript-lotto/pull/123#pullrequestreview-901594861) 좋은 커밋 메시지에는 what과 why가 들어가 있어야 한다.
  - title에 what(무엇을 작성했는 지), 내용에 why(왜 이 커밋을 작성했는 지)

### 9-3. PR 리뷰 잘 받는 팁

- [[#123]](https://github.com/woowacourse/javascript-lotto/pull/123#pullrequestreview-901594861)
  커밋에서 피드백이 잘 반영되었다는 것을 리뷰어님께 알려드리고 싶다면, comment에 커밋 넘버를 남기자.
