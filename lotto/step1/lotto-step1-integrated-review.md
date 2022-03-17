# 주간 코리스 [🎱행운의 로또 1호]

## 0. 코리스 설명

- [코드리뷰스터디(코리스)](https://github.com/woowacourse-study/2022-code-review-study)는 `함께 자라기 🌱`를 지향해요.

- 이번 호에 정리한 코드리뷰 피드백 주제는 `Level1 LottoMission Step1`입니다.
- 스터디원이 아니시더라도 본 자료에 대한 피드백 및 [디스커션](https://github.com/woowacourse-study/2022-code-review-study/discussions) 참여 등을 모두 환영해요.💚

- 자료 : 우아한테크코스 프론트엔드 4기 코드리뷰
  - 각 목록의 앞에 해당 PR번호 기재
- 편집 : 코드리뷰스터디원
  - 마르코(@wonsss)
  - 호프(@moonheekim0118)
  - 무비(@byhhh2)
  - 록바(@lokba)

<br>

## 1. 아키텍처

### 1-1 MVC 패턴

- [[#90](https://github.com/woowacourse/javascript-lotto/pull/90#issuecomment-1049971008), [#90 - 추가 코멘트](https://github.com/woowacourse/javascript-lotto/pull/90#issuecomment-1050580916)] MVC 패턴 관련 공유 내용

  ```
  Model은 Controller와 View에 의존하지 않아야 한다.
  → Model 내부에 Controller와 View에 관련된 코드가 있으면 안 된다.
  → 즉 모델 내부에서 컨트롤러와 뷰에 있는 것을 임포트하면 안 됨

  View는 Model에만 의존해야하고, Controller에 의존하면 안 됨.
  → View에는 Model의 코드만 있을 수 있고, Controller의 코드가 있으면 안 됨. *
  View가 Model로부터 데이터를 받을 때,
  사용자마다 다르게 보여주어야 하는 데이터에 대해서만 Model에서 받야아 한다.
  → 공통으로 보여지는 부분은 View가 자체적으로 가지고 있어야하는 것.

  Controller는 Model과 View에 의존해도 된다.
  View가 Model로부터 데이터를 받을 때, 반드시 Controller에서 받아야 한다. **
  → 데이터를 받을 때 Controller 코드 안에서만 받야 한다.

  + 리뷰어님의 추가 코멘트
  View에는 Model의 코드만 있을 수 있고, Controller의 코드가 있으면 안 됨. *
  -> Controller를 통해 Model의 데이터가 업데이트되기때문에 Controller의 코드가 있을 필요가 없다.
  View가 Model로부터 데이터를 받을 때, 반드시 Controller에서 받아야 한다. **
  -> Controller가 Model을 업데이트하고 그 업데이트된 Model의 데이터를 받아야한다. 위 글에서 "반드시 Controller에서 받아야 한다."는 Controller의 액션의 통해서 받아야한다 라고 말씀드리는게 명확했던 것 같네요.
  ```

  <br>

- [[#102](https://github.com/woowacourse/javascript-lotto/pull/102#discussion_r815267289)] 컨트롤러에서 모델에게 전달받은 상태를 직접 뷰에게 전달하는 것이 적절하다.
  <br>
- [[#109](https://github.com/woowacourse/javascript-lotto/pull/109#discussion_r813851182)] 컨트롤러가 여러개의 view를 가질 수 있으니 확장성을 고려하며 설계할 것
  <br>
- [[#90](https://github.com/woowacourse/javascript-lotto/pull/90#issuecomment-1049971008)] 리뷰어님의 개인적인 취향은 controller에서 model과 view 인스턴스를 생성하고, 생성하고 `index.js`에서 controller를 생성하는 편.
  <br>

- [#103] Controller와 View의 의존성을 끊는 방법
  - Controller와 View의 의존성을 끊기 위해서는 다양한 방법이 있는데, 웹에서는 흔히 customEvent를 활용합니다. View에서 이벤트핸들러를 등록하고, 그 핸들러 내부에서 위의 customEvent를 dispatch합니다. Controller에서는 여러가지 customEvent를 수신해서 동작을 수행합니다.
  - window객체에 메서드(dispatch)를 만들어두는 방법으로 우회하는 방법도 있습니다. View에서 등록한 핸들러 내부에서 적정한 타입/값을 담은 dispatch를 호출합니다. dispatch함수는 인자를 받아 controller들에게 알립니다(publish). 해당 타입을 subscribe하고 있는 controller가 이를 처리합니다.

<br>

- [#86] MVC에서 event 바인딩하는 함수를 controller보다 view에 두는 것이 일반적이긴 하나, 상황에 따라 자신이 좋다고 판단하고 작성한 코드가 오히려 정답일 수 있다.

  - 일단, 위임을 할지 말지에 따라서도 다르게 가져갈 것 같고 앱의 규모에 따라 다르게 가져갈 여지도 있다. 참고로 view에서 event 바인딩하는 함수를 구현한다면 유지보수 측면에서 원하는 요소를 찾기 쉬울 것 같다는 장점이 있다.
    <br>

- [#114] 이벤트 등록은 View에서 해야할까? Controller에서 해야할까?
  - 셀렉터라는게 view에서 element를 선택하자는 거니까 view에 의존적인게 당연하죠. 당연히 view에 의존적일 수밖에 없는 내용이라면 view에 몰아넣는게 합리적이지 않나요? 모든 크루들이 view와 controller의 의존관계를 끊어내려 하기보다 오히려 controller가 적극적으로 view에 개입하고 있는데, 그 이유는 제가 추측하기로는 크루분들 사이에 eventListener를 controller에서 등록해야 한다는 강박 같은게 자리잡은게 아닌가 싶거든요.. 왜 그런 컨센서스가 생긴건지 모르겠는데, **이벤트등록은 뷰에서 하는게 맞습니다.** 이벤트 발생시 컨트롤러에서의 동작은 별도의 함수를 마련, 호출함으로써 해결하면 되구요.

<br>

- [[#90](https://github.com/woowacourse/javascript-lotto/pull/90#issuecomment-1049971008)] 질문: view와 controller중 어디에서 `addEventListener`를 해주는 게 맞을까요?

  - 리뷰어님의 개인적인 취향은 DOM에 대한 이벤트 바인딩은 view에서 담당하고 그 이벤트에 대한 액션은 controller에서 하는게 좀 더 역할 분담이 명확해 보임

<br>

- [#88] model에 직접 접근은 지양해라.

  ```javascript
  //직접 접근
  this.view.renderLottos(this.lottoManager.lottos);

  //간접 접근
  const lottos = this.lottoManager.getLottos();
  this.view.renderLottos(lottos);
  ```

<br>

### 1-2. UI와 비즈니스 로직

- [#92] 뷰로직과 비즈니스 로직을 분리한다. 비즈니스 로직(=도메인 로직)이란, 실제 서비스의 핵심 기능을 구현하기 위해 `데이터를 어떻게 가공하고 관리할 것인가` 전반에 걸친 로직을 포함한다. https://en.wikipedia.org/wiki/Business_logic

  - 비즈니스 로직은 데이터가 생성, 저장 및 변경될 수 있는 방법을 결정하는 것이다. 반면, 그 외의 로직은 데이터베이스를 관리하거나 사용자 인터페이스를 표시하거나 일반적으로 프로그램의 다양한 부분을 연결하는 하위 수준 세부 정보와 관련되어 있다.
    <br>

- [#84] 누가 어떤 역할을 하고 있는지는 명확히 보이도록 설계하는 것이 좋다.

---

## 2. 함수

### 2-1. 함수분리

- [#83] 함수분리 기준
  - 함수를 분리하지 않더라도 충분히 이해할 수 있는 범주의 역할인데다가 과도한 분리는 오히려 유지보수를 어렵게 만든다는 생각을 하고있기에 코멘트를 드렸습니다. 혹 재사용을 하더라도 분리할 필요성이 떨어지는 함수라고 생각이 들어요. 우선은 제가 함수를 분리하는 관점은 기능 확장 될 여지가 있거나 내부의 구현이 복잡하다면 추상화를 통해 미리 해서 분리한다. 기능이 확장 될 여지가 없거나 코드가 짧거나(코드를 처음 봤을때 맥락이 애해되는 범주) 재사용성이 떨어진다면 분리하지 않는 방향성들을 고려해보고 판단하는게 맞는 방향이라고 생각됩니다.
    <br>
- [[#101](https://github.com/woowacourse/javascript-lotto/pull/101#discussion_r815254485)] 특정 값에 종속적인 함수는 분리할 필요가 없다.
  <br>
- [[#95](https://github.com/woowacourse/javascript-lotto/pull/95#discussion_r814765497)] 역할을 분리하는 것은 좋지만 역할을 분리함으로써 불필요한 코드가 더 생기진 않는지 생각해보자.
  <br>

- [#92] 여러 곳에서 범용적으로 쓰일 법한 함수는 밖으로 옮기는 것이 낫다.
  <br>

- [#86] 과도한 분리는 오히려 복잡성을 증가시킬 수 있다.
  <br>

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
            this.$chargeForm.addEventListener(
              "submit",
              this.onSubmitChargeInputForm
            );
            this.$alignConverter.addEventListener(
              "change",
              this.onChangeAlignState
            );
          }
          start() {
            this.#initializeGame();
            this.#initializeDOM();
            this.#initializeHandler();
          }
        }
        ```

    <br>

### 2-2. 고차함수

- [[#99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814069099)] 반복이면 바로 `forEach`를 쓸 수 있지만, 만약 반환을 해줘야 하는 경우라면 `map`을 고려해볼 것
  <br>
- [[#99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814030886)] 리뷰어님이 괜찮다고 해주신 커스텀 반복함수

  ```js
  const repeatCallback = (count, callback) => {
    for (let i = 0; i < count; i += 1) {
      callback();
    }
  };
  ```

  <br>

- [[#109](https://github.com/woowacourse/javascript-lotto/pull/109#discussion_r813861348)] 템플릿을 만들 때 중복이 된다면 `map`을 고려해보자.
  <br>
- [[#109](https://github.com/woowacourse/javascript-lotto/pull/109#discussion_r816423887)] 순차적인 데이터로 배열을 채울 때 활용할 수 있는 내장함수

  ```js
  //before
  const array = [];

  for (let i = start; i <= end; i += 1) {
    array.push(i);
  }

  return array;
  };

  //after : for문을 사용하지 않는 방식
  Array.from({ length: end - start + 1 }).map((_, index) => start + index);
  ```

  <br>

- [[#95](https://github.com/woowacourse/javascript-lotto/pull/95#discussion_r814757511)] 배열을 생성할 때 `map`을 생각해보자

<br />

- [#105] 특정 길이만큼 배열을 만들어서 값을 넣어주려면, `for문과 push 메서드` 대신에 `특정 길이의 빈 배열에 map 메서드`를 쓸 수도 있다.

  - 특정 length의 빈 배열을 만들고 싶을 때, `[...Array(6)]` 또는 `Array.from({ length: 6 })` 를 활용할 수 있다.

        ```jsx
        for (let i = 0; i < count; i += 1) {
          const lotto = new Lotto();
          lotto.generateRandomNumber();
          this.lottos.push(lotto);
        }
        ```

        ```jsx
        this.lottos = Array.from({ length: count }).map(() => {
          const lotto = new Lotto();
          lotto.generateRandomNumber();
          return lotto;
        });
        ```

    <br>

- [#86] 중복된 값을 저장하지 않는 Set 자료구조 및 해당 메서드(add)등도 활용할 수 있다.

  ```jsx
  const lottoNumberSet = new Set();
  while (lottoNumberSet.size < LOTTO_NUMBERS.LOTTO_LENGTH) {
    lottoNumberSet.add(
      getRandomNumber(
        LOTTO_NUMBERS.MIN_LOTTO_NUMBER,
        LOTTO_NUMBERS.MAX_LOTTO_NUMBER
      )
    );
  }
  return [...lottoNumberSet];
  ```

  <br>

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
        const lottoList = [...new Array(availableLottoAmount)].map(
          () => new Lotto()
        );
        ```

    <br>

- [#82] 드모르간 법칙을 활용하여 every 메서드에서 some 메서드로 변경하면 더 효율적인 로직일 수 있다.

  - 조건을 '모두 충족시켜야만' true가 반환되는 every보다는, 이를 뒤집어서 '일부 조건이라도 충족시' true가 반환되는 some을 쓰면 좀더 효율적인 검색이 이뤄질 수 있다. (드모르간 법칙 이용)

        ```jsx
        function isValidNumber(lottoNumbers) {
          return lottoNumbers.every(
            (number) =>
              Number.isInteger(number) &&
              number >= NUMBER.LOTTO_MIN_NUMBER &&
              number <= NUMBER.LOTTO_MAX_NUMBER
          );
        }
        ```

    <br>

### 2-3. 콜백함수

- [[#102](https://github.com/woowacourse/javascript-lotto/pull/102#discussion_r815267289)] 콜백함수를 넘긴다는건 그 함수에게 역할을 위임 하겠다는 것
  <br>

### 2-4. 깊은복사

- [#81] Model의 getter 함수에서 깊은 복사를 하여 반환해주는 것은 상황에 따라 전략을 취하면 된다.

  - 깊은 복사를 하여 불변성을 통해 얻는 이점이 해당 코드에서 있다면 할 수도 있다.
  - 그리고 중첩된 객체의 경우 전체 depth를 모두 깊은복사해야 하는 경우도 있겠지만, 1depth까지만 깊은 복사를 해도 충분한 경우, 또는 얕은 복사로도 아무 문제 없는 경우도 많다.
  - 그리고 성능최적화가 고도로된 V8 엔진에서는 깊은 복사를 해도 발생하는 성능 손실은 생각보다 미미하므로, 필요시 깊은복사를 해도 되겠다.

    ```jsx
    // 유틸
    export const deepCopy = (object) => JSON.parse(JSON.stringify(object));
    // 이것은 아마 1depth까지만 깊은 복사가 되는 코드?

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

    <br>

### 2-5. 예외처리

- [[#94](https://github.com/woowacourse/javascript-lotto/pull/94#discussion_r815283789)] `Throw`와 `try-catch`문을 함께 사용할 때 `alert`를 `catch`에 작성해주면 코드량도 줄고 유연한 구조가 된다.
  <br>
- [#85] 무한대의 값을 입력할 수 있도록 두면, 일정 금액 이상이 넘어가게 되면 앱이 정지하게 되므로, 예외처리를 해야 한다.
  <br>

### 2-6. Prototype

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

    <br>

### 2-7. 변수

- [[#90](https://github.com/woowacourse/javascript-lotto/pull/90#issuecomment-1049971008)] 메서드 안에 임시변수를 쓰는 것은 지양되어야 할까요?
  - 리뷰어님의 개인적인 의견은 남이 유추가 가능한지 아닌지로 임시변수를 선언할지 말지를 결정한다.
    <br>
- [[#90](https://github.com/woowacourse/javascript-lotto/pull/90#discussion_r813976935)] 예외의 값은 저장하지 않아도 된다.

<br />

### 2-8. 연산자

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

### 2-9. 중복 판단

- [[#99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814076018)] 중복을 판단하는 로직을 작성하기보다는 Set을 활용해보자

---

## 3. 클래스

### 3.1 내장 메소드

- [[#99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r815812894)] 가격에 단위표시를 하고 싶다면 `toLocaleString()`을 사용해보자.
  <br>
- [[#95](https://github.com/woowacourse/javascript-lotto/pull/95#discussion_r814791814)] 현재 문자열의 시작을 다른 문자열로 특정 길이만큼 채우고 싶다면 [`padStart()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/padStart)를 사용해보자.
  <br>

### 3.2 은닉화

- [#103] getter/setter의 장점
  - IDE 환경에 따라 다르겠지만, `.`만 찍어도 접근자들을 확인할 수 있다.
  - 내부 로직을 은닉화하고, 외부에는 필요한 접근자들만 제공한다.
  - Object.assign, destructuring assignment등에 의해서도 노출되지 않는다.
    <br>
- [#92] 캡슐화가 잘됐다면 객체가 외부에 반드시 제공해야 되는 기능만 드러내고 나머지는 은닉화해야 한다.

  - 어떤 객체의 getter, setter가 특별한 이유없이 public 이라면 캡슐화를 온전히 적용했다고 보기 어렵다. 캡슐화가 잘됐다면 객체가 외부에 반드시 제공해야 되는 기능만 드러내고 나머지는 은닉화 해야한다.
    <br>

- [#86] 외부에서 알 필요가 없는 메서드라면 private로 처리해주는게 맞다.

  - 예를들어 전기포트가 있다고 할 때 사용자는 전원을 키고 끄는법에 대해서만 알면 되고 내부에서 어떻게 전기포트에서 열이 올라서 물을 끓이는지 세부적인 내용에 대해 알 필요가 없는 부분과 같은 맥락이다. "중요한 데이터를 외부에서 건드릴 수 없도록 하고, 외부에서 사용하지 않는다"의 맥락을 모두 이 전기포트의 예시에 빗대어 생각해볼 수 있다.
    <br>

- [#83] 외부에서 사용하지 않는 변수나 메서드는 무조건 private 으로 처리해주면 좋은가?

  - 외부에서 사용하지 않는 변수나 메서드는 무조건 private 으로 처리해주면 좋은가?
  - 외부에서 알 필요가 없는 메서드라면 private로 처리해주는게 맞다고 생각합니다. 예를들어 전기포트가 있다고 할 때 저희는 전원을 키고 끄는법에 대해서만 알면되고 내부에서 어떻게 전기포트에서 열이 올라서 물을 끓이는지 세부적인 내용에 대해 사용자가 알 필요가 없는 부분과 같은 맥락이라 생각합니다.중요한 데이터를 외부에서 건드릴 수 없도록 + 외부에서 사용하지 않는다의 맥락이 모두 위의 예시에 빗대어 생각해보시면 어느정도 윤곽이 잡히시지 않을 까 생각해봅니다.

<br>

- [#115] #을 이용한 은닉화의 범위
  - 객체지향 관점에서 외부로 노출될 메소드들만 public으로 지정해놓는게 좋고,
    노출되지 않을 메소드는 private으로 만드는게 좋습니다!

<br>

- [#112] 생성자의 파라미터로 넘기기 vs setter 사용하기

  - 생성자의 파라미터로 넘기는 건, 인스턴스를 생성할 때부터 값을 셋팅해주는거고, 기본적으로 OOP 에서는 외부에서 직접적으로 객체의 데이터에 접근하는 것을 막기 위해 setter 를 사용합니다. (같이 봐야하는 개념으로 private 이 있습니다) 만약 외부에서 변경이 가능하다면 우리가 정의한 객체의 무결성이 깨질 수 있기 때문입니다. 예를 들어, lottoList 가 private 하지 않는다면 아래와 같이 객체를 깨뜨릴 수 있겠죠. 그렇기 때문에 setter 를 사용합니다..!
    <br>

### 3-3. Static

- [#91] static 메서드의 기준은 무엇일까?
  - class는 기본적으로 인스턴스를 생성하는 틀입니다. 인스턴스 내부 '메소드'의 정의는 인스턴스 '자신'의 내부 동작을 수행하는 함수입니다. '자신'에 관여하는 내용이 없다면 이는 '메서드'가 아닌 '함수'로 불러야 하겠죠. 그런 맥락에서 보면, **this를 사용하지 않는 메서드는 인스턴스에서 접근해야할 필요가 없습니다. 인스턴스에 제공될 필요가 없으니 굳이 상속시키지 않게끔 static으로 분리하라는 것이죠.** 그런데 전부 static 메서드만 있는 클래스의 경우, 이를 바탕으로 인스턴스로 만들어봐야 그 인스턴스는 빈껍데기만 있는 객체에 불과합니다. 실제로 인스턴스로 만드는 시도조차 안하실거구요. 그렇다면 해당 클래스는 클래스로서가 아니라 일반 객체로서의 의미밖에 없는 것이니, static으로만 구성된 클래스는 차라리 처음부터 객체로 만드는 편이 좋다고 생각합니다.

<br>

### 3-4. 상속

- [[#109](https://github.com/woowacourse/javascript-lotto/pull/109#discussion_r813851496)] 부모 클래스가 자식 클래스의 도메인 명을 알고 있지 않도록 주의
  <br>

### 3-5. new.target

- [#82] `new.target` 사용

  - [new.target](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/new.target) - new.target 속성(property)은 함수 또는 생성자가 new 연산자를 사용하여 호출됐는지를 감지할 수 있습니다. new 연산자로 인스턴스화된 생성자 및 함수에서, new.target은 생성자 또는 함수 참조를 반환합니다. 일반 함수 호출에서는, new.target은 undefined입니다.

---

## 4. DOM, 이벤트

### 4-1. DOM

- [#86] Element가 받는 value 중 Number Input의 경우 value 대신 `valueAsNumber` 프로퍼티를 사용할 수도 있다.
  <br>
- [#113] SVG vs PNG
  - 이미지를 svg로 사용하게 되면 좋은 이유:
  1.  모든 크기의 브라우저를 원할하게 지원한다. (무한한 확장이 가능)
  2.  svg일부를 스타일링 할 수 있다.
  3.  dom을 사용하여 svg를 실시간으로 수정할 수 있다.
  4.  용량이 적어 출력이 빠르다.
      이미지를 png로 사용해야하는 경우:
  - 백그라운드 이미지와 같이 이미지를 벡터화하기 어려울 경우
    디자이너가 넘겨주는 파일을 사용하는게 맞겠지만,
    백그라운드 이미지와 같이 이미지를 벡터화하기 어렵다면 png를 사용하고 그렇지 않은 경우에는 svg를 사용하는 편이 좋은 거군요?!
    <br>

### 4-2. 이벤트 리스너

- [#103] 이벤트 리스너 내부 익명함수 -> 핸들러 분리
  - 이벤트 리스너는 원래 `등록`과 `해제`를 함께 고려해야 한다.
    - 해제해주지 않으면, 메모리가 낭비될 뿐만 아니라 경우에 따라서 중복 등록되어 이벤트에 대해 함수가 여러번 호출될 수 있다.
  - 익명함수를 사용할 경우 해제를 할 수 없기때문에 핸들러로 분리해야 한다.

---

## 5. 클린코드

### 5-1. 인자

- [#84] 코드를 한 줄로 짜는 것(반환값을 다시 함수의 인자로 넣고 그 반환값을 다시 함수의 인자로 넣는 상황)도 좋지만, 뒤에서부터 읽고 반환값을 추측해야해서 코드를 이해하는 데에는 힘든 점이 있다. 함수에서 반환된 값은 따로 변수에 할당하고, 그 변수를 다른 함수에게 인수로 넘겨주도록 분리하자.
  <br>
- [#86] 안 쓰는 인자는 언더바(\_)를 사용하면 남들이 이해하기 쉽다.

  ```jsx
        (e, idx) => idx + 1,  // before
        (_, idx) => idx + 1,  // after
  ```

<br>

### 5-2. 불필요한 메서드

- [#103] 불필요한 초기화 메서드를 만들지 마라.

  - initDom(), initEventListener() 등 불필요한 메서드를 만들지 말고, constructor에서 초기화를 진행해라.

  <br>

- [#116] switch 문
  - break를 하기보다 return을 바로 하는게 더 안전하지 않을까요? 또한 break를 하고 마지막에 return하기보다 액션이 없는경우 default로 return 하는것이 더 명료할것 같아요.

<br>

### 5-3. 네이밍

- [[#99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814071346)] 데이터가 변경될 것을 대비해서 그 데이터를 대표하는 단어를 함수에 쓰지 말 것
  <br>
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

- [#82] 하나의 로또 티켓에 들어가는 번호가 6개라는 의미로, 6을 값으로 갖는 `LOTTO_NUMBER_LENGTH`라는 변수명에서 `length`는 '자릿수'를 의미하는 것으로 오해할 여지가 있으므로, `length`를 'amount'로 변경해보는 것도 고민해보자.
  <br>

- [#104] 네이밍에 전치사?
  - 클린코드 원칙에는 전치사 사용을 자제하는것을 추천한다.
    <br>
- [#96] `this.model` 같은 추상적이고 모호한 이름보다 사용하는 곳에서 바로 알 수 있도록 구체적인 이름으로 바꾸는 것이 좋다.
  <br>

- [[#94](https://github.com/woowacourse/javascript-lotto/pull/94#discussion_r815283358)] `Controller`와 같은 일반적인 이름을 사용한다면 라이브러리인지, 구현체인지 헷갈리기 쉽다. `LottoController`와 같이 구체적으로 작명해줄 것.
  <br>
- [[#100](https://github.com/woowacourse/javascript-lotto/pull/100#discussion_r814729214)] 함수의 네이밍은 동사/명령형
  <br>
- [#83] - set

  - setOnSubmitCash 네이밍도 나쁘진 않은 것 같은데 아무래도 네이밍 앞에 set이 붙으면 이벤트 바인딩보다는 내부에 있는 상태를 설정하는 느낌이 드네요
    <br>

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

### 5-4. 상수화

- [#91] 상수는 굳이 멤버로 할당할 필요가 없다.
  ```javascript
  this.lottoPrice = LOTTO_PRICE;
  ```

<br>

- [[#99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814070274)] 리뷰어님이 기준이 명확하다고 한 상수화

  1. 재사용과 변경이 될 수 있는 값을 상수화한다.
  2. 그 자체로 의미를 알아볼 수 있는 값이면 상수화하지 않아도 된다.
     <br>

- [#98] [리뷰어 질문] : selector 도 모두 상수화하신 게 인상깊네요. 개인적으로는 대문자 상수를 많이 쓰게 되면 가독성이 오히려 떨어지는 느낌인데, 쓰면서 어떠셨나요?

  - 해당 크루의 답변
    - 가독성은 좋지는 않다.
    - 추후 선택자를 수정사항이 생기면, 한 번에 변경이 가능하다.
    - vscode의 자동 표기법이 편하다.

  <br>

- [[#99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814079068)] 테스트 코드에도 상수를 사용하여 로또 가격이 바뀔 것을 고려할 것
  <br>
- [[#99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814082393)] 커스텀 이벤트를 사용한 경우 커스텀 이벤트가 많아질 경우를 대비해 상수화 할 것

<br />

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

---

## 6. HTML, CSS

### 6-1. HTML

- [[#102](https://github.com/woowacourse/javascript-lotto/pull/102#discussion_r815262983)] [Heading](https://developer.mozilla.org/ko/docs/Web/HTML/Element/Heading_Elements#%EC%82%AC%EC%9A%A9_%EC%9D%BC%EB%9E%8C) 단계를 뛰어넘지 말 것. 순차적으로 기입하세요. Heading은 글자 크기를 위해 존재하는 게 아닙니다.
  <br>
- [[#102](https://github.com/woowacourse/javascript-lotto/pull/102#discussion_r815269072)] `label`과 `input`을 `for`로 연결해줄 것
  <br>
- [[#100](https://github.com/woowacourse/javascript-lotto/pull/100#discussion_r815269744)] `innerHTML`은 XSS 공격에 취약하고, 무겁고 비싸다. 대신 `createElement`나 `insertAdjacentHTML`를 사용할 수 있다.
  <br>

### 6-2. CSS

- [#97] 0px
  - margin,padding 같은 속성에서 0으로 속성을 줘야할 때 px을 주지 않는 것이 성능상 유리하다.
    <br>
- [#116] CSS 선택자

  - 순서에 의존하는 선택자는 지양
  - lotto-list > . Lotto-list-container . 와 같은 선택자는 지양
    <br>

- [#111] css 파일명 원칙
  - 보통 css 파일명으로 카멜케이스를 사용하지 않는다.
    <br>
- [[#109](https://github.com/woowacourse/javascript-lotto/pull/109#discussion_r813836290)] 기본 태그 (`input`, `p` 등)으로 스타일을 조정하면 서비스가 커질 때 사이드 이펙트를 발생시킬 확률이 높다.
  <br>
- [[#90](https://github.com/woowacourse/javascript-lotto/pull/90#discussion_r813975297)] user agent stylesheet를 고려한 [reset.css](https://abcdqbbq.tistory.com/9)
  <br>
- [[#94](https://github.com/woowacourse/javascript-lotto/pull/94#pullrequestreview-894384954)] css를 HTML에 link를 통해 import할 경우 css의 rule이 전역에서 사용된다. 이는 사이드 이팩트를 일으킴. 이런 문제를 해결하기 위해 [css 모듈화](https://blog.toycrane.xyz/css%EC%9D%98-%EC%A7%84%ED%99%94-%EA%B3%BC%EC%A0%95-f7c9b4310ff7)가 진행
  <br>
- [[#95](https://github.com/woowacourse/javascript-lotto/pull/95#discussion_r814778236)] js에서 직접 스타일을 조정하지 않고 class를 달아주고 제거하는 방식을 택하자.
  <br>
- [#86] 중복되는 CSS 컬러는 CSS 변수로 관리해보자.

  - https://developer.mozilla.org/ko/docs/Web/CSS/Using_CSS_custom_properties
    <br>

- [#115] 공통적으로 쓰이는 색상은 global css variable로 만들자.

  - 추후 개발을 진행하거나 했을때 일관적으로 색상 팔레트가 관리되고 있지 않다면, 찾아다니는게 대규모 프로젝트에선 생각외로 힘들다.

---

## 7. 테스트

- [[#99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r815802271)] TDD의 잔재주의! 테스트는 실제 모듈로 진행하자.
  <br>
- [[#99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r816019589)] 성공을 보장해야하는 테스트도 존재한다.
  <br>
- [[#102](https://github.com/woowacourse/javascript-lotto/pull/102#pullrequestreview-894376083)] 테스트코드를 Given, When, Then 구조로 구체적인 설명을 적는다면 역할과 흐름이 잘 보여진다.
  <br>
- [[#94](https://github.com/woowacourse/javascript-lotto/pull/94#discussion_r816456100)] 경계값에 대한 테스트도 다양하게 작성해보자.
  <br>
- [#105] 단위테스트는 어떤 기능 하나에 대한 테스트라고 볼 수 있으므로, 각 파일별로 테스트 파일을 작성하여, 그 파일별로 테스트 코드를 응집하는 게 나을 수 있다.
  <br>
- [#92] private 메서드는 세부 기능이기 때문에 특별한 경우가 아니라면 테스트 작성하지 않는 편이다. [링크 참고](http://shoulditestprivatemethods.com)
  <br>
- [#92] 랜덤테스트의 가장 큰 문제는 검증을 실패하거나 성공했을 때 왜 성공했는지? 왜 실패했는지? 동일한 상황 재현이 어렵다는 것이다.

  - 랜덤테스트를 진행한다면 여러 테스트 케이스의 히스토리를 캡쳐하고 그 기록을 관리하는 체계가 필요하다.
    <br>

- [#92] 랜덤테스트를 안하려면 숫자를 테스트할 때 수동으로 입력해줘야 한다.

  - mock, spyOn 등의 키워드로 검색하여 방법 강구

        > Mock 객체란 모듈의 겉모양이 실제 모듈과 비슷하게 보이도록 만든 가짜 객체이다. 모듈이 필요로 하는 의존성이 테스트 케이스 작성을 어렵게 만드는데, 이 의존성을 단절하기 위해 Mock 객체가 사용된다. https://eminentstar.github.io/2017/07/24/about-mock-test.html

        > mocking에는 스파이(spy)라는 개념이 있습니다. 현실이나 영화 속에서 스파이라는 직업은 “몰래” 정보를 캐내야 합니다. 테스트를 작성할 때도 이처럼, 어떤 객체에 속한 함수의 구현을 가짜로 대체하지 않고, 해당 함수의 호출 여부와 어떻게 호출되었는지만을 알아내야 할 때가 있습니다. 이럴 때, Jest에서 제공하는 jest.spyOn(object, methodName) 함수를 이용하면 됩니다. https://www.daleseo.com/jest-fn-spy-on/

        ```jsx
        const calculator = {
          add: (a, b) => a + b,
        };

        const spyFn = jest.spyOn(calculator, "add");

        const result = calculator.add(2, 3);

        expect(spyFn).toBeCalledTimes(1);
        expect(spyFn).toBeCalledWith(2, 3);
        expect(result).toBe(5);
        ```

    <br>

- [#81] 유틸 함수에 대한 테스트에 가까울 경우, 서비스 로직 테스트와 분리하는 것이 더 분명한 의미를 전달할 수 있다.

---

## 8. 웹 접근성, UI/UX

### 8-1. 웹 접근성

- [#110] hidden
  - 스크린리더기에서 읽혀야 하지만, 화면에 보이지 않아야 할 때는 어떻게 해야할까?
  - CSS의 visibility hidden 속성을 주도록 한다.
  - 따라서 , section 내부 타이틀을 설정하고 , hidden 속성을 주도록 한다.
    <br>

### 8-2. UI/UX

- [[#101](https://github.com/woowacourse/javascript-lotto/pull/101#discussion_r814892644)]디자인을 이유로 input의 focus를 알려주는 outline을 삭제하지 말 것. 어떤 요소에 접근했는지 알려주는 역할이기 때문이다. 이는 웹 접근성 관련
  <br>
- [[#100](https://github.com/woowacourse/javascript-lotto/pull/100#discussion_r814748074)] 버튼이 disable일때는 표시해주자.

---

## 9. 기타

### 9-1. 파일 분리

- [#81] utils, constants에서 파일을 많이 생성하여, 코드들을 분리하는 것도 명확하다는 장점이 있다.
  <br>

- [[#102](https://github.com/woowacourse/javascript-lotto/pull/102#discussion_r815270046)] 도메인에 속해있는 로직은 util로 분리하지 마세요. 범용적인 성격을 가진 아이들만 분리해주세요.
  <br>

### 9-2. 패키지

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

### 9-3. 새로운 접근

- [#114] 꼭, constructor에서 element에 접근해서 멤버변수로 해야할까?

  - HTML상에 위 element의 생명주기가 JS의 생명주기보다 먼저 시작해서 끝까지 살아있다는 전제하에서는, 위 값을 상수 또는 변수로 만들어두면 안될까요?
    `javascript const ELEMENTS = { ... SWITCH_INPUT: $(".switch-input") } `
    <br>

- [#83] 로또 번호 추첨 함수
  - 더 나아가서 Set을 통해서 중복되었을 때 한번 더 루프를 도는 방식이 아니라 무조건 NUMBERS_COUNT번만에 끝낼 수 있는 방식으로 구현할 수 있을까요? 실제로 로또 번호를 추첨하는 방식처럼요!
  - 1~45까지의 배열에서 최대값이 배열의 크기인 랜덤한 수를 얻어 해당 index를 기반으로 spliced를 통해 로또 번호를 뽑는 방식
    <br>

### 9-4. 좋은 말

- [[#100](https://github.com/woowacourse/javascript-lotto/pull/100#pullrequestreview-894510935)] `You Aren't Gonna Need It` - 처음부터 확장성이나 미래를 심히 고려해 오버엔지니어링하는 걸 경계하자.
  <br>
