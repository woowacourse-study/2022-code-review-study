# Level1 Lotto Step1(담당 PR 번호들) - 마르코

- 분석 담당 코드
  - 준찌 [#81](https://github.com/woowacourse/javascript-lotto/pull/81)
  - 유세지 [#82](https://github.com/woowacourse/javascript-lotto/pull/82)
  - 동키콩
  - 온스타
  - 위니
  - 자스민
  - 아놀드
  - 빅터
  - 코이

## 코드 분석

### [#81] 준찌, [#82] 유세지

![준찌유세지](img/schema82.png)

#### Controller

- 진입점인 index.js가 바로 부르는 RacingGameManager() 클래스에서 .start() 메서드로 시작한다. 해당 클래스는 컨트롤러 역할인 듯하다.

  - start 메서드는 `다른 클래스 인스턴스 생성`, `dom요소 가져오기`, `event 바인딩`을 init한다.

    - (1) model과 view 클래스 인스턴스 생성하여 멤버변수로 할당
    - (2) DOM 요소 가져와서 멤버변수 할당

      - constructor가 없다. 메서드 내부에서 멤버 변수를 만들고 있다.
        - "많이들 이렇게 작성하시던데, 이건 그냥 constructor에 넣어두어도 되지 않나요?"

    - (3) 선택된 DOM 요소에 이벤트 바인딩
      - [네이밍] 핸들러 네이밍을 할 때, 접두사로 on을 붙인다. `onSubmitChargeInputForm`

- controller는 이벤트를 통해 발생한 데이터를 가공하고, view의 렌더링 메서드를 호출하며 그 데이터를 매개변수로 전달한다.

#### View

- constructor 내부에 dom 요소를 멤버변수 할당하지 않고, 프라이빗 메서드 내부에서 dom 요소를 멤버변수로 할당하여 사용하고 있다.
- render하는 메서드들은 매개변수로 렌더링할 데이터를 (컨트롤러로부터) 받고 있다.
- view가 model에 의존하고 있지 않다.

#### model

- model에는 `LottoGameModel`과 `LottoModel` 2개가 있다.
  - 그런데 두 모델의 네이밍이 와닿지 않는다는 리뷰어의 의견이 있었다. ["Lotto 클래스는 어떤 정보를 담는 녀석인지, LottoGame은 또 어떤 녀석인지, 이름만 봤을 때 대략적으로라도 유추할 수 있는 네이밍을 고민해보시면 좋겠어요."]
- controller에서 의존하고 있는 model인 LottoGameModel은 lottoList(2차원 배열)만 멤버변수로 관리하고 있다.
- LottoGameModel이 의존하고 있는 LottoModel은 lottoNumbers(1차원 배열, 길이 6)만 멤버변수로 관리하고 있다.

#### 클로저 활용

- 6개 랜덤 숫자가 담긴 lotto 배열 만들 때, closure 사용

```jsx
function shuffle(array) {
  const copy = [...array]; // 값을 받을 때 깊은복사

  copy.sort(() => Math.random() - 0.5); // random은 0과 1사이 랜덤값이고 sort메서드의 compareFunction은 음수냐 양수냐를 기준으로 두 값씩 비교하며 정렬하므로, 값들이 한 쪽으로 편중되지 않도록 random 값에 0.5를 빼서 음수 또는 양수가 랜덤하게 나오도록 compareFunction을 만들어서 사용한다.

  return [...copy]; // 값을 반환할 때도 깊은복사하여 반환
}

function lottoNumberClosure() {
  const pickNumbers = shuffle([...new Array(45)].map((_, idx) => idx + 1)); // [1,2,3,4,...,45] -> 섞어서

  return () => pickNumbers.pop(); // 배열에서 마지막 값을 하나씩 반환하고 배열에서 제거
}

class Lotto {
  constructor() {
    this.lottoNumbers = this.createLottoNumbers();
  }
  createLottoNumbers() {
    const getLottoNumber = lottoNumberClosure();
    // getLottoNumber()는 1부터 45 사이의 값 중 하나를 반환하고, 한 번 반환된 값은 다시 반환되지 않는다.
    return [...new Array(6)].map(() => getLottoNumber());
  }
}
const lotto = new Lotto();
console.log(lotto); // Lotto { lottoNumbers: [ 7, 14, 45, 33, 18, 36 ] }
```

## 피드백 정리

### 아키텍처

- [#81] Model의 getter 함수에서 DeepCopy를 수행하는 것에 대해 어떻게 생각하시나요 ?
  - 질문설명: get으로 가져오는 멤버 변수는 불러온 사용처에서 변경을 가하더라도 영향이 가지 않았으면 해요. 사용된 곳에서 가한 변경이 객체의 멤버에 직접 전달되어 버린다면, 예상치 못한 부수효과들이 발생할 것 같거든요 하지만 DeepCopy 하는 로직의 경우 객체의 레이어가 깊을수록, 데이터가 많을수록 비용이 너무 커질 것 같아 우려되기도 합니다.
  - 답변: 현재 코드에서 얼마나 DeepCopy된 모델의 데이터가 필요한가? 가 기준이 될 것 같은데요. 그만큼 모델 멤버 객체의 뎁스가 깊지 않다면 굳이 모델의 getter함수에서 DeepCopy를 수행하는 것이 유의미한지 잘 모르겠습니다.
  - 답변2: 깊은복사는 불변성을 통해 얻는 이점이 이를 위한 퍼포먼스 손실에 비해 큰 경우에 취하는 전략입니다. 특히 레퍼런스에 대한 성능최적화가 고도화된 V8엔진 하에서는 이로부터 얻는 퍼포먼스 손실은 생각보다 미미합니다.
    그렇다면 무조건 불변객체를 사용하는게 좋느냐 하면, 반드시 그렇지는 않다고 생각합니다. nested한 객체의 경우 전체 뎁스를 모두 깊은복사해야만 하는 경우도 물론 있지만, 1뎁스까지만 깊은복사를 해도 충분한 경우도 있고, 얕은복사로도 아무 문제 없는 경우도 많습니다. 성능이 고민되신다면 각각의 상황에 따라 전략을 취하시면 되겠습니다.

```jsx
// 예시
// 유틸
export const deepCopy = object => JSON.parse(JSON.stringify(object));

// 모델
class LottoGameModel {
  constructor() {
    this.lottoList = null;
  }

  getLottoList() {
    /** getter로 가져간 lottoList를 변경하여도 lottoList의 멤버에겐 영향이 없다. */
    return deepCopy(this.lottoList);
  }
}
```

현재 코드에서 얼마나 DeepCopy된 모델의 데이터가 필요한가? 가 기준이 될 것 같은데요. 그만큼 모델 멤버 객체의 뎁스가 깊지 않다면 굳이 모델의 getter함수에서 DeepCopy를 수행하는 것이 유의미한지 잘 모르겠습니다.

### 함수/클래스

- [#81] 고차함수를 추천하는 이유 중 하나는 가독성이 좋아지는 점이 가장 크다.
  - map 메서드로 리팩토링 사례

```jsx
// before
const lottoList = [];
for (
  let lottoCount = 0;
  lottoCount < availableLottoAmount;
  lottoCount = lottoCount + 1
) {
  const lottoNumbers = this.createLottoNumbers();
  const lotto = Lotto.create(lottoNumbers);
  lottoList.push(lotto);
}
```

```jsx
// after
const lottoList = [...new Array(availableLottoAmount)].map(() => new Lotto());
```

- [#81] Prototype에 커스텀 메소드를 추가해서 사용하는 것 주의
  - Prototype에 커스텀 메소드를 추가해서 사용하는 것은, 내장 객체가 가지는 전역 범위때문에 생길 수 있는 변경 가능성 때문에 별로 추천하는 방법이 아니다. 또한, 유틸성 함수의 역할로 쓰려는 목적이라면 명시적으로 유틸 함수를 정의하고 모듈화하는 것이 더 좋다.
  - built-in 객체의 prototype에 이미 있던 함수를 새로운 내용으로 변경하는 것은 당연히 위험한 일이니 지양하는게 맞는데,
    개인적으로는 built-in 객체의 prototype에 새로운 내용을 추가하는 것 역시 권장하고 싶지 않습니다. 다른 모듈(파일)들을 작업하는 협업자 입장에서는 deepCopy라는 메서드가 있는지 여부를 알기 어려워서 안쓰게 될 확률이 높아요.
    명시적으로 import해서 쓸 수 있도록 static한 함수로 구현하는 것이 더 좋다고 생각해요. 그렇게 하면 prototype 체이닝상의 존재여부와 무관하게 다른 객체들도 쓸 수 있을테니까요. (deepCopy는 배열 뿐 아니라 객체에서도 쓰기 좋은 함수입니다).
    굳이 this 및 prototype 체이닝을 이용한 '메서드'로 써보고 싶다! 라고 하면, Array를 extends하는 새로운 subClass를 만들어서 그 안에 method를 정의하는 편이 좋습니다. 이러면 앞서 설명드린 문제점들은 모두 해소됩니다. 다만 이 경우엔 리터럴 선언 방식은 유효하지 않다는 점이 아쉽긴 한데, prototype 메서드를 활용하고자 하는 니즈와 이 아쉬움 사이에서 잘 저울질해서 선택하면 되겠죠.

```jsx
Array.prototype.deepCopy = function () {
  return JSON.parse(JSON.stringify(this));
};
```

- [#82] `new.target` 사용

  - [new.target](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/new.target)
    - new.target 속성(property)은 함수 또는 생성자가 new 연산자를 사용하여 호출됐는지를 감지할 수 있습니다. new 연산자로 인스턴스화된 생성자 및 함수에서, new.target은 생성자 또는 함수 참조를 반환합니다. 일반 함수 호출에서는, new.target은 undefined입니다.

- [#82] 드모르간 법칙을 활용하여 every 메서드에서 some 메서드로 변경하면 더 효율적인 로직일 수 있다.
  - 조건을 '모두 충족시켜야만' true가 반환되는 every보다는, 이를 뒤집어서 '일부 조건이라도 충족시' true가 반환되는 some을 쓰면 좀더 효율적인 검색이 이뤄질 수 있다. (드모르간 법칙 이용)

```jsx
function isValidNumber(lottoNumbers) {
  return lottoNumbers.every(
    number =>
      Number.isInteger(number) &&
      number >= NUMBER.LOTTO_MIN_NUMBER &&
      number <= NUMBER.LOTTO_MAX_NUMBER
  );
}
```

### 컨벤션/네이밍

- [#81] utils, constants에서 파일을 많이 생성하여, 코드들을 분리하는 것도 명확하다는 장점이 있다.
- [#82] 하나의 로또 티켓에 들어가는 번호가 6개라는 의미로, 6을 값으로 갖는 `LOTTO_NUMBER_LENGTH`라는 변수명에서 `length`는 '자릿수'를 의미하는 것으로 오해할 여지가 있으므로, `length`를 'amount'로 변경해보는 것도 고민해보자.

### 테스트

- [#81] 유틸 함수에 대한 테스트에 가까울 경우, 서비스 로직 테스트와 분리하는 것이 더 분명한 의미를 전달할 수 있다.

### 함수/메서드 분리

- [#82] 함수/메서드를 분리해야 하는 목적은 `외부에서의 접근수단 제공` 및 `여러 곳에서 호출`을 기준으로 판단해야 한다. 단순히 기능이 다르다는 이유만으로 잘게 분리하면 오히려 혼란을 줄 수도 있다.
  - 아래 코드의 경우 지나치게 잘게 분리되었다고 볼 수 있다. renderLottoList, renderPurchasedMessage는 모두 renderLottoSection 한군데에서만 호출하고 있는, 분리의 목적이 불분명한 메서드들이다. 이런 메서드들은 협업시 다른 사람들로 하여금 혹시 다른데서도 호출하는 곳이 있나? 하는 혼란만 주게 될 수 있지 않는지 고민할 필요가 있다.

```jsx
renderLottoSection(lottoList) {
  this.renderPurchasedMessage(lottoList.length);
  this.renderLottoList(lottoList);
}

renderLottoList(lottoList) {
  this.$lottoContainer.innerHTML = lottoList
    .map((lotto) => this.generateLottoTemplate(lotto))
    .join('');
}

renderPurchasedMessage(lottoAmount) {
  this.$purchasedMessage.innerText = `총 ${lottoAmount}개를 구매하였습니다.`;
}
```

```jsx
// 이 또한 과도한 메서드 분리라고 볼 수 있다. 기능별로 분리를 해두었지만, 실제 호출은 start메서드 한군데에서만 이뤄지고 있기 때문이다.
class LottoGameManager {
  #initializeGame() {
    this.lottoGameModel = new LottoGameModel();
    this.lottoGameView = new LottoGameView();
  }

  #initializeDOM() {
    this.$chargeForm = findElement(SELECTOR.CHARGE_INPUT_FORM);
    this.$chargeInput = findElement(SELECTOR.CHARGE_INPUT);
    this.$alignConverter = findElement(SELECTOR.ALIGN_CONVERTER);
  }

  #initializeHandler() {
    this.$chargeForm.addEventListener("submit", this.onSubmitChargeInputForm);
    this.$alignConverter.addEventListener("change", this.onChangeAlignState);
  }
  start() {
    this.#initializeGame();
    this.#initializeDOM();
    this.#initializeHandler();
  }
}
```
