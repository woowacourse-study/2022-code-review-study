Level번호 미션명 Step번호(담당 PR 번호들) - 호프
- 분석 담당 코드
    - [#83-시지프 ](https://github.com/woowacourse/javascript-lotto/pull/83)
    - [#89-호프 ]([https://github.com/woowacourse/javascript-lotto/pull/89)
    - [#116-티거](https://github.com/woowacourse/javascript-lotto/pull/116)
    - [#119-병민](https://github.com/woowacourse/javascript-lotto/pull/119)
    - [#112-도리](https://github.com/woowacourse/javascript-lotto/pull/112)
    - [#113-꼬재](https://github.com/woowacourse/javascript-lotto/pull/113)
    - [#107-소피아](https://github.com/woowacourse/javascript-lotto/pull/107)
    - [#110-우연](https://github.com/woowacourse/javascript-lotto/pull/110)
…

## 아키텍처 분석

### #83 시지프
![](https://i.ibb.co/f4YqcNz/IMG-1597.jpg)
- Model, Controller, View 로 분리 
#### Model
- 발급받은 로또를 관리한다.
- 발급될 로또 수량을 매개변수로 받으면 로또 넘버를 생성해 준 후, 저장한다.

#### Controller
- View로 넘겨줄 이벤트 핸들러를 정의한다.
- 이 때, 이벤트 핸들러는 여러가지의 View와 Model을 조작하는 역할을 한다. 
- View에 이벤트 핸들러를 넘겨줄 때는 아래와 같이 해당하는 View의 `bindSubmitCash` 메서드의 인자로서 핸들러를 넘겨준다. 

```javascript
#bindEventHandlers() {
    this.#purchaseFormView.bindSubmitCash(cash => this.#handleSubmitCash(cash));
  }
```

#### View
- 해당 영역(section)의 이벤트 핸들러를 붙여준다.
- 이벤트가 View에서만 관리될 수도 있고, 필요시 위의 Controller로부터 추가 핸들러를 받아온다.

- 전형적인 MVC패턴으로 Model 과 View를 Controller가 연결해준다.
- Model과 View 간의 영역 침범(?)이 없다. 둘은 서로의 존재를 아예 모른다.



### #89 호프 
-  위의 83번 페어코드와 다른 점만 설명합니다.
```javascript
export default class View {
  constructor(props = {}) {
    this.props = props;
    this._init();
  }
  _init() {
    this._configureDOM();
    this._bindEvents();
  }
  _bindEvents() {}
  _configureDOM() {}
}
```
- View 추상클래스를 정의해서, 반복되는 부분을 제거한다.
- configureDOM : 해당 View에서 필요한 Dom Element를 선택하는 영역
- bindEvents : 해당 View 에서 필요한 이벤트 리스너를 붙이는 영역
- 객체가 생성되면 필요한 Dom Elemet를 선택하고, 이벤트 리스너를 붙인다. 
```javascript
  #purchaseFormView = new PurchaseFormView({
    submitCashHandler: cash => this.#submitCashHanlder(cash),
  });
```
- #83 의 코드에서는 Controller 의 이벤트 핸들러를 View 로 전달할 때, 해당 View 인스턴스의 bindEvents 메서드를 직접 실행한 반면, 해당 코드에서는 View의 생성자 매개변수(props)로 필요한 이벤트 핸들러를 전달
- 로또 번호를 생성하는 함수는 모델 -> util로 분리 
- 왜 ? 로또 번호 생성은 엄밀히 말하면 Model의 역할이 아니라고 판단했기 때문입니다. Model은 `데이터를 생성하고 저장`하는 영역이라고 생각했기 때문에 로또 번호를 만드는 로직을 Model 클래스가 알 필요가 없다고 생각합니다. 추후에 로또 번호 생성 로직이 달라질 수도 있구요.



 
### #116 티거 / #119 병민 
![](https://i.ibb.co/J2mQPGS/IMG-1601.jpg)
- flux 패턴을 사용하여 Component를 Store의 구독자로 등록. Store 내부의 상태가 변경될 시, Store의 구독자 Component를 모두 리렌더링하여 새로운 상태를 반영하도록 구현

#### Store
```javascript
class Store {
  #subscribers = [];
  #state = {};

  constructor(initialState) {
    this.#state = initialState;
  }

  subscribe(component) {
    this.#subscribers.push(component);
  }

  dispatch(action) {
    const newState = reducer(this.getState(), action);
    this.#setState(newState);
    this.#subscribers.forEach((subscriber) => {
      subscriber.notify();
    });
  }
  getState() {
    return this.#state;
  }

  #setState(newState) {
    this.#state = newState;
  }
}
```

- 생성자에서 초기 상태값을 받아온다.
- subscribe  : 해당 메서드를 통해 원하는 컴포넌트를 구독자로 등록한다.
- dispatch : 컴포넌트에서 상태를 변경 할 때 실행하는 메서드. action을 매개변수로 받고, 등록된 action에 따라 새로운 상태값이 reducer로부터 반환되고 상태가 업데이트 된다. 업데이트 된 후, 구독자 Component들에게 notify로 상태값이 변경되었음을 알린다.


#### Reducer
```javascript
export default function reducer(state, { type, payload }) {
  const newState = { ...state };
  switch (type) {
    case ACTION.PURCHASE_LOTTO: {
      newState.money = payload;
      newState.lottoList = generateLottoList(payload);
      return newState;
    }
    case ACTION.TOGGLE_LOTTO_LIST:
      newState.lottoListVisibility = payload;
      return newState;
    case ACTION.SET_WINNING_NUMBERS:
      newState.winningNumbers = payload;
      return newState;
    default:
      return state;
  }
}
```

- 위의 Store에서 사용된 Reducer 함수
- 액션 타입에 따라서 다른 상태값을 받아온다.
- payload 는 액션과 함께 받아온 새로운 데이터를 의미한다. 
- 알려지지 않은 액션타입이 들어온 경우 기존의 상태를 반환한다.

#### Component
```javascript
class Component extends HTMLElement {
  connectedCallback() {
    this.render();
    this.subscribe();
    this.setEvent();
  }
  render() {
    this.innerHTML = this.template();
  }
  template() {
    return '';
  }

  subscribe() {
    window.store.subscribe(this);
  }
  setEvent() {}

  addEvent(eventType, selector, callback) {
    const children = [...this.querySelectorAll(selector)];
    const isTarget = (target) => children.includes(target) || target.closest(selector);
    this.addEventListener(eventType, (event) => {
      if (!isTarget(event.target)) {
        return false;
      }
      return callback(event);
    });
  }
  notify() {
    this.render();
  }
  hide() {
    this.setAttribute('hidden', true);
  }
  show() {
    this.removeAttribute('hidden');
  }
}
```
- Custom Element 를 사용하였다.
- Component 추상 클래스가 애초에 HTMLElement 를 상속받는다.즉, Component 자체가 하나의 Element를 의미한다. 
- connectedCallback : Custom Element의 라이프사이클 중 하나로, Custom Element가 등록될 때 제일 처음 실행되는 메서드
- render : 해당 Element의 innerHTML을 수정한다.
- subscribe: 해당 Component 생성과 함께 실행되어, Component를 위에서 생성된 Store의 구독자로 등록한다.
- setEvent: 아래의 addEvent 메서드를 호출한다.
- addEvent: 이벤트 타입, 셀렉터, 이벤트 핸들러 콜백 함수를 받아온다. 이벤트 리스너를 맨 위의 root (this) 에 등록하고, eventTarget 을 검사하여 콜백을 실행한다.
- notify : Store에서 상태 변경시 실행되어 리렌더링 한다. 

- 개인적인 의견으로 아쉬운 점은 모든 컴포넌트가 하나의 스토어를 바라보고 있다는 점입니다. 물론 로또앱 자체가 작아서 리렌더링이 심하게 일어나지는 않지만, 로또 상태와 관련이 없는 불필요한 부분에도 리렌더링이 지속적으로 일어날 가능성이 높아서 아쉽습니다.  상태에 상관 없는 부분, 예를들면 Form 같은 부분은 Container 로 또 다른 추상 클래스를 정의해서 상속 받아서 불필요한 notify를 줄이는건 어떨까요 ? 
- 혹은 해당 Store의 상태를 직접적으로 참조하는 Component만 리렌더링되게 하는 방법이 있지 않을까요? 


### #112- 도리 / 113- 꼬재 
![](https://i.ibb.co/jyd95B1/IMG-1602.jpg)
- LottoApp 영역에서 이벤트 바인딩, 핸들링, UI 조작 등을 모두 담당한다.
- LottoApp 에서 LottoList 라는 데이터를 관리하는데, LottoList 는 Lotto 객체의 배열로 이루어져 있다.
- index.html에 다른 Element가 등록되어 있지 않기 때문에 이벤트를 app 엘리먼트 (최상위) 에 바인딩 시켜준 점이 인상깊다. 

#### bindEventListener 함수 
```javascript
export const bindEventListener = ({ appElement, type, selector, callback }) => {
  const children = [...getElements(selector)];
  const isTarget = (target) =>
    children.includes(target) || target.closest(selector);
  appElement.addEventListener(type, (e) => {
    if (!isTarget(e.target)) return;
    e.preventDefault();
    callback(e);
  });
};
```

- 위의 함수가 클로져를 잘 사용했다고 생각해서 발췌
- app에 이벤트를 바인딩 시킨다.
- 받은 selector를 사용해서 app 내부에 있는 selector에 해당하는 모든 element를 가져온다.
- isTarget 함수를 클로져로 저장하여, 아래 이벤트 리스너 콜백에서 현재 event.target이 등록된 이벤트 타겟인지 검사한다.
- 아쉬운 점이 있다면 ! e.preventDefault(); 호출을 type 이 submit에만 해줘도 되지 않을까? 하는 점


###  #107- 소피아 / 110 - 우연 
![](https://i.ibb.co/TWST6Nz/IMG-1603.jpg)
- LottoMachine, LottoManager, LottoMachineView, Lotto 클래스로 나뉘어진다.
- MVC 패턴으로 보이지만, 클래스 이름을 도메인과 관련하여 지으니까 훨씬 유의미 하다는 것을 깨달았다.

#### LottoMachine
- lottoManager (Model) 과 lottoMachineView (View) 인스턴스를 생성한다.
- 이벤트를 바인딩하고 처리한다.

#### LottoMachineView
- UI 조작을 담당한다.

#### LottoManager
- 모델 역할을 하여 로또를 생성한다.
- lottos [] 배열은 Lotto 객체 배열이다.

#### Lotto
- 로또 번호를 생성한다. 


## 피드백 정리
### 대분류(ex: 아키텍처, 함수/클래스, 컨벤션, DOM, 테스트 등)
### 컨벤션
- [#116] switch 문 
	- break를 하기보다 return을 바로 하는게 더 안전하지 않을까요? 또한 break를 하고 마지막에 return하기보다 액션이 없는경우 default로 return 하는것이 더 명료할것 같아요.

### 클래스
- [#83]  은닉화 
	- 외부에서 사용하지 않는 변수나 메서드는 무조건 private 으로 처리해주면 좋은가?
	- 외부에서 알 필요가 없는 메서드라면 private로 처리해주는게 맞다고 생각합니다. 예를들어 전기포트가 있다고 할 때 저희는 전원을 키고 끄는법에 대해서만 알면되고 내부에서 어떻게 전기포트에서 열이 올라서 물을 끓이는지 세부적인 내용에 대해 사용자가 알 필요가 없는 부분과 같은 맥락이라 생각합니다.중요한 데이터를 외부에서 건드릴 수 없도록 + 외부에서 사용하지 않는다의 맥락이 모두 위의 예시에 빗대어 생각해보시면 어느정도 윤곽이 잡히시지 않을 까 생각해봅니다. 
- [#112] 생성자의 파라미터로 넘기기 vs setter 사용하기
	- 생성자의 파라미터로 넘기는 건, 인스턴스를 생성할 때부터 값을 셋팅해주는거고, 기본적으로 OOP 에서는 외부에서 직접적으로 객체의 데이터에 접근하는 것을 막기 위해 setter 를 사용합니다. (같이 봐야하는 개념으로 private 이 있습니다) 만약 외부에서 변경이 가능하다면 우리가 정의한 객체의 무결성이 깨질 수 있기 때문입니다. 예를 들어, lottoList 가 private 하지 않는다면 아래와 같이 객체를 깨뜨릴 수 있겠죠. 그렇기 때문에 setter 를 사용합니다..!

### 함수
- [#83] 함수 분리 기준
	- 함수를 분리하지 않더라도 충분히 이해할 수 있는 범주의 역할인데다가 과도한 분리는 오히려 유지보수를 어렵게 만든다는 생각을 하고있기에 코멘트를 드렸습니다. 혹 재사용을 하더라도 분리할 필요성이 떨어지는 함수라고 생각이 들어요. 우선은 제가 함수를 분리하는 관점은 기능 확장 될 여지가 있거나 내부의 구현이 복잡하다면 추상화를 통해 미리 해서 분리한다. 기능이 확장 될 여지가 없거나 코드가 짧거나(코드를 처음 봤을때 맥락이 애해되는 범주) 재사용성이 떨어진다면 분리하지 않는 방향성들을 고려해보고 판단하는게 맞는 방향이라고 생각됩니다 


### 도메인 관련 함수 
- [#83] 로또 번호 추첨 함수
	-  더 나아가서 Set을 통해서 중복되었을 때 한번 더 루프를 도는 방식이 아니라 무조건 NUMBERS_COUNT번만에 끝낼 수 있는 방식으로 구현할 수 있을까요? 실제로 로또 번호를 추첨하는 방식처럼요!
	-  1~45까지의 배열에서 최대값이 배열의 크기인 랜덤한 수를 얻어 해당 index를 기반으로 spliced를 통해 로또 번호를 뽑는 방식
	
- [#112] validation 함수 네이밍 및 역할 관련
```javascript
const isDivisibleBy = (payment, price) => {
  if (payment % price !== 0) {
    throw new Error(ERROR_MESSAGE.MONEY_OUT_OF_STANDARD)

  }
  return parseInt(payment / price);
};
``` 
	- 저는 파라미터가 두번째 파라미터로 나누어 떨어져? 라고 물어봤는데 응/아니야 라고 대답하는 게 아니라 Error! / 계산된 값 줄게 으로 대답하는 게 조금은 이상한데 어떻게 생각하시는 지 궁금합니다…! isDivisibleBy 보단 차라리, divideBy 가 어떨까 싶어요..! (이걸로 나눠줘! -> 불가능 그러니까 에러 발생시킬게! / 그래 여기)
- 
### 네이밍
- [#83] - set
	- setOnSubmitCash 네이밍도 나쁘진 않은 것 같은데 아무래도 네이밍 앞에 set이 붙으면 이벤트 바인딩보다는 내부에 있는 상태를 설정하는 느낌이 드네요 



### 아키텍쳐

### DOM
- [#113] SVG vs PNG
	- 이미지를 svg로 사용하게 되면 좋은 이유:
	1. 모든 크기의 브라우저를 원할하게 지원한다. (무한한 확장이 가능)
	2. svg일부를 스타일링 할 수 있다.
	3. dom을 사용하여 svg를 실시간으로 수정할 수 있다.
	4. 용량이 적어 출력이 빠르다.
	이미지를 png로 사용해야하는 경우:
	* 백그라운드 이미지와 같이 이미지를 벡터화하기 어려울 경우
	디자이너가 넘겨주는 파일을 사용하는게 맞겠지만,
	백그라운드 이미지와 같이 이미지를 벡터화하기 어렵다면 png를 사용하고 그렇지 않은 경우에는 svg를 사용하는 편이 좋은 거군요?! 

### 테스트 

### CSS
- [#116] CSS 선택자
	- 순서에 의존하는 선택자는 지양
	- lotto-list > . Lotto-list-container . 와 같은 선택자는 지양
- [#97] 0px 
	- margin,padding 같은 속성에서 0으로 속성을 줘야할 때 px을 주지 않는 것이 성능상 유리하다.

### 웹 접근성
- [#110] hidden 
	- 스크린리더기에서 읽혀야 하지만, 화면에 보이지 않아야 할 때는 어떻게 해야할까?
	- CSS의 visibility hidden 속성을 주도록 한다.
	- 따라서 , section 내부 타이틀을 설정하고 , hidden 속성을 주도록 한다.


