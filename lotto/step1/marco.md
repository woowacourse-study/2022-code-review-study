# Level1 Lotto Step1(담당 PR 번호들) - 마르코

- 분석 담당 코드
  - 준찌 [#81](https://github.com/woowacourse/javascript-lotto/pull/81)
  - 유세지
  - 동키콩
  - 온스타
  - 위니
  - 자스민
  - 아놀드
  - 빅터
  - 코이

## 아키텍처 분석(desc: 담당한 4개의 소프트웨어의 아키텍처를 간단히 분석하고 설명합니다)

### [#81] 준찌

#### Controller

- 진입점인 index.js가 바로 부르는 RacingGameManager() 클래스에서 .start() 메서드로 시작한다. 해당 클래스는 컨트롤러 역할인 듯하다.

  - start 메서드는 `다른 클래스 인스턴스 생성`, `dom요소 가져오기`, `event 바인딩`을 init한다.

    - (1) model과 view 클래스 인스턴스 생성하여 멤버변수로 할당
    - (2) DOM 요소 가져와서 멤버변수 할당

      - constructor가 없다. 메서드 내부에서 멤버 변수 만든다.

    - (3) 선택된 DOM 요소에 이벤트 바인딩
      - [네이밍] 핸들러 네이밍을 할 때, 접두사로 on을 붙인다. `onSubmitChargeInputForm`

- controller는 이벤트를 통해 발생한 데이터를 가공하고, view의 렌더링 메서드를 호출하며 그 데이터를 매개변수로 전달한다.

#### View

- constructor 내부에 dom 요소를 멤버변수 할당하지 않고, 프라이빗 메서드 내부에서 dom 요소를 멤버변수로 할당하여 사용하고 있다.
- render하는 메서드들은 매개변수로 렌더링할 데이터를 (컨트롤러로부터) 받고 있다.
- view가 model에 의존하고 있지 않다.

#### model

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

### 대분류(ex: 아키텍처, 함수/클래스, 컨벤션, DOM, 테스트 등)

### 아키텍처

- [#81] Model의 getter 함수에서 DeepCopy를 수행하는 것에 대해 어떻게 생각하시나요 ?
  - 질문설명: get으로 가져오는 멤버 변수는 불러온 사용처에서 변경을 가하더라도 영향이 가지 않았으면 해요. 사용된 곳에서 가한 변경이 객체의 멤버에 직접 전달되어 버린다면, 예상치 못한 부수효과들이 발생할 것 같거든요 하지만 DeepCopy 하는 로직의 경우 객체의 레이어가 깊을수록, 데이터가 많을수록 비용이 너무 커질 것 같아 우려되기도 합니다.
  - 답변: 현재 코드에서 얼마나 DeepCopy된 모델의 데이터가 필요한가? 가 기준이 될 것 같은데요. 그만큼 모델 멤버 객체의 뎁스가 깊지 않다면 굳이 모델의 getter함수에서 DeepCopy를 수행하는 것이 유의미한지 잘 모르겠습니다.

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

#### 함수/클래스

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

### 함수/클래스

- [#81] Prototype에 커스텀 메소드를 추가해서 사용하는 것 주의
  - Prototype에 커스텀 메소드를 추가해서 사용하는 것은, 내장 객체가 가지는 전역 범위때문에 생길 수 있는 변경 가능성 때문에 별로 추천하는 방법이 아니다. 또한, 유틸성 함수의 역할로 쓰려는 목적이라면 명시적으로 유틸 함수를 정의하고 모듈화하는 것이 더 좋다.

```jsx
Array.prototype.deepCopy = function () {
  return JSON.parse(JSON.stringify(this));
};
```

### 컨벤션

- [#81] utils, constants에서 파일을 많이 생성하여, 코드들을 분리하는 것도 명확하다는 장점이 있다.

### 테스트

- [#81] 유틸 함수에 대한 테스트에 가까울 경우, 서비스 로직 테스트와 분리하는 것이 더 분명한 의미를 전달할 수 있다.
