# Level1 Youtube Step2(담당 PR 번호들) - 마르코

- 분석 담당 코드
  - 마르코 [#147](https://github.com/woowacourse/javascript-youtube-classroom/pull/147)
  - 위니 [#150](https://github.com/woowacourse/javascript-youtube-classroom/pull/150)
  - 병민 [#146](https://github.com/woowacourse/javascript-youtube-classroom/pull/146)
  - 해리 [#142](https://github.com/woowacourse/javascript-youtube-classroom/pull/142)
  - 코카콜라 [#134](https://github.com/woowacourse/javascript-youtube-classroom/pull/134)
  - 후이 [#148](https://github.com/woowacourse/javascript-youtube-classroom/pull/148)
  - 샐리 [#131](https://github.com/woowacourse/javascript-youtube-classroom/pull/131)

# 2. 피드백 정리

## 1. 함수

- [#150] for문을 some 메서드를 활용하여 리팩토링 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/150#discussion_r830994669)
    - [MDN - some](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/some)
```js
// [before] for문
for (let idx = 0; idx < this.#savedVideoItems.length; idx += 1) {
  if (this.#savedVideoItems[idx].videoId === deleteVideoId) {
    this.#savedVideoItems.splice(idx, 1);
    return;
  }
}
```

```js
// [after] some 메서드
this.#savedVideoItems.some((item, idx) => {
  if (item.videoId === deleteVideoId) {
    this.#savedVideoItems.splice(idx, 1);
  }
  return item.videoId === savedId;
});
```

## 2. 클래스와 메서드

- [#148] 클래스에 연관된 헬퍼함수, 템플릿, 상수 파일들의 분리 방법 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/148/files/1ba87826bed272037aac5446acbadcd4565ad3a3#r830620883)
  - 예들 들어, SearchModal 폴더를 만들고 하위에 SearchModal.js, SearchModal.helper.js, SearchModal.template.js, SearchModal.constants.js 이렇게 나누는것도 한 방법이다.

## 3. DOM

- [#146] attribute는 '고유속성'에 가까운 정적인 개념에 가깝다. 동적으로 변하는 '상태'에는 적합하지 않다.
  - https://ko.javascript.info/dom-attributes-and-properties#ref-1789
- [#178] 데이터 바인딩 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/148#discussion_r833264831)
  - [준찌 추천 링크](https://facebook.github.io/flux/docs/in-depth-overview/)  
  - 데이터 바인딩은 데이터와 뷰를 묶는다는 뜻이다. 웹어플리케이셥의 복잡도가 증가하면 뷰와 데이터를 일치시키기 어려워진다. 그래서 데이터와 뷰가 자동으로 일치하도록 묶어 두는 것이 데이터 바인딩이다.
  - 데이터 바인딩은 다시 `단방향`과 `양방향`으로 나뉜다.
    - 단반향 데이터 바인딩은 데이터가 변경되면 템플릿과 데이터를 합쳐 뷰를 만든다.
      - 대표적으로 React가 단방향 데이터 바인딩을 한다. 단방향 데이터 바인딩은 데이터와 템플릿을 결합해 화면을 생성하는 것이다. (JS -> HTML만 가능) 사용자의 입력에 따라 데이터를 갱신하고 화면을 업데이트 해야 하므로 단방향 데이터 바인딩으로 구성하면, 데이터의 변화를 감지하고 화면을 업데이트 하는 코드를 매번 작성해주어야 한다. 리액트는 자바스크립트 기반으로, 부모 뷰->자식 뷰 바뀐 내용을 직접 전달한다.  
      - 장점 : 데이터 변화에 따른 성능 저하 없이 DOM 객체 갱신 가능, 데이터 흐름이 단방향(부모->하위 컴포넌트)이라, 코드를 이해하기 쉽고 데이터 추적과 디버깅이 쉬움
      - 단점: 변화를 감지하고 화면을 업데이트 하는 코드를 매번 작성해야 함
    - 양방향 데이터바인딩은 뷰의 변경을 감지하여 데이터에 반영해주는 동시에, 데이터의 변경이 일어나면 템플릿과 데이터를 합쳐 뷰에 반영해준다.
      - 양방향 데이터 바인딩
        - 두 데이터 혹은 정보의 소스를 일치시키는 기법으로, 화면에 보이는 데이터와 브라우저 메모리에 있는 데이터(여러개의 자바스크립트 객체)를 일치시키는 것을 말한다. 예를 들어, MVC 모델에서 model과 view를 서로 묶어 model과 view의 "자동 동기화" 시키기 라고 이해할 수 있다. 
          - 컨트롤러에서 model이 변경됨 -> view변경됨, view에서 scope model이 변경됨 -> 컨트롤러에서 model이 변경됨
        - 장점 : 코드의 사용면에서 코드량을 크게 줄여줌
        - 단점 : 변화에 따라 DOM 객체 전체를 렌더링해주거나 데이터를 바꿔주므로, 성능이 감소되는 경우가 있음     
  - [편집하면서 참고한 블로그](https://blog.hyunmin.dev/15)

## 4. 이벤트

## 5. 클린코드

## 6. CSS, HTML

- [#147] css 네 방향 속성 관련
  - 예를 들어, `margin: 0 0 30px 0;` 과 `margin: 0 0 30px;`은 같다.
    - "위 오른쪽 아래 왼쪽" 에서 오른쪽과 왼쪽이 같으면 "위 오른쪽=왼쪽 아래 "처럼 마지막 왼쪽을 생략해도 된다.
    - 그리고 "위와 아래끼리", "오른쪽과 왼쪽끼리" 같은 경우 "위=아래 오른쪽=왼쪽" 처럼 두 개로 축약해도 된다.
- [#147] `CSS 의사 클래스`와 `CSS 의사요소`를 구분하자.
  - 브라우저에서 콜론 하나(:)는 `CSS 의사 클래스`에서 사용하고, 콜론 두 개(::)는 `CSS 의사 요소`에서 사용한다.
  - CSS3에 들어서면서, `CSS 의사 요소`와 `CSS 의사 클래스`를 구분하기 위해, 콜론 두 개인 `::before`과 같은 구문을 도입했다.
    - CSS3에서는 `::before`이고 CSS2에서는 원래 `:before`로 사용되었다.
  - 아직 브라우저에서는 CSS2 구문인 `:before`를 아직 허용한다고 하지만, before는 `의사 요소`이므로 `::before` 구문으로 사용하는 것이 권장된다.
  - 즉, CSS3 도입 취지에 부합하도록, `의사요소(:active, :hover 등)`와 `의사클래스(::before, ::after 등)`를 구분하여 콜론 개수를 달리 표시해야 한다.
  - [MDN- CSS 의사 클래스](https://developer.mozilla.org/ko/docs/Web/CSS/Pseudo-classes)
  - [MDN- CSS 의사 요소](https://developer.mozilla.org/ko/docs/Web/CSS/Pseudo-elements)

## 7. 테스트

- [#147] 웹스토리지나 API 자체를 Mocking하여 테스트하는 것에 의미가 없지 않다.
  - [질문] 제가 로컬스토리지를 mocking해서 테스트하려는 것은 "저장한 영상을 로컬스토리지에서 불러올 수 있어야 한다."인데, 이처럼 유닛테스트에서 웹스토리지나 API 자체를 Mocking해서 테스트하는 것에 어떤 의미가 있는 것인지 고민이 떠올랐어요. 결국 테스트를 위한 테스트가 될 것 같다는 생각이 들기도 해서요. 해당 테스트가 유닛테스트에서 불필요할 것 같아, 지울까 하는 고민이 드는데, 어떻게 생각하시나요?
  - [답변] 의미 없지 않습니다! 실제 메소드가 동작하는지 로컬스토리지에 데이터가 있는 상황, 없는 상황을 가정해서 테스트를 할 수 있거든요! 추후엔 오히려 더 테스트 케이스를 늘려서 보강하셔야합니다!

## 8. 설계

- [#147] 객체지향에서는 setter는 사용하지 않는 것이 권장된다.

  - 객체지향에서 말하길, setter와 getter에서 setter는 사용하지 않는걸 권장하고 있다. 그 이유는 객체지향은 객체간 협력을 나타내는 것인데, setter는 객체간 협력보다는 "의존" 혹은 "명령"이기 때문이다.
  - 예를 들어, A와 B가 모래성을 쌓고 있는데, 이들은 멋진 모래성을 쌓는게 목표이다. 그렇게 열심히 둘이 모래성을 쌓고 있는데, B가 실수로 A에게 "파괴하라!" 라고 `명령(set)`을 하게 된다면, A는 모래성을 파괴하게 된다.
    - 만약 setter를 안쓰고 `행동 단위`로 했다면 어땠을까? A와 B는 C라는 목표를 달성하기 위해 행동하는 객체이게 된다. C라는 목표를 모래성으로 설정하고. A와 B는 메소드로 PileUp() 이라는 메소드로 쌓을 수 있다. 즉, PileUp(C)를 통해 C를 서로가 쌓을 수 있게 된다. 여기서 C가 B로 바뀌던 A로 바뀌던, 결국엔 쌓기만 한다. `쌓는 행위` 외에 다른 것들은 할 수 없으니 비교적 명령보다 `안전`하다.
  - 따라서, setter와 같은 명령 대신에 행동 단위로 하는 것이 좋다.

- [#142] 객체지향적 관점에서 `has-a`와 `is-a` 차이점 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/142#discussion_r830583944)

  - is-a
    - is-a는 추상화(형식이나 클래스와 같은)들 사이의 포함 관계를 의미하며, 한 클래스 A가 다른 클래스 B의 서브클래스(파생클래스)임을 이야기합니다. 다른 말로, 타입 A는 타입 B의 명세(specification)를 암시한다는 점에서 타입 B의 서브타입이라고도 할 수 있습니다. is-a 관계는 타입 또는 클래스 간의 has-a 관계와는 대조됩니다. has-a 및 is-a 관계들 간의 혼동은 실세계 관계 모델에 대한 설계에 있어 자주 발견되는 에러입니다. is-a 관계는 또한 객체 또는 타입 간의 instance-of 관계와도 대조됩니다.
      - 예) 학생은 사람이다(o)    
      - 코드를 공통적으로 관리하기 때문에 코드의 추가 및 변경이 매우 용이하다.    
      - 조상클래스의 변경이 있으면 자손클래스는 영향을 주지만, 자손클래스가 변경되는 것은 조상클래스에 아무런 영향을 주지 않는다
      - 자손클래스의 인스턴스를 생성하면 조상클래스의 멤버와 자손 클래스의 멤버가 합쳐진 하나의 인스턴스로 생성된다
![image](https://user-images.githubusercontent.com/59413128/160842017-4e59add0-2e4f-4d03-8d1e-c4a567e8bf03.png)
  - has-a
    - has-a는 구성 관계를 의미하며 한 오브젝트(구성된 객체, 또는 부분/멤버 객체라고도 부릅니다)가 다른 오브젝트(composite type이라고 부릅니다)에 "속한다(belongs to)"를 말합니다. 단순히 말해, has-a 관계는 객체의 멤버 필드라고 불리는 객체를 말하며, Multiple has-a 관계는 소유 계층구조를 형성하기 위해 결합하는 경우를 말합니다.
      - 예) 학생은 책을 가지고있다(o) 
      - 한 클래스의 멤버변수로 다른 클래스를 선언하는 것
      - 작은 단위의 클래스를 먼저 만들고, 이 들을 조합해서 하나의 커다란 클래스를 만든다.
![image](https://user-images.githubusercontent.com/59413128/160842074-1885d659-f21b-441a-abc5-ed2e20c0dcb2.png)

- [#131] 아키텍처 맵(구조도) - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/131#issuecomment-1073797128)
  - 아키텍처 맵은 다음과 같이 두 가지로 방법으로 그려볼 수 있다.
    - 폴더 스트럭쳐: 폴더 스트럭쳐단위로, 서로의 폴더가 어떤걸 참조하고 있는지 그리는 방법
    - SoC: 관심사 단위로 어떤 것들이 어떻게 분리되고 어떻게 참조하고 있는지 그리는 방법
