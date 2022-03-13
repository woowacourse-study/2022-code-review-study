# LEVEL 1 행운의 로또 STEP 2 - 무비😎

## 분석 담당 코드

- 마르코 [PR 142](https://github.com/woowacourse/javascript-lotto/pull/142) X 무비 [PR 140](https://github.com/woowacourse/javascript-lotto/pull/140)
- 밧드 [PR 146](https://github.com/woowacourse/javascript-lotto/pull/146) X 우디 [PR 156](https://github.com/woowacourse/javascript-lotto/pull/156)
- 앨버 [PR 136](https://github.com/woowacourse/javascript-lotto/pull/136) X 결 [PR 120](https://github.com/woowacourse/javascript-lotto/pull/120)
- 샐리 [PR 100](https://github.com/woowacourse/javascript-lotto/pull/100)

<br />
<br />

## 피드백 정리

😛 링크를 누르면 해당 discussion으로 이동! 😛

<br />

### MVC 패턴

- (_데이터 변경이 없는 이벤트에 대한 처리는 View에서 해야할까요? Controller에서 해야할까요?_) \*
  1. Controller를 통해 이벤트를 처리할 필요가 없다면 View - [PR 142](https://github.com/woowacourse/javascript-lotto/pull/142#issuecomment-1059778168)
  2. Web API를 활용할 수 있다면 굳이 CustonEvent는 사용할 필요가 없다고 생각합니다. - [PR 136](https://github.com/woowacourse/javascript-lotto/pull/136#issuecomment-1059902711)

<br />

### 구조

- 구조가 맡는 역할이 모호해지면, 코드가 길어지고 가독성이 떨어진다. - [PR 123](https://github.com/woowacourse/javascript-lotto/pull/123#pullrequestreview-900065374)

<br />

### CSS

- 앱에서 중복적으로 사용되는 CSS는 변수로 [분리](https://developer.mozilla.org/ko/docs/Web/CSS/:root)해주자 - [PR 142](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820071137)
- 버튼이 disable 상태일 때 `cursor: not-allowed;`를 적용해줘야할까? - [PR 142](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820072764) \*
  - `cursor: not-allowed;`을 적용했을 시 디자인적인 문제가 있다.
  - 대부분의 서비스에서 찾아볼 수 없다.
  - bootstrap에서 위 같은 문제로 삭제된 [전례](https://github.com/twbs/bootstrap/issues/22222)가 있다.
- 길이에서 `0px`을 표기하고 싶을 때 `0`을 사용하자. 이는 선택이지만 [권장사항](https://www.w3.org/TR/css-values-3/#lengths) - [PR 142](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820072820)
- `scroll-behavior: smooth;` 딱 한 줄만으로 [부드러운 스크롤](https://gomakethings.com/smooth-scrolling-links-with-only-css/)을 구현할 수 있습니다. - [PR 140](https://github.com/woowacourse/javascript-lotto/pull/140#pullrequestreview-901771377) \*
- CSS에도 [방법론](https://whales.tistory.com/33)이 존재한다. - [PR 136](https://github.com/woowacourse/javascript-lotto/pull/136#issuecomment-1060243153) \*
- 색상 표기는 통일성이 있게 해보자. - [PR 120](https://github.com/woowacourse/javascript-lotto/pull/120#discussion_r820346138)

  ```css
  background-color: white;
  background-color: #00bcd4;
  /* 색상 이름, 16진수로 작성하는 방법 .. 두 가지의 방법이 혼용되어 사용되고 있었음. */
  ```

<br />

### HTML

- [`fieldset`](https://developer.mozilla.org/ko/docs/Web/HTML/Element/fieldset)을 사용하면 여러 `input`과 하나의 `label`을 묶어줄 수 있습니다. 이는 하나의 `form`안에 두는 것이 일반적입니다. - [PR 146](https://github.com/woowacourse/javascript-lotto/pull/146#discussion_r820173289) \*
- template가 반복될 때 `map`을 사용해주자. - [PR 146](https://github.com/woowacourse/javascript-lotto/pull/146#discussion_r820173527)
- label이 가르키는 input과 그 label은 가까이 두자. - [PR 156](https://github.com/woowacourse/javascript-lotto/pull/156#commitcomment-68209662)
- (_DOM은 HTML에서 그려야할까 vs JS에서 그려야할까_) - [PR 120](https://github.com/woowacourse/javascript-lotto/pull/120#discussion_r817445951)
  - 작성자의 입장 : 유저의 액션에 의해 DOM을 추가하거나 변경하는 것은 JS가 담당,유저의 액션과 관계없이 항상 존재해야하는 DOM 요소는 HTML에서 작성해야 한다고 생각
  - 리뷰어의 입장 : JS자체가 DOM을 조작하기 위해 사용하는 언어인데 JS에서 DOM조작을 최소화하기보다는 좀 더 효과적으로 JS를 활용해 DOM을 조작하는게 좋은 것 같다고 생각

<br />

### Eslint

- [consistent-return](https://eslint.org/docs/rules/consistent-return)규칙은 함수 안에서 일관되게 `return`을 해주도록 강제한다. 하지만 `return`을 해주지 않아도 되는 상황에서 규칙때문에 억지로 `return`해주는 경우가 있을 수 있다. [코드 분기에 따라 다른 동작을 하게 하려면 이 규칙을 꺼주는 것이 안전하다.](https://eslint.org/docs/rules/consistent-return#when-not-to-use-it) - [PR 142](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820124484) \*

<br />

### Class

- 생성자에서 해줘도 될 일을 굳이 함수로 빼줄 필요는 없다. - [PR 146](https://github.com/woowacourse/javascript-lotto/pull/146#issuecomment-1060542912)

<br />

### Function

- Boolean을 `return`할 경우 조건을 `return`할 수 있는지 확인해주자. - [PR 142](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820095323)
- 두개 이상의 인자를 받을 경우 객체 형태로 넣어주세요. [인자는 2개 이하가 이상적입니다.](https://github.com/qkraudghgh/clean-code-javascript-ko#%ED%95%A8%EC%88%98functions) - [PR 140](https://github.com/woowacourse/javascript-lotto/pull/140#discussion_r820068616)
  - 매개변수가 많으면 가독성 측면에서 좋지 않고, 유지보수할 때 실수를 유발할 수 있다. - [PR 136](https://github.com/woowacourse/javascript-lotto/pull/136#discussion_r820088741)

<br />

### 고차 함수

- 객체의 프로퍼티를 이용하여 계산할 때 [`reduce`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)를 사용해보자. - [PR 142](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820230362) \*

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

<br />

### 유용한 문법

- `try-catch`의 장점은 오류가 있을 경우 즉시 실행을 멈춘다는 점과 에러 핸들링을 할 수 있다는 점이 있다. - [PR 136](https://github.com/woowacourse/javascript-lotto/pull/136#discussion_r820396206)
- `Array.from`을 통해 `map`의 작업도 동시에 할 수 있다. - [PR 136](https://github.com/woowacourse/javascript-lotto/pull/136#discussion_r820091000) \*

  ```js
  console.log(Array.from([1, 2, 3], x => x + x));
  // expected output: Array [2, 4, 6]
  ```

<br />

### Array

- 배열의 마지막 값은 숫자로 명시하지 말고, `length - 1`을 사용해보자 - [PR 142](https://github.com/woowacourse/javascript-lotto/pull/142#discussion_r820108518)

<br />

### Object

- 객체를 복사하는 방법에는 `JSON.parse(JSON.stringify(object))`가 있다. 주의할 점은 객체안의 메서드는 복사되지 않는다. - [PR 156](https://github.com/woowacourse/javascript-lotto/pull/156#discussion_r820578491) \*

<br />

### 변수

- 변수가 많으면 관리포인트가 많아진다. 공통으로 관리할 수 있는 부분은 배열과 객체를 이용하자. - [PR 136](https://github.com/woowacourse/javascript-lotto/pull/136#discussion_r820089422)

<br />

### 이벤트 핸들러

- 비슷한 방식으로 여러 요소의 이벤트를 처리해야할 때 [이벤트 위임](https://ko.javascript.info/event-delegation)을 사용해보자. - [PR 146](https://github.com/woowacourse/javascript-lotto/pull/146#discussion_r820577052) \*

<br />

### 네이밍

- `init`은 가장 처음을 위한 초기화에 가깝다. (reset이 아님) - [PR 156](https://github.com/woowacourse/javascript-lotto/pull/156#discussion_r820520699) \*
- `submit`은 `form`에 있는 데이터를 제출한다는 의미로 많이 사용된다. toggle 같은 함수에서 붙일 필요가 없다. (예시 - `submitLottoToggle()`라는 이름의 함수) - [PR 120](https://github.com/woowacourse/javascript-lotto/pull/120#discussion_r817495192)

<br />

### 디렉토리

- 하나의 `index.js`에 많은 `import`를 한다면 가독성이 떨어질 수 있다. 만약 여러개의 View를 하나의 `index.js`에서 `import`하고 있다면 View 디렉토리에 새로운 `index.js`를 만들고 이를 `import`해주는 방식을 고려해보자. - [PR 136](https://github.com/woowacourse/javascript-lotto/pull/136#discussion_r820087835) \*

<br />

### 테스트

- 랜덤을 위한 테스트에는 `mock`을 사용해보자. - [PR 140](https://github.com/woowacourse/javascript-lotto/pull/140#issuecomment-1060418153) \*

<br />

### Commit

- (commitlint를 사용했을 때의 gitmoji) gitmoji 자체에 type의 의미를 담기 때문에 별도의 type text를 쓰지 않아도 된다. 😮 - [PR 123](https://github.com/woowacourse/javascript-lotto/pull/123#issuecomment-1059093455) \*
- 좋은 커밋 메시지에는 what과 why가 들어가 있어야 한다. - [PR 123](https://github.com/woowacourse/javascript-lotto/pull/123#pullrequestreview-901594861) \*
  - title에 what(무엇을 작성했는 지), 내용에 why(왜 이 커밋을 작성했는 지)

<br />

### PR 리뷰 잘 받는 팁

- 커밋에서 피드백이 잘 반영되었다는 것을 리뷰어님께 알려드리고 싶다면, comment에 커밋 넘버를 남기자. - [PR 123](https://github.com/woowacourse/javascript-lotto/pull/123#pullrequestreview-901594861)
