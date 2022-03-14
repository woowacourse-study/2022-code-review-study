# STEP2. 꼬재 담당 크루 PR 링크

---

- [#132꼬재](https://github.com/woowacourse/javascript-lotto/pull/132)
- [#138도리](https://github.com/woowacourse/javascript-lotto/pull/138)
- [#145후이](https://github.com/woowacourse/javascript-lotto/pull/145)
- [#144콤피](https://github.com/woowacourse/javascript-lotto/pull/144)
- [#124나인](https://github.com/woowacourse/javascript-lotto/pull/124)
- [#128비녀](https://github.com/woowacourse/javascript-lotto/pull/128)
- [#164코이](https://github.com/woowacourse/javascript-lotto/pull/134)

## 아키텍쳐 분석

---

<br />

### #132꼬재

---

#### LottoConsumer

로또 구매자 티켓 관련 담당

#### LottoSeller

로또 판매자 티켓 관련 담당

#### LottoApp

이벤트 바인딩과 전체 프로그램 로직을 담당

#### 그 외 파일

util, template, dom 요소를 함수로 가지고 있고 필요한 영역에서 import 후 사용

<br />

### #138도리

---

#### Lotto

로또 티켓 한 장을 생성하는 객체

#### PurchasedLotto

구매한 전체 로또를 저장하고 있음

#### LottoApp

이벤트 바인딩과 전체적인 프로그램 로직을 담당

#### view 폴더

view에 해당하는 로직과 dom 요소, templates들을 분리하여 관리

<br />

### #145후이, #124나인, #128비녀, #164코이, #144콤피

---

#### model

나인, 비녀 : 로또 한 장의 객체 / 유저가 구매한 로또들, 당첨 번호를 관리하는 객체
코이 : 유저가 구매한 로또들, 당첨 번호를 관리하는 객체
후이 : 로또 한 장의 객체 / 당첨 로또 한 장의 객체 / 유저가 구매한 로또를 관리하는 객체 / 유저가 구매한 로또의 당첨 결과를 관리하는 객체

후이)
js에서는 지원하지 않는 protected 사용 \_(언더바를 이용)

상속을 통해 로또 한장의 객체와 당첨 로또 한 장의 객체에서 overriding해서 사용해 중복된 코드 제거

```js
export default class Lotto {
  // _는 protected 입니다.
  _lottoNumbers = [];

  generate() {
    const { MAX_RANDOM_NUMBER: MAX, MIN_RANDOM_NUMBER: MIN } = LOTTO_SETTING;
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

#### controller

전체적인 프로그램 로직을 담당

- 이벤트 바인딩
- validate

#### views 폴더

각각의 view 별로 파일을 나눠 view를 관리

#### utils

document에서 돔요소를 찾는 경우와 부모 엘리먼트에서 돔 요소를 찾을 수 있게 자동화 시킨 부분이 인상 깊어 코드를 가져왔습니다.

```js
export const $ = (parentElement, childSelector = null) => {
  const target = childSelector || parentElement;
  const $parent = childSelector
    ? parentElement
    : document.getElementById(SELECTOR.ID.APP);

  return $parent.querySelector(target);
};

export const $$ = (parentElement, childSelector = null) => {
  const target = childSelector || parentElement;
  const $parent = childSelector
    ? parentElement
    : document.getElementById(SELECTOR.ID.APP);

  return $parent.querySelectorAll(target);
};
```

<br />

에러 메시지를 관리할 때 줄 바꿈을 활용하는 부분이 인상 깊어 코드를 가지고 왔습니다.

```js
export const ERROR_MESSAGE = {
  NEGATIVE_INPUT_ERROR:
    "양수를 입력해 주세요! \n (please enter positive number)",
  NOT_INTEGER_INPUT_ERROR:
    "정수를 입력해 주세요! \n (please enter integer number)",
  NOT_MUTIPLE_THOUSAND:
    "1000단위로 입력해 주세요! \n (please enter number that is mutiples of thousand)",
};
```

<br/>

## 피드백 정리

### CSS

---

#### [#145] !important는 지양

- CSS 케스케이딩에 대해 명확하게 확인해볼 필요가 있음.
  <br />정확하게 정리되어 있는 블로그를 찾지는 못함.

#### [#145] vw, vh 와 em, rem 의 차이를 알아볼까요?

현재 실행중인 스크린 크기에 맞춰 상대적 크기를 반환하겠다는 의미의 vw, vh

- vw

  - viewport width
  - 1vw는 viewport의 넓이의 1%와 같다

- vh
  - viewport height
  - 1vh는 viewport의 높이의 1%와 같다

<br />

em과 rem은 font-size 속성 값에 비례해서 결정되는 상대단위

- em

  - 해당하는 단위가 사용되고 있는 요소의 font-size 속성 값이 기준이된다.

- rem
  - 최상위 요소의 font-size 속성 값이 기준이된다.

#### [#145] 의미있는 단위로 줄 간격을 줘볼까요?

- 코드 라인수에 제한을 두는 것 보다, 의미있는 단위로 줄 간격을 나눠주는 것이 가독성을 높이는데 더 효율적이다.

#### [#164] color 값은 css 변수로 관리해보자!

```css
:root {
  --color-primary: #00bcd4;
  --color-light-gray: #b4b4b4;
  --color-white: #ffffff;
  --color-table-border: #c8c8c8;
}
```

#### [#164] 불필요한 z-index를 사용하지 않는다.

- z-index를 1로 둔 이유가 있을까요?
  <br />만약 1로 두지 않았다면 어떻게 되었을까요?
  <br />쌓임의 맥락에 대해 한 번쯤 살펴볼 필요가 있다.

### JS

---

#### [#132] from을 사용하면 fill,map 두가지 메소드를 사용하지 않고 하나의 메소드 구현할 수 있습니다. 더불어 현재 map에서 첫번째 파라미터 \_를 사용하지않고 있네요!

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

#### [#145] setter 가 아닌 addMoney 함수가 더 어울리네요! 그리고 setter 는 안만들수록 좋으니 더더욱 addMoney 가 좋겠네요!

- setter는 가급적 사용하지 않는게 좋다.
- setter 대신에 생성자에 값을 넘겨주거나, 빌더 패턴을 사용하는 방법이 있다.

빌더 패턴이란..?!

#### [#145] WinningLotto 가 Lotto 를 상속해보면 어떨까요?

- 위에 코멘트 남긴대로 pushNumberIntoPickedNumbers 가 필요없으니 pushPickNumbers 같은 함수가 생길테니 재사용가능하고, generate 는 재정의하고, pickedNumbers 를 활용하면 bonusNumber 관련 로직만 추가하면 될 것 같은데요! 어떤가요?

  ```js
  export default class Lotto {
    // _는 protected 입니다.
    _lottoNumbers = [];

    generate() {
      const { MAX_RANDOM_NUMBER: MAX, MIN_RANDOM_NUMBER: MIN } = LOTTO_SETTING;
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

#### [#145] 상속에 대한 리뷰어님의 생각

- 아하 상속은 항상 장단점이 있죠! 한 함수만을 추상화한다해도 여러 자식 클래스에서 상속할 부모 클래스라면 그럴 가치는 있죠!
  <br />
  다만 프론트는 서버에 비해 변경 가능성이 크기 때문에 (예를 들어 숫자 인풋에서 문자 인풋으로 바뀔 수도 있죠?) 뷰 단까지 상속 구조를 써야 하나..? 내가 실무에서 뷰 단을 상속한 적이 있었나..? 라고 생각했을 땐 케이스가 별로 없네요 😅
  <br />
  아마 나중에 api 단을 구현하실 때 상속할 일이 좀 나올거에요! 그 때 더 고민하면서 적용해보시죠!

#### [#144] 재사용되지 않는 View 들은 굳이 selector 들을 외부에서 주입해줘야할까? 라는 생각이 들긴 하는군요 🤔

- 재사용되지 않는 인스턴스는 필요한 곳에서 생성해서 사용하는 것이 좋다. 맴버 변수로 관리할 필요가 없기 때문

#### [#144] 이건 fill 을 이용해 초기화할 수도 있을 거 같습니다! (수정할 필요는 없어용)

```js
const output = Array.from(
  { length: LOTTO_SETTING.RACKING_START_NUMBER },
  () => 0
);
const output = Array(LOTTO_SETTING.RACKING_START_NUMBER).fill(0);
```

#### [#144] 오... 이벤트를 한 번만 걸어주고 자동으로 지워주는 게 있군요! 하나 더 배워갑니다ㅎ

```js
$element.addEventListener(eventType, callback, {
  once: true,
});
```

### 컨벤션

---

#### [#144] SELECTOR를 상수화 할 경우 class선택자와 id 선택자를 분리하여 작성해주는게 좋다.

- 물론, 당연히 SELECTOR, DOM_NAME 의 쓰임새에 대한 '약속'을 했지만, 휴먼에러는 언제든 발생할 수 있으니까요.
  ID SELECTOR 인데 CLASS 로 생각해서 iterator 문법을 사용할 수도 있고요 ;ㅁ;

#### [#132] MONEY를 객체로 만들 필요가 있을까요?ㅎ 혹시 제가 생각못한 확장성 부분이 있을까요?

- 현재 프로젝트에서는 모두 객체로 사용하지만 실제 상수는 enum, array, 값 등 다양하게 사용됩니다. 그렇기때문에 통일성을 맞추기보다 그 역할에 맞게 작성하시는게 좋을 것 같습니다 ㅎ

```js
export const MONEY = {
  STANDARD: 1000,
};
```

```js
export const MONEY_STANDARD = 1000;
```

### 테스트

---

#### [#145] 의미 없는 테스트 코드는 지워준다.

- 유효성 체크를 처음부터 모두 짜놓고 코드를 구현하겠지만, 코드 구현 후 테스트 코드로 돌아왔을 때 막상 필요없는 테스트라면 지우는게 맞지 않을까 싶습니다!

#### [#132] 모든 테스트 케이스마다 cy.visit('./index.html');가 있는데요 이는 before으로 따로 빼도 좋을 것 같습니다ㅎ

[before/beforeEach](https://docs.cypress.io/guides/core-concepts/writing-and-organizing-tests#Hooks)

- before : 같은 describe 안에서 테스트가 많더라도 단 한 번만 실행되는 경우 사용
  <br />
  cy.visit()의 경우 한 번 방문한 후 계속해서 동작을 이어나갈 수 있다.

- beforeEach : 같은 describe 안에서 테스트별로 각각 한 번씩 사용되어야할 경우 사용
