# Level1 Lotto Step1(담당 PR 번호들) - 록바

- 분석 담당 코드
  - 후이 [#93](https://github.com/woowacourse/javascript-lotto/pull/93)
  - 콤피 [#98](https://github.com/woowacourse/javascript-lotto/pull/98)
  - 돔하디 [#108](https://github.com/woowacourse/javascript-lotto/pull/108)
  - 민초 [#104](https://github.com/woowacourse/javascript-lotto/pull/104)
  - 블링 [#88](https://github.com/woowacourse/javascript-lotto/pull/88)
  - 태태 [#91](https://github.com/woowacourse/javascript-lotto/pull/91)
  - 하리 [#115](https://github.com/woowacourse/javascript-lotto/pull/115)
  - 록바 [#103](https://github.com/woowacourse/javascript-lotto/pull/103)
  - 코카콜라 [#111](https://github.com/woowacourse/javascript-lotto/pull/111)
  - 안 [#114](https://github.com/woowacourse/javascript-lotto/pull/114)

<br>

## 아키텍처 분석(desc: 담당한 4개의 소프트웨어의 아키텍처를 간단히 분석하고 설명합니다)

### [#93, #98] 후이, 콤피

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

<br>

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

<br>

#### Model

- Model은 Lotto, LottosModel로 구성되어 있다.
  - Lotto 모델은 pickNumbers(선택된 로또번호들)을 멤버 변수로 가지고 있다. Set() 자료형을 사용했다.
  ```javascript
  #pickNumbers = new Set();
  ```
  - LottosModel은 lottos(로또 객체들)을 멤버 변수로 가지고 있다. 배열 [] 자료형을 사용했다.
  - Controller에서 Model의 상태 데이터에 접근하기 위해서 getter를 사용하고 있다.

<br>

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

<br>

### [#103, #111, #115] 하리, 록바, 코카콜라

파일 구조도는 MVC 패턴을 따르고 있다.

Model - LottoNumbers, PurchasedLottos
View - View, PurchasedMoneyView, PurchasedLottoView, WinningNumberView
Controller - LottoMachineController

#### Controller

- Controller는 Model, View에게 지시하는 역할을 수행하고 있다.
  - constructor에서 Model, View 인스턴스를 생성 후, 멤버 변수로 가지고 있다.
  - **dom을 View에서 관리하고 있기 때문에, Controller에서 실행되어야 하는 함수를 콜백함수로 전달하는 것이 아닌, 부모 클래스인 View의 handlers(멤버 변수)에 저장한다.**

<br>

#### View

- View는 PurchasedMoneyView, PurchasedLottoView, WinningNumberView, View로 구성되어 있다.

  - View(부모 클래스)는 View에서 실행해야하는 Controller의 handler 함수들을 관리하고 있다.

    ```javascript
    export default class View {
      handlers = new Map();

      addHandler({ name, handler }) {
        const data = this.handlers.get(name) || [];
        data.push(handler);
        this.handlers.set(name, data);
      }
    }
    ```

  - View(부모 클래스)를 상속하고 있는 PurchasedMoneyView 클래스는 View(부모 클래스)에 handler 멤버 변수에 접근하여, Controller에 있는 함수를 호출한다.

    ```javascript
    //PurchasedMoneyView
    submitHandler(e) {
      e.preventDefault();

      const purchaseMoney = convertToNumber(this.input.value);

      try {
        validatePurchaseMoney(purchaseMoney);
        this.handlers
          .get('purchasedMoneySubmit')
          .forEach(func => func(purchaseMoney));
      } catch (error) {
        this.resetInputValue();
        alert(error);
      }
    }
    ```

  - View는 Model, Controller에 직접 접근하는 경우가 없다.

<br>

#### Model

- Model은 LottoNumbers, PurchasedLottos로 구성되어 있다.
  - 각 Model의 상태 데이터는 #을 이용해 private field에 선언하였다.
  - 외부에서 Model 상태 데이터를 접근하는 방식을 getter/setter 방식을 사용하였다.

<br>

#### 인상 깊은 점

- 로또 번호를 6개를 생성하는 방식

  - 1 ~ 45 배열을 생성한다.
  - 해당 배열을 shuffle하여 뒤섞는다.
  - 배열에서 6개의 번호를 추첨한다.

  ```javascript
  export const pickLottoNumber = (count) => {
    //로또번호 1 ~ 45를 소유하고 있는 배열
    const lottoNumbers = [...Array(RULES.MAX_LOTTO_NUMBER)].map(
      (_, index) => index + 1
    );
    shuffleArray(lottoNumbers);

    const numbers = [];

    for (let i = 0; i < count; i++) {
      numbers.push(lottoNumbers.pop());
    }

    return numbers;
  };

  //immutable 적용하기ㅠㅠ
  function shuffleArray(inputArray) {
    inputArray.sort(() => Math.random() - 0.5);
  }
  ```

  - 효율/성능을 따져야할 만큼 심각한 성능저하가 따르는 경우가 아닌 한은 읽기좋은 코드 / 간단한 코드를 작성하는 다양한 방법을 생각해 보시면 좋겠다

    <br>

### [#104, #108] 민초, 돔하디

파일 구조도는 MVC 패턴을 따르고 있다.

Model - Lotto
View - lottoView
Controller - LottoController

#### Controller

- Controller는 LottoController로 구성되어 있다.
  - constructor에서 lottos(로또번호를 포함하고 있는 멤버들로 구성된)멤버변수를 가지며, 이벤트리스너를 등록하고 있다.

<br>

#### View

- View는 lottoView로 구성되어 있다.
  - 클래스가 아닌, 함수표현식으로 구성되어 있다.
    ```javascript
    export const showResult = (lottos) => {
      deactivateForm();
      showResultElements();
      showNumberOfLottos(lottos.length);
      showLottoImage(lottos);
    };
    ```
    - showResult함수에서, view에 관련된 함수를 호출하는 형태로 구성되어있다.

#### Model

- Model은 Lotto로 구성되어 있다.
  - constructor에는 lottoNumbers(로또 번호들) 멤버변수와 로또 번호를 생성하는 함수를 호출하고 있다.

<br>

### [#114] 안

파일 구조도는 MVC 패턴을 따르고 있다.

Model - Lotto
View - LottoGameView
Controller - LottoGame

#### Controller

- Controller는 LottoGame로 구성되어 있다.
  - constructor에는 Model과 View 인스턴스를 생성한 후, 멤버변수로 할당하고 있다.
  - constructor에서 이벤트리스너를 등록하고 있다.

<br>

#### View

- View는 LottoGameView로 구성되어 있다.
  - View에서 토글 이벤트리스너를 등록하고 있다.

<br>

#### Model

- Model은 Lotto로 구성되어 있다.
  - constructor에는 lottos(로또번호를 포함하고 있는 멤버들로 구성된)멤버변수를 가지고 있다.

<br>

#### 인상깊은 부분

```javascript
resetLottoList() {
  this.lottoNumberList.replaceChildren("");
}
```

- MDN에 따르면, Element.replaceChildren()메서드는 기존 자식을 지정된 새 자식 집합으로 바꿉니다. 이것들은 DOMString 또는 Node객체가 될 수 있다.

<br>

### [#88, #91] 블링, 태태

파일 구조도는 MVC 패턴을 따르고 있다.

Model - lotto, lottoManager
View - lottoView
Controller - lottoController

#### Controller

- Controller는 lottoController로 구성되어 있다.
  - constructor에는 Model과 View 인스턴스를 생성한 후, 멤버변수로 할당하고 있다.
  - View에서 dom을 관리하고 있기 때문에, 이벤트 동작과정에서 Controller에 필요한 부분을 callback으로 View에게 전달하고 있다.
  - 외부에 드러낼 필요없는 부분은 #을 이용해 은닉화를 진행하였다.

<br>

#### View

- View는 lottoView로 구성되어 있다.
  - constructor에는 화면에 연관되어 있는 element를 멤버변수로 할당하고 있다.
  - 로또 element는 템플릿을 사용하지 않고, tag와 className을 이용해 element를 생성하고 text를 넣는 식으로 구성하였다.
  - 외부로 드러낼 필요없는 부분은 #을 이용해 은닉화를 진행하였다.

<br>

#### Model

- Model은 lotto, lottoManager로 구성되어 있다.
  - Lotto클래스는 lottoNumberSet(로또 번호들)을 멤버변수로 가지고 있으며, Set 자료형을 사용하고 있다. Set 객체를 생성할 때, 로또 번호를 랜덤하게 가져오는 함수를 호출하여 리턴 값을 매개변수로 전달하고 있다.
    ```javascript
    class Lotto {
      constructor() {
        this.lottoNumberSet = new Set(
          generateRandomNumberInRange({
            min: LOTTO_NUMBER_RANGE.MIN,
            max: LOTTO_NUMBER_RANGE.MAX,
            count: LOTTO_NUMBER_COUNT,
          })
        );
      }
    }
    ```
  - LottoManager클래스는 lottoPrice(로또 가격), purchaseAmount(구매한 로또 갯수), lottos(로또 번호들로 이루어진 배열)을 멤버변수로 초기화하고 있다.

<br>

#### 인상깊은 부분

```javascript
#toggleLottoNumbersShow = ({ target: { checked: isVisible } }) => {
  const { classList: lottoGridClassList } = this.lottoGrid;
  if (isVisible) {
    lottoGridClassList.add(CLASSNAMES.ONE_COLUMN_GRID_CLASSNAME);
    lottoGridClassList.remove(CLASSNAMES.HIDE_NUMBERS_CLASSNAME);
    return;
  }
  lottoGridClassList.remove(CLASSNAMES.ONE_COLUMN_GRID_CLASSNAME);
  lottoGridClassList.add(CLASSNAMES.HIDE_NUMBERS_CLASSNAME);
};
```

- 구조 분해(destructuring)이 인상깊다.

<br>

---

## 피드백 정리

### 대분류(ex: 아키텍처, 함수/클래스, 컨벤션, DOM, 테스트 등)

<br>

#### 아키텍처

- [#103] Controller와 View의 의존성을 끊는 방법
  - Controller와 View의 의존성을 끊기 위해서는 다양한 방법이 있는데, 웹에서는 흔히 customEvent를 활용합니다. View에서 이벤트핸들러를 등록하고, 그 핸들러 내부에서 위의 customEvent를 dispatch합니다. Controller에서는 여러가지 customEvent를 수신해서 동작을 수행합니다.
  - window객체에 메서드(dispatch)를 만들어두는 방법으로 우회하는 방법도 있습니다.
    View에서 등록한 핸들러 내부에서 적정한 타입/값을 담은 dispatch를 호출합니다.
    dispatch함수는 인자를 받아 controller들에게 알립니다(publish).
    해당 타입을 subscribe하고 있는 controller가 이를 처리합니다.

<br>

- [#114] 이벤트등록은 View에서 해야할까? Controller에서 해야할까?
  - 셀렉터라는게 view에서 element를 선택하자는 거니까 view에 의존적인게 당연하죠. 당연히 view에 의존적일 수밖에 없는 내용이라면 view에 몰아넣는게 합리적이지 않나요? 모든 크루들이 view와 controller의 의존관계를 끊어내려 하기보다 오히려 controller가 적극적으로 view에 개입하고 있는데, 그 이유는 제가 추측하기로는 크루분들 사이에 eventListener를 controller에서 등록해야 한다는 강박 같은게 자리잡은게 아닌가 싶거든요.. 왜 그런 컨센서스가 생긴건지 모르겠는데, **이벤트등록은 뷰에서 하는게 맞습니다.** 이벤트 발생시 컨트롤러에서의 동작은 별도의 함수를 마련, 호출함으로써 해결하면 되구요.

<br>

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

- [#103] 불필요한 초기화 메서드를 만들지 마라.
  - initDom(), initEventListener() 등 불필요한 메서드를 만들지 말고, constructor에서 초기화를 진행해라.

<br>

- [#103] getter/setter의 장점
  - IDE 환경에 따라 다르겠지만, `.`만 찍어도 접근자들을 확인할 수 있다.
  - 내부 로직을 은닉화하고, 외부에는 필요한 접근자들만 제공한다.
  - Object.assign, destructuring assignment등에 의해서도 노출되지 않는다.

<br>

- [#103] 이벤트 리스너 내부 익명함수 -> 핸들러 분리
  - 이벤트 리스너는 원래 `등록`과 `해제`를 함께 고려해야 한다.
    - 해제해주지 않으면, 메모리가 낭비될 뿐만 아니라 경우에 따라서 중복 등록되어 이벤트에 대해 함수가 여러번 호출될 수 있다.
  - 익명함수를 사용할 경우 해제를 할 수 없기때문에 핸들러로 분리해야 한다.

<br>

- [#115] #을 이용한 은닉화의 범위
  - 객체지향 관점에서 외부로 노출될 메소드들만 public으로 지정해놓는게 좋고,
    노출되지 않을 메소드는 private으로 만드는게 좋습니다!

<br>

- [#91] static 메서드의 기준은 무엇일까?
  - class는 기본적으로 인스턴스를 생성하는 틀입니다. 인스턴스 내부 '메소드'의 정의는 인스턴스 '자신'의 내부 동작을 수행하는 함수입니다. '자신'에 관여하는 내용이 없다면 이는 '메서드'가 아닌 '함수'로 불러야 하겠죠. 그런 맥락에서 보면, **this를 사용하지 않는 메서드는 인스턴스에서 접근해야할 필요가 없습니다. 인스턴스에 제공될 필요가 없으니 굳이 상속시키지 않게끔 static으로 분리하라는 것이죠.** 그런데 전부 static 메서드만 있는 클래스의 경우, 이를 바탕으로 인스턴스로 만들어봐야 그 인스턴스는 빈껍데기만 있는 객체에 불과합니다. 실제로 인스턴스로 만드는 시도조차 안하실거구요. 그렇다면 해당 클래스는 클래스로서가 아니라 일반 객체로서의 의미밖에 없는 것이니, static으로만 구성된 클래스는 차라리 처음부터 객체로 만드는 편이 좋다고 생각합니다.

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

- [#103] Object.freeze가 인상적인데, "굳이"라는 생각이 든다

  - 이미 '상수'임을 모두가 아는 상태에서, 객체에 접근하는 경우가 발생한다면 건든 사람이 책임을 져야하는 일이다. 오버스펙이라 생각한다.

<br>

- [#111] css 파일명 원칙
  - 보통 css 파일명으로 카멜케이스를 사용하지 않는다.

<br>

- [#104] && 연산자 관련 코멘트
  ```javascript
  const isValidMoneyInput = (money) =>
    isThousandMultiple(money) &&
    isOverThouand(money) &&
    isUnderMillion(money) &&
    isValidMoneyRange(money);
  ```
  - && 연산자를 사용할 때, 값에 대한 평가로만 사용하고 함수 호출을 지양하는 편이다.

<br>

- [#104] 네이밍에 전치사?
  - 클린코드 원칙에는 전치사 사용을 자제하는것을 추천한다.

<br>

- [#91] 상수는 굳이 멤버로 할당할 필요가 없다.
  ```javascript
  this.lottoPrice = LOTTO_PRICE;
  ```

<br>

-[#88] model에 직접 접근은 지양해라.

```javascript
//직접 접근
this.view.renderLottos(this.lottoManager.lottos);

//간접 접근
const lottos = this.lottoManager.getLottos;
this.view.renderLottos(lottos);
```

<br>

### 테스트

- [#number]

<br>

### 패키지

- [#115] ^(캐럿)을 붙인 이유는 무엇인가요?
  ```javascript
  "devDependencies": {
    "@babel/core": "^7.16.12",
    "@babel/preset-env": "^7.16.11",
    "babel-jest": "^27.4.6",
    "babel-loader": "^8.2.3",
    "clean-webpack-plugin": "^4.0.0",
    "html-webpack-plugin": "^5.5.0",
    "css-loader": "^6.6.0",
    "eslint": "^8.9.0",
  ```
  - npm 측에서의 버전 간의 호환성을 위하여 도입.

<br>

### CSS

- [#115] 공통적으로 쓰이는 색상은 global css variable로 만들자.
  - 추후 개발을 진행하거나 했을때 일관적으로 색상 팔레트가 관리되고 있지 않다면, 찾아다니는게 대규모 프로젝트에선 생각외로 힘들다.

<br>

### 새로운 접근

- [#114] 꼭, constructor에서 element에 접근해서 멤버변수로 해야할까?
  - HTML상에 위 element의 생명주기가 JS의 생명주기보다 먼저 시작해서 끝까지 살아있다는 전제하에서는, 위 값을 상수 또는 변수로 만들어두면 안될까요?
    ```javascript
    const ELEMENTS = {
      ...
      SWITCH_INPUT: $(".switch-input")
    }
    ```
