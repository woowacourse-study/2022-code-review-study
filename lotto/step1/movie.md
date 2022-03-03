# LEVEL 1 행운의 로또 STEP 1 - 무비😎

## 분석 담당 코드

- 마르코 [PR 101](https://github.com/woowacourse/javascript-lotto/pull/101) X 무비 [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99)
- 밧드 [PR 102](https://github.com/woowacourse/javascript-lotto/pull/102) X 우디 [PR 109](https://github.com/woowacourse/javascript-lotto/pull/109)
- 앨버 [PR 90](https://github.com/woowacourse/javascript-lotto/pull/90) X 결 [PR 87](https://github.com/woowacourse/javascript-lotto/pull/87)
- 나인 [PR 95](https://github.com/woowacourse/javascript-lotto/pull/95) X 비녀 [PR 94](https://github.com/woowacourse/javascript-lotto/pull/94)
- 샐리 [PR 100](https://github.com/woowacourse/javascript-lotto/pull/100)

<br />
<br />

## 아키텍처 분석

### 마르코 [PR 101](https://github.com/woowacourse/javascript-lotto/pull/101) X 무비 [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99)

- MVC 패턴
- custom 이벤트를 subscribe하는 형식
  - `emit`으로 이벤트를 송신하고 `on`으로 이벤트를 수신한다.
  - controller가 직접적으로 dom 이벤트를 처리하는 방식이 아닌, view가 dom 이벤트를 controller에게 알리고, controller가 처리하는 방식

### 밧드 [PR 102](https://github.com/woowacourse/javascript-lotto/pull/102) X 우디 [PR 109](https://github.com/woowacourse/javascript-lotto/pull/109)

- MVC 패턴
- 큰 controller가 존재하고 그 아래에 sub controller가 존재하는 방식, sub controller는 생성자의 매개변수로 큰 controller의 인스턴스를 받는다.
  - PR 109에서는 [core](https://github.com/woowacourse/javascript-lotto/tree/greenblues1190/src/js/core)에 부모 controller, model등이 존재하고 자식들이 상속받는 방식으로 변경됨
- view에 `addEventListener`를 등록하는 함수가 존재하고 그 함수를 controller에서 호출한다. controller에서는 로직을 처리하는 함수를 `callback`으로 전달한다.
- `index.html`에서 template를 만들어 놓고 `show`, `hide`하는 방식이 아닌, 이벤트가 발생할 때마다 `innerHTML`을 통해 template를 교체하는 방식

### 앨버 [PR 90](https://github.com/woowacourse/javascript-lotto/pull/90) X 결 [PR 87](https://github.com/woowacourse/javascript-lotto/pull/87)

- MVC 패턴
- `index.js`에서 model, view, controller를 선언해주는 방식이다.
- custom 이벤트를 subscribe하는 형식 (마르코, 무비와 같음)
- controller와 model은 하나씩 존재하고, view는 여러개

### 나인 [PR 95](https://github.com/woowacourse/javascript-lotto/pull/95) X 비녀 [PR 94](https://github.com/woowacourse/javascript-lotto/pull/94)

- MVC 패턴
- controller에서 이벤트를 등록해주고, model과 view에 존재하는 함수를 불러와 실행하는 방식
- controller와 view는 하나씩 존재하며, model은 두개 존재 (로또와 로또 더미들)
- controller를 `app.js`에서 생성해주고 controller의 생성자 안에서 model과 view를 생성하는 구조

### 샐리 [PR 100](https://github.com/woowacourse/javascript-lotto/pull/100)

- 드디어 MVC 패턴이 아니다!
- `app.js`에서 이벤트를 등록해주는 방식
  - `App`에서 validator라던지, view를 render해주는 모듈을 import해서 이벤트를 처리
  - `App`에 lotto에 대한 데이터, 사용자가 입력한 금액 등을 저장
  - 로직들이 있는 모듈은 `core`폴더에 존재, 이 곳에 `Lotto` 클래스도 존재

<br />
<br />

## 피드백 정리

😛 링크를 누르면 해당 discussion으로 이동! 😛

<br />

### MVC 패턴

- 컨트롤러에서 모델에게 전달받은 상태를 직접 뷰에게 전달하는 것이 적절하다. - [PR 102](https://github.com/woowacourse/javascript-lotto/pull/102#discussion_r815267289)
- 컨트롤러가 여러개의 view를 가질 수 있으니 확장성을 고려하며 설계할 것 - [PR 109](https://github.com/woowacourse/javascript-lotto/pull/109#discussion_r813851182)
- MVC 패턴 관련 공유 내용 - [PR 90](https://github.com/woowacourse/javascript-lotto/pull/90#issuecomment-1049971008), [PR 90 - 추가 코멘트](https://github.com/woowacourse/javascript-lotto/pull/90#issuecomment-1050580916)

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

- (_구조에 대한 생각?_) 리뷰어님의 개인적인 취향은 controller에서 model과 view 인스턴스를 생성하고, 생성하고 `index.js`에서 controller를 생성하는 편 - [PR 90](https://github.com/woowacourse/javascript-lotto/pull/90#issuecomment-1049971008)
- (_view와 controller중 어디에서 `addEventListener`를 해주는 게 맞을까요?_) 리뷰어님의 개인적인 취향은 DOM에 대한 이벤트 바인딩은 view에서 담당하고 그 이벤트에 대한 액션은 controller에서 하는게 좀 더 역할 분담이 명확해 보임 - [PR 90](https://github.com/woowacourse/javascript-lotto/pull/90#issuecomment-1049971008)

### OOP

- (_js에서 클래스를 통해서 OOP를 했을 때 가지는 장점이 있을까요?_) class를 사용하면 접근성이 좋아지고, 쉽고 일반적이다. - [PR 94](https://github.com/woowacourse/javascript-lotto/pull/94#pullrequestreview-894384954)

### 상속

- 부모 클래스가 자식 클래스의 도메인 명을 알고 있지 않도록 주의 - [PR 109](https://github.com/woowacourse/javascript-lotto/pull/109#discussion_r813851496)

### 상수화

- 리뷰어님이 기준이 명확하다고 한 상수화 - [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814070274)
  1. 재사용과 변경이 될 수 있는 값을 상수화한다.
  2. 그 자체로 의미를 알아볼 수 있는 값이면 상수화하지 않아도 된다.
- 테스트 코드에도 상수를 사용하여 로또 가격이 바뀔 것을 고려할 것 - [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814079068)
- 커스텀 이벤트를 사용한 경우 커스텀 이벤트가 많아질 경우를 대비해 상수화 할 것 - [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814082393)

<br />

### 변수

- (_메서드 안에 임시변수를 쓰는 것은 지양되어야 할까요?_) 리뷰어님의 개인적인 의견은 남이 유츄가 가능한지 아닌지로 임시변수를 선언할지 말지를 결정한다. - [PR 90](https://github.com/woowacourse/javascript-lotto/pull/90#issuecomment-1049971008)
- 예외의 값은 저장하지 않아도 된다. - [PR 90](https://github.com/woowacourse/javascript-lotto/pull/90#discussion_r813976935)

<br />

### 네이밍

- `Controller`와 같은 일반적인 이름을 사용한다면 라이브러리인지, 구현체인지 헷갈리기 쉽다. `LottoControlle`와 같이 구체적으로 작명해줄 것 - [PR 94](https://github.com/woowacourse/javascript-lotto/pull/94#discussion_r815283358)
- 함수의 네이밍은 동사/명령형 - [PR 100](https://github.com/woowacourse/javascript-lotto/pull/100#discussion_r814729214)

<br />

### 고차함수

- 반복이면 바로 `forEach`를 쓸 수 있지만, 만약 반환을 해줘야 하는 경우라면 `map`을 고려해볼 것 - [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814069099)
- 리뷰어님이 괜찮다고 해주신 커스텀 반복함수 - [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814030886)

  ```js
  const repeatCallback = (count, callback) => {
    for (let i = 0; i < count; i += 1) {
      callback();
    }
  };
  ```

- 템플릿을 만들 때 중복이 된다면 `map`을 고려해보자. - [PR 109](https://github.com/woowacourse/javascript-lotto/pull/109#discussion_r813861348)
- 순차적인 데이터로 배열을 채울 때 활용할 수 있는 내장함수 - [PR 109](https://github.com/woowacourse/javascript-lotto/pull/109#discussion_r816423887)

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

- 배열을 생성할 때 `map`을 생각해보자 - [PR 95](https://github.com/woowacourse/javascript-lotto/pull/95#discussion_r814757511)

<br />

### 함수

- 데이터가 변경될 것을 대비해서 그 데이터를 대표하는 단어를 함수에 쓰지 말 것 - [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814071346)
- 특정 값에 종속적인 함수는 분리할 필요가 없다. - [PR 101](https://github.com/woowacourse/javascript-lotto/pull/101#discussion_r815254485)
- 역할을 분리하는 것은 좋지만 역할을 분리함으로써 불필요한 코드가 더 생기진 않는지 생각해보자. - [PR 95](https://github.com/woowacourse/javascript-lotto/pull/95#discussion_r814765497)

<br />

### 콜백함수

- 콜백함수를 넘긴다는건 그 함수에게 역할을 위임 하겠다는 것 - [PR 102](https://github.com/woowacourse/javascript-lotto/pull/102#discussion_r815267289)

<br />

### 유용한 내장 함수

- 가격에 단위표시를 하고 싶다면 `toLocaleString()`을 사용해보자. - [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r815812894)
- 현재 문자열의 시작을 다른 문자열로 특정 길이만큼 채우고 싶다면 [`padStart()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/padStart)를 사용해보자. - [PR 95](https://github.com/woowacourse/javascript-lotto/pull/95#discussion_r814791814)

<br />

### 중복 판단

- 중복을 판단하는 로직을 작성하기보다는 Set을 활용해보자 - [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r814076018)

<br />

### 예외 처리

- `Throw`와 `try-catch`문을 함께 사용할 때 `alert`를 `catch`에 작성해주면 코드량도 줄고 유연한 구조가 된다. - [PR 94](https://github.com/woowacourse/javascript-lotto/pull/94#discussion_r815283789)

<br />

### 테스트

- TDD의 잔재주의! 테스트는 실제 모듈로 진행하자 - [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r815802271)
- 성공을 보장해야하는 테스트도 존재한다. [PR 99](https://github.com/woowacourse/javascript-lotto/pull/99#discussion_r816019589)
- 테스트코드를 Given, When, Then 구조로 구체적인 설명을 적는다면 역할과 흐름이 잘 보여진다. - [PR 102](https://github.com/woowacourse/javascript-lotto/pull/102#pullrequestreview-894376083)
- 경계값에 대한 테스트도 다양하게 작성해보자. - [PR 94](https://github.com/woowacourse/javascript-lotto/pull/94#discussion_r816456100)

<br />

### UI/UX

- 디자인을 이유로 input의 focus를 알려주는 outline을 삭제하지 말 것. 어떤 요소에 접근했는지 알려주는 역할이기 때문이다. 이는 웹 접근성 관련 - [PR 101](https://github.com/woowacourse/javascript-lotto/pull/101#discussion_r814892644)
- 버튼이 disable일때는 표시해주자. - [PR 100](https://github.com/woowacourse/javascript-lotto/pull/100#discussion_r814748074)

<br />

### HTML

- [Heading](https://developer.mozilla.org/ko/docs/Web/HTML/Element/Heading_Elements#%EC%82%AC%EC%9A%A9_%EC%9D%BC%EB%9E%8C) 단계를 뛰어넘지 말 것. 순차적으로 기입하세요. Heading은 글자 크기를 위해 존재하는 게 아닙니다. - [PR 102](https://github.com/woowacourse/javascript-lotto/pull/102#discussion_r815262983)
- `label`과 `input`을 `for`로 연결해줄 것 - [PR 102](https://github.com/woowacourse/javascript-lotto/pull/102#discussion_r815269072)
- `innerHTML`은 XSS 공격에 취약하고, 무겁고 비싸다. 대신 `createElement`나 `insertAdjacentHTML`를 사용할 수 있다. - [PR 100](https://github.com/woowacourse/javascript-lotto/pull/100#discussion_r815269744)

### CSS

- `display: none`은 영역에서 아예 사라지게 하는 스타일이므로 검색엔진이나 스크린리더에서 접근이 불가 - [PR 102](https://github.com/woowacourse/javascript-lotto/pull/102#discussion_r816348984)
  - `<h2>로또 섹션입니다~~</h2>`방식으로 제목을 넣어 숨기는 방식보다는 section을 쓰지 않는 방법도 있다.
- 기본 태그 (`input`, `p` 등)으로 스타일을 조정하면 서비스가 커질 때 사이드 이펙트를 발생시킬 확률이 높다. - [PR 109](https://github.com/woowacourse/javascript-lotto/pull/109#discussion_r813836290)
- user agent stylesheet를 고려한 [reset.css](https://abcdqbbq.tistory.com/9) - [PR 90](https://github.com/woowacourse/javascript-lotto/pull/90#discussion_r813975297)
- css를 HTML에 link를 통해 import할 경우 css의 rule이 전역에서 사용된다. 이는 사이드 이팩트를 일으킴. 이런 문제를 해결하기 위해 [css 모듈화](https://blog.toycrane.xyz/css%EC%9D%98-%EC%A7%84%ED%99%94-%EA%B3%BC%EC%A0%95-f7c9b4310ff7)가 진행 - [PR 94](https://github.com/woowacourse/javascript-lotto/pull/94#pullrequestreview-894384954)
- js에서 직접 스타일을 조정하지 않고 class를 달아주고 제거하는 방식을 택하자. - [PR 95](https://github.com/woowacourse/javascript-lotto/pull/95#discussion_r814778236)

<br />

### util

- 도메인에 속해있는 로직은 util로 분리하지 마세요. 범용적인 성격을 가진 아이들만 분리해주세요. - [PR 102](https://github.com/woowacourse/javascript-lotto/pull/102#discussion_r815270046)

<br />

### YAGNI!

- `You Aren't Gonna Need It` - 처음부터 확장성이나 미래를 심히 고려해 오버엔지니어링하는 걸 경계하자. - [PR 100](https://github.com/woowacourse/javascript-lotto/pull/100#pullrequestreview-894510935)
