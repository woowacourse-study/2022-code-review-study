# Level1 Lotto Step1(담당 PR 번호들) - 록바

- 분석 담당 코드
  - 후이 [#93](https://github.com/woowacourse/javascript-lotto/pull/93)
  - 콤피 [#98](https://github.com/woowacourse/javascript-lotto/pull/98)
  - 돔하디
  - 민초
  - 블링
  - 태태
  - 하리
  - 록바
  - 코카콜라
  - 안

## 아키텍처 분석(desc: 담당한 4개의 소프트웨어의 아키텍처를 간단히 분석하고 설명합니다)

### [#93] 후이, 콤피

파일 구조도는 MVC 패턴을 따르고 있다.

Model - Lotto, LottosModel
View - MoneyInputView, LottoListView
Controller - LottoController

#### Controller

- Controller는 Model, View에게 지시하는 역할을 수행하고 있다.

  - Model, View 인스턴스를 생성 후, 멤버 변수로 가지고 있다.

    - 인스턴스 생성을 constructor()에서 하지 않고 private 필드에서 진행하였다.

    ```javascript
    class LottoController{
      #MoneyInputView = new MoneyInputView($(`.${SELECTOR.CLASS.LOTTO_MONEY_SECTION}`));
      #LottoListView = new LottoListView($(`.${SELECTOR.CLASS.LOTTO_LIST_SECTION}`));
      #LottosModel = new LottosModel();

      constructor() {...}
    }
    ```

    - constructor()에서 이벤트를 바인딩하는 함수를 호출하고 있다.
      - dom을 View에서 관리하다 보니, Controller에서 수행햐는 함수를 콜백함수로 전달하고 있다.
    - Controller는 메인 로직인, 입력받는 금액을 바탕으로 Model에 접근하여 상태 데이터를 간접적으로 변경하고 View에 화면의 변화를 주도록 지시하고 있다.

#### View

- View는 MoneyInputView와 LottoListView로 구성되어 있다.

  - Controller에서 인스턴스 생성시 전달한 dom 객체를 멤버변수로 할당하고 있다.

  ```javascript
  #container;

  constructor($element){
    this.#container = $element;
  }
  ```

  - 특정 영역을 show/hide 및 toggle은 classList를 활용해 변화를 담당했다.
  - View는 Model, Controller에 직접 접근하는 경우가 없다.

#### Model

- Model은 Lotto, LottosModel로 구성되어 있다.
  - Lotto 모델은 pickNumbers(선택된 로또번호들)을 멤버 변수로 가지고 있다. Set() 자료형을 사용했다.
  ```javascript
  #pickNumbers = new Set();
  ```
  - LottosModel은 lottos(로또 객체들)을 멤버 변수로 가지고 있다. 배열 [] 자료형을 사용했다.
  - Controller에서 Model의 상태 데이터에 접근하기 위해서 getter를 사용하고 있다.

#### 인상 깊은 점

- Array.from 활용 부분

```javascript
const lottoCount = inputMoney / LOTTO_SETTING.PRICE;

Array.from({ length: lottoCount }, () => {
  const lottoInstance = new Lotto();
  lottoInstance.generateNumberList();

  this.#lottos.push(lottoInstance);
});
```

Array.from의 length 속성을 이용하고, Array.from의 스펙을 잘 이용한 것 같다.

<br>

- scss 사용

```css
$border-color: #b4b4b4;
$border-radius: 5px;
$primary-color: #00bcd4;
```

자주 쓰이는 속성값을 변수에 담아서 활용하였다.

---

## 피드백 정리

### 대분류(ex: 아키텍처, 함수/클래스, 컨벤션, DOM, 테스트 등)

### 아키텍처

- [#number]

#### 함수/클래스

- [#93] 토글 조작으로 인한 화면 show/hide 관련 함수

  ```javascript
  showLottoList() {
    this.#container.classList.add('show');
  }

  hideLottoList() {
    this.#container.classList.remove('show');
  }
  ```

  - toggle 이라는거는 show -> hide, hide -> show 를 번갈아가면서 하겠다는 것을 내포하고 있는데 show 가 왜 붙어야하는지 모르겠습니다!
    - showLottoList, hideLottoList -> toggleLottoList로 정정

<br>

### 컨벤션

- [#93] 혹시 class 네이밍 컨벤션 관련 검색 키워드 추천 해주실 만한게 있을까요?

  - (정답은 아니고, 제안) label과 input을 감싸는 애들은 **wrapper**로, 여러 컴포넌트를 감싸는 애들은 **container**로 한다.

<br>

- [#93] 함수 매개변수명 관련 코멘트
  ```javascript
  handleMoneyInputSubmit({ moneyInput });
  ```
  - moneyInput -> inputElment가 넘어오는 걸로 착각을 일으킨다.
  - moneyInput -> money 수정

<br>

- [#93] 배열을 할당받고 있는 변수명 네이밍 관련 코멘트
  - 배열은 보통 ~~s 또는 ~~List로 네이밍을 짓는다.

<br>

- [#93] 변수명 관련 코멘트
  - 현재, 두 가지 View의 멤버변수는 `this.#container = $element`로 되어있다.
  - [리뷰어님 답변] : container 같이 element 에 대한 변수명을 지을 때 number, string 과 같은 값을 의미하는 변수와 구분을 두면 가독성이 좋아질 것 같습니다. 예를 들어 변수명 뒤에 ~~~Element 를 붙인다던지, element 변수명 앞에는 $ 를 붙인다던지 등등 규칙을 정하자!

<br>

- [#98] [리뷰어 질문] : selector 도 모두 상수화하신 게 인상깊네요. 개인적으로는 대문자 상수를 많이 쓰게 되면 가독성이 오히려 떨어지는 느낌인데, 쓰면서 어떠셨나요~?
  - 콤피의 답변
    - 가독성은 좋지는 않다.
    - 추후 선택자를 수정사항이 생기면, 한 번에 변경이 가능하다.
    - vscode의 자동 표기법이 편하다.

<br>

- [#98] 아래 코드에서는 Object.freeze를 할 필요없을까?
  ```javascript
  export const LOTTO_SETTING = {
    MIN_RANDOM_NUMBER: 1,
    MAX_RANDOM_NUMBER: 45,
    PRICE: 1000,
    LOTTO_NUMBER_LENGTH: 6,
  };
  ```
  - 객체는 변경가능해지기도 해서 상수는 보통 문자열로 선언해 사용한다.
  - 변수명 자체를 대문자로 쓰는 편이라, Object.freeze를 하지 않는다.

<br>

### 테스트

- [#number]
