# Level1 Lotto Step1(담당 PR 번호들) - 마르코

- 분석 담당 코드
  - 준찌 [#121](https://github.com/woowacourse/javascript-lotto/pull/121)
  - 유세지 [#141](https://github.com/woowacourse/javascript-lotto/pull/141)
  - 동키콩 [#143](https://github.com/woowacourse/javascript-lotto/pull/143)
  - 온스타 [#131](https://github.com/woowacourse/javascript-lotto/pull/131)
  - 위니 [#147](https://github.com/woowacourse/javascript-lotto/pull/147)
  - 자스민 [#135](https://github.com/woowacourse/javascript-lotto/pull/135)
  - 아놀드 [#127](https://github.com/woowacourse/javascript-lotto/pull/127)
  - 빅터 [#152](https://github.com/woowacourse/javascript-lotto/pull/152)

## 2. 피드백 정리

### 함수분리

- [#141] 그저 함수가 긴 것 같아서 불필요하게 분리된 짧은 함수는 가독성을 떨어트린다.

  - "확인해보니 함수내 '줄수 제한'은 요구사항이 아닙니다. 애초에 모든 함수가 15줄 이내여야 한다는 건 너무 가혹하기도 하고, 도리어 불필요한 짧은 함수를 남발하여 가독성을 떨어뜨리는 결과를 초래할 수도 있어요."

- [#131] 함수 분리를 "같은 로직이 반복되어 중복이 발생할 경우에 한정"한다. 긴밀하게 연결된 메서드라면 분리를 위한 분리는 할 필요가 없다.

### 테스트코드

- [#141] 테스트코드에는 경계값 및 여러 경우의 수를 다 넣고 설명도 상세히 쓰자.
  - "테스트코드만 봐도 1001, 1000.5, 1500 같은 수들이 다 괜찮을지 어떨지 알 수 있게 해주시면 좋겠어요."
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

### 상수화

- [#141] 여러 곳에서 자주 쓰이는 값들에 대해서만 고민하여 선택적으로 상수화를 하자.

### DOM

- [#141, #143] [setAttribute](https://developer.mozilla.org/en-US/docs/Web/API/Element/setAttribute) vs [classList](https://developer.mozilla.org/ko/docs/Web/API/Element/classList), style 처리를 위한 경우 setAttribute() 메서드보다는 classList 프로퍼티의 메서드(add, remove, toggle, contains, replace)를 사용하는 것이 성능상 더 낫고 편리한 것 같다. 또한, `this.elem.style.display = 'flex'`처럼 js에서 직접 css를 고치기보다는 css의 class를 활용하는 것이 낫다.

- [#147] dom 요소 탐색 시, root부터 탐색하지 말고 parent부터 탐색하는 방식이 성능상 더 좋다. [리팩토링한 해당 커밋](https://github.com/woowacourse/javascript-lotto/pull/147/commits/88122adf486354c6864f7f881c5584f930788444)

- [#135] 한글의 경우 keypress 이벤트에서 감지하지 못하는 문제가 있다.

### 설계 및 패턴

- [#121] 커스텀 이벤트를 활용하여 View 이벤트를 바인딩한 MVC 패턴의 경우, 커스텀 이벤트명으로 이벤트 발생을 개발자가 '의도한 시점에서 자유롭게' 이벤트를 일으킬 수 있다.

- [#121] 클로저 패턴(또는 커링 함수)은 네트워크 요청 데이터 캐싱과 같이 자유변수의 참조값을 캐싱하고자 할 때 많이 쓰고, 스크롤이벤트에 디바운스를 걸 때 활용하기도 한다.

- [#143] 객체들의 의존 관계를 그림으로 그려보고, 예를 들어 "A객체가 B객체를 꼭 알아야 할까? A객체의 역할은 a인데, b,c 역할에서 수행할 법한 기능을 포함해서 역할이 모호해지네?"등과 같은 고민을 거치면서, 구조를 개선해보자.

### 은닉화

- [#121] 외부에서 접근해서는 안 되는 메소드에 대해서 은닉화한다. 그 접근 가능 여부의 기준에는 "외부에서 접근했을 때 객체의 상태를 직접적으로 변경시키는가?" 또는 "외부에서 써도 안전한 메소드인가?" 등이 있다.

### 함수 리팩토링

- [#147] 배열에 push하는 for문을 Array.from()을 이용하여 리팩토링

  - 참고: [MDN- Array.from()과 화살표 함수 사용하기](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/from#array.from%EA%B3%BC_%ED%99%94%EC%82%B4%ED%91%9C_%ED%95%A8%EC%88%98_%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)
  - 리팩토링 예시

    - ```jsx
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

### 이벤트 위임 활용

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

### 표준 내장 객체

- [#135] `Number.isNaN`과 `전역 window.isNaN`의 차이
  - `전역의 isNaN`은 인수가 Number형이 아닌 경우, 그 값을 먼저 숫자로 강제하고, 그 다음 NaN인지 판단을 한다. `"37"`은 NaN이 아닌 숫자 `37`로 변환되므로 `false`가 반환된다. 그리고 헷갈리게도 `undefined`, `{}`, `'ok'`도 `true(=NaN)`가 반환된다.
    - [MDN-혼란스러운 특별 케이스 행동](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/isNaN#%ED%98%BC%EB%9E%80%EC%8A%A4%EB%9F%AC%EC%9A%B4_%ED%8A%B9%EB%B3%84_%EC%BC%80%EC%9D%B4%EC%8A%A4_%ED%96%89%EB%8F%99)
  - 하지만 `Number.isNaN`은 오직 숫자형이면서 NaN인 값만을 true를 반환한다. 예를 들어, `undefined`, `{}`, `'ok'` 같은 것들은 `false(=not NaN)`가 반환된다. 또한, `37`, `"37"`, `""`, `"true"`, `"null"`도 모두 `false`가 반환된다.
  - 따라서 보다 엄격하게 `NaN`(`Number.NaN`, `0/0`)만을 판별하고 싶다면 `Number.isNaN`을 사용하는게 좋을 것 같다.

### 긍정 조건문이 부정문보다 가독성에 좋다.

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

### 순수 함수

- [#127] 순수하지 않은 함수는 그 작동 과정을 확인하기 위해 다른 함수를 살펴야 해서 좋지 않을 수 있다.
